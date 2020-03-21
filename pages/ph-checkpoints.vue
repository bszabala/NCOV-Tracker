<template>
  <div>
    <div id="phCheckpointMap"></div>
  </div>
</template>

<script>
import MapboxGL from 'mapbox-gl'

export default {
  data() {
    return {
      accessToken: process.env.MAPBOX_ACCESS_TOKEN,
      map: {}
    }
  },
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      MapboxGL.accessToken = this.accessToken

      this.map = new MapboxGL.Map({
        container: 'phCheckpointMap',
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
#phCheckpointMap {
  width: 100%;
  height: 100vh;
}
</style>
