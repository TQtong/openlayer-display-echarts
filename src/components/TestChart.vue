<template>
  <div>
    <div ref="table" class="overlay-chart"></div>
  </div>
</template>

<script lang="ts" setup>
import * as echarts from 'echarts'
import { ref, onMounted } from 'vue'

const table = ref<any>()

onMounted(() => {
  const chart = echarts.init(table.value)
  chart.setOption(drawChart())
})

function drawChart () {
  const option = {
    title: {
      text: '全国/湖北疫情趋势'
    },
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['全国新增', '湖北新增']
    },
    xAxis: {
      type: 'category',
      boundaryGap: false
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '全国新增',
        type: 'line',
        data: [2590, 2829, 3235, 3887, 3694, 3151, 3399, 2653]
      },
      {
        name: '湖北新增',
        type: 'line',
        data: [
          1921, 2103, 2345, 3156, 2987, 2447, 2841, 2147, 2618, 2097, 1638,
          14840, 4823, 2420, 1843, 1933, 1807, 1693, 775, 631, 366, 630,
          398, 499, 401, 409, 318, 423, 570
        ]
      }
    ]
  }

  return option
}

</script>

<style lang="less" scoped>
.overlay-chart {
  width: 500px;
  height: 500px;
  background: #000;
}
</style>
