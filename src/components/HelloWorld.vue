<template>
  <div class="hello">
    <div id="mapDiv"></div>
    <div id="map" ref="mapRef">
      <TestChart></TestChart>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import TestChart from './TestChart.vue'
import 'ol/ol.css'
import { Map, View } from 'ol'
import OSM from 'ol/source/OSM'
import Overlay from 'ol/Overlay'
import { Tile as TileLayer } from 'ol/layer'

const mapRef = ref()

onMounted(() => {
  const map = new Map({
    target: 'mapDiv',
    layers: [
      new TileLayer({
        source: new OSM()
      })
    ],
    view: new View({
      center: [0, 0],
      zoom: 0
    })
  })

  const overlay = new Overlay({
    element: mapRef.value,
    stopEvent: false,
    position: [0, 0],
    positioning: 'center-left',
    className: 'point-overlay'
  })
  map.addOverlay(overlay)
})

</script>

<style scoped lang="less">
.hello {
  width: 100%;
  height: 100%;
  #mapDiv {
    width: 100%;
    height: 100%;
  }
}
</style>
