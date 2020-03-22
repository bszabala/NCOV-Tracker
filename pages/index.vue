<template>
  <div>
    <div id="worldMap"></div>
  </div>
</template>

<script>
import MapboxGL from 'mapbox-gl'
import axios from 'axios'

export default {
  data() {
    return {
      loading: false,
      accessToken: process.env.MAPBOX_ACCESS_TOKEN,
      sources: {
        world: { 
          api: process.env.API_COVID_WORLD_CASES,
          data: []
        }
      }
    }
  },
  asyncData () {
    return new Promise((resolve) => {
      setTimeout(function () {
        resolve({})
      }, 1000)
    })
  },
  mounted () {
    this.initMap()
  },
  fetch() {
    let data = [];
    axios.get(this.sources.world.api)
      .then(function(response) {
        response.data.locations.forEach(function(location) {
          data.push({
            type: 'Feature',
            properties: {
              id: location.id,
              description: `
                <div>
                  <small>${location.province ? location.province+', ' : ''}${location.country}</small><br/>
                  <small>Cases:</small> <strong>${location.latest.confirmed}</strong><br/>
                  <small>Deaths:</small> <strong>${location.latest.deaths}</strong><br/>
                  <small>Recovered:</small> <strong>${location.latest.recovered}</strong>
                </div>
              `
            },
            geometry: {
              type: 'Point',
              coordinates: [
                location.coordinates.longitude,
                location.coordinates.latitude
              ]
            }
          })
        })
      })
      .catch(function(error) {
        console.log(error)
      })

    return this.sources.world.data = data;
  },
  methods: {
    initMap() {
      console.info(this.sources.world.data)
      MapboxGL.accessToken = this.accessToken

      var map = new MapboxGL.Map({
        container: 'worldMap',
        style: 'mapbox://styles/mapbox/dark-v10',
        zoom: 1.3,
        center: [0, 0]
      })

      var size = 100

      // implementation of CustomLayerInterface to draw a pulsing dot icon on the map
      // see https://docs.mapbox.com/mapbox-gl-js/api/#customlayerinterface for more info
      var pulsingDot = {
        width: size,
        height: size,
        data: new Uint8Array(size * size * 4),

        // get rendering context for the map canvas when layer is added to the map
        onAdd: function() {
          var canvas = document.createElement('canvas')
          canvas.width = this.width
          canvas.height = this.height
          this.context = canvas.getContext('2d')
        },

        // called once before every frame where the icon will be used
        render: function() {
          var duration = 1000
          var t = (performance.now() % duration) / duration

          var radius = (size / 2) * 0.3
          var outerRadius = (size / 2) * 0.7 * t + radius
          var context = this.context

          // draw outer circle
          context.clearRect(0, 0, this.width, this.height)
          context.beginPath()
          context.arc(
            this.width / 2,
            this.height / 2,
            outerRadius,
            0,
            Math.PI * 2
          )
          context.fillStyle = 'rgba(255, 200, 200,' + (1 - t) + ')'
          context.fill()

          // draw inner circle
          context.beginPath()
          context.arc(this.width / 2, this.height / 2, radius, 0, Math.PI * 2)
          context.fillStyle = 'rgba(220, 20, 60, 1)'
          context.strokeStyle = '#FFC0CB'
          context.lineWidth = 2 + 4 * (1 - t)
          context.fill()
          context.stroke()

          // update this image's data with data from the canvas
          this.data = context.getImageData(0, 0, this.width, this.height).data

          // continuously repaint the map, resulting in the smooth animation of the dot
          map.triggerRepaint()

          // return `true` to let the map know that the image was updated
          return true
        }
      }

      map.on('load', () => {
        map.addImage('pulsing-dot', pulsingDot, { pixelRatio: 2 })

        map.addSource('points', {
          type: 'geojson',
          data: {
            type: 'FeatureCollection',
            features: this.sources.world.data
          }
        })

        map.addLayer({
          id: 'points',
          type: 'symbol',
          source: 'points',
          layout: {
            'icon-image': 'pulsing-dot'
          }
        })

        // When a click event occurs on a feature in the places layer, open a popup at the
        // location of the feature, with description HTML from its properties.
        map.on('click', 'points', function(e) {
          var coordinates = e.features[0].geometry.coordinates.slice()
          var description = e.features[0].properties.description

          // Ensure that if the map is zoomed out such that multiple
          // copies of the feature are visible, the popup appears
          // over the copy being pointed to.
          // while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
          //   coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360
          // }

          new MapboxGL.Popup()
            .setLngLat(coordinates)
            .setHTML(description)
            .addTo(map)
        })

        // Change the cursor to a pointer when the mouse is over the places layer.
        map.on('mouseenter', 'points', function() {
          map.getCanvas().style.cursor = 'pointer'
        })

        // Change it back to a pointer when it leaves.
        map.on('mouseleave', 'points', function() {
          map.getCanvas().style.cursor = ''
        })
      })

    }
  }
};
</script>

<style scoped>
#worldMap {
  height: calc(100vh - 64px);
  height: -moz-calc(100vh - 64px);
  height: -webkit-calc(100vh - 64px);
  width: 100%;
}
</style>
