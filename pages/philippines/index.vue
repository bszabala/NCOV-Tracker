<template>
  <div>
    <div id="phMap"></div>
  </div>
</template>

<script>
import MapboxGL from 'mapbox-gl'

export default {
  data() {
    return {
      loading: false,
      accessToken: process.env.MAPBOX_ACCESS_TOKEN,
      sources: {
        philippines: {
          confirmed: { 
            api: process.env.API_COVID_PH_CONFIRMED_CASES,
            data: []
          },
          suspected: {
            api: process.env.API_COVID_PH_SUSPECTED_CASES,
            data: []
          },
          pui: {
            api: process.env.API_COVID_PH_PUI,
            data: []
          },
          checkpoints: {
            api: process.env.API_COVID_PH_CHECKPOINTS,
            data: []
          }
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
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      MapboxGL.accessToken = this.accessToken

      this.map = new MapboxGL.Map({
        container: 'phMap',
        style: 'mapbox://styles/mapbox/dark-v10',
        zoom: 5.4,
        center: [122.5644379, 10.6969404]
      })

      this.map.on('load', function() {})
    }
  }
};
</script>

<style scoped>
#phMap {
  width: 100%;
  height: 100vh;
}
</style>
