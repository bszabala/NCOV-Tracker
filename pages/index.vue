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
      accessToken: process.env.MAPBOX_ACCESS_TOKEN
      // map: {}
    }
  },
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      MapboxGL.accessToken = this.accessToken

      var covidSource = 'https://coronavirus-tracker-api.herokuapp.com/v2/locations';
      var features = __fetchSource(covidSource);

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
          context.fillStyle = 'rgba(255, 100, 100, 1)'
          context.strokeStyle = 'white'
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

      map.on('load', function() {
        map.addImage('pulsing-dot', pulsingDot, { pixelRatio: 2 })

        map.addSource('points', {
          type: 'geojson',
          data: {
            type: 'FeatureCollection',
            // 'features': [
            //   {
            //     'type': 'Feature',
            //     'geometry': {
            //       'type': 'Point',
            //       'coordinates': [0, 0]
            //     }
            //   },
            //   {
            //     'type': 'Feature',
            //     'geometry': {
            //       'type': 'Point',
            //       'coordinates': [12.8797207, 121.7740173]
            //     }
            //   },
            // ]
            'features': features
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
      })

      function __fetchSource(source) {
        let data = [];

        axios.get(source)
          .then(function (response) {
            response.data.locations.forEach(function (location) {
              data.push({
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [location.coordinates.longitude, location.coordinates.latitude]
                }
              })
            })
          })
          .catch(function (error) {
            console.log(error)
          });

        console.log(data)

        return data;
      }
    }
  }
};
</script>

<style scoped>
#worldMap {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
</style>
