<template>
  <div class="hello">
    <div id="mapDiv"></div>
    <div id="map" ref="mapRef"></div>
    地图容器
    <div id="chart" ref="chart"></div>
    监测点的柱状图容器（ 写完这篇博文之后发现了一个小问题，
    <div id="chart" ref="chart"></div>
    该div会在页面上占位置，会出现在地图容器下方，页面会有滚动条，解决办法就是给该div加一个display：none就OK了，并不会影响柱状图的定位和显示）
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted } from 'vue'
import 'ol/ol.css'
import { Map, View } from 'ol'
import OSM from 'ol/source/OSM'
import Overlay from 'ol/Overlay'
import Feature from 'ol/Feature'
import Point from 'ol/geom/Point'
import { Vector as VectorS, XYZ, ImageArcGISRest, TileArcGISRest } from 'ol/source'
import { Tile as TileLayer, Image as ImageLayer, Vector as VectorLayer } from 'ol/layer'
import { Style, Icon } from 'ol/style'
import { fromLonLat, toLonLat, proj } from 'ol/proj'
import { CENTER_CENTER } from 'ol/OverlayPositioning'
import { Sphere } from 'ol/sphere.js'
import * as echart from 'echarts'

export default defineComponent({
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup () {
    const dataColumn = [
      {
        name: ['监测点1'],
        x: 109.302221,
        y: 27.787397,
        value: [3]
      },
      {
        name: ['监测点2'],
        x: 109.069956,
        y: 27.757735,
        value: [8]
      }, {
        name: ['监测点3'],
        x: 109.162517,
        y: 27.671774,
        value: [5]
      }
    ]
    const chart = echart
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

    function guid () { // 为了生成不一样的id，实现每个装柱状图的盒子的唯一性
      let d = new Date().getTime()
      const guid = 'xxxx-xxxx-xxxx-xxxx'.replace(
        /[xy]/g,
        function (c) {
          const r = (d + Math.random() * 16) % 16 | 0
          d = Math.floor(d / 16)
          return (c == 'x' ? r : (r & 0x7 | 0x8)).toString(16)
        })
      return guid
    }

    function addColumnChart () { // 向点位添加柱状图的方法
      let html = ''
      for (let i = 0; i < dataColumn.length; i++) {
        // 1、循环每一条数据，生成id不同的div，
        // 2、获取到该div，将柱状图添加上去，
        // 3、 new Overlay，将每个柱状图添加到对应的点位上去
        const d = dataColumn[i]
        const pt = fromLonLat([d.x, d.y])
        const domid = 'chart' + guid() // 生成不同的id
        html += "<div id='" + domid + "' style='width: 35px;height: 100px; margin-left: -18px;margin-bottom: -22px;'></div>"
        chart.innerHTML = html // chart为HTML里的柱状图容器，
        // 创建树状图
        let option = {}
        const myChart = echart.init(document.getElementById(domid))

        option = {
          color: ['#c23531'],
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            }
          },
          xAxis: {
            name: '监测点名称',
            data: d.name,
            show: false
          },
          yAxis: {
            max: '10',
            name: '浓度单位（mg/kg）',
            show: false
          },
          series: [{
            name: '汞浓度(mg/kg)',
            type: 'bar',
            data: d.value
          }]
        }
        myChart.setOption(option)
        // 将柱状图添加到指定点位上去
        var chart = new Overlay({
          id: domid,
          positioning: 'bottom-center',
          element: document.getElementById(domid),
          offset: [0, 5],
          stopEvent: false // overlay也支持滚珠放大缩小
        })
        map.addOverlay(chart)
        // map是在mounted里new Map出来的，按openlayer官网操作即可，
        chart.setPosition(pt)
      }
    }
  }

})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
