<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwww@163.com
 * @LastEditTime: 2024-02-27 10:18:37
 * @FilePath: \satellite-web\src\components\charts\timeLineChart.vue
 * @Description: 时间轴折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=area-time-axis
-->
<script setup>
import * as echarts from 'echarts'

let base = +new Date(1988, 9, 3)
const oneDay = 24 * 3600 * 1000
const data = [[base, Math.random() * 300]]
for (let i = 1; i < 20000; i++) {
  const now = new Date((base += oneDay))
  data.push([+now, Math.round((Math.random() - 0.5) * 20 + data[i - 1][1])])
}

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  tooltip: {
    trigger: 'axis',
    position(pt) {
      return [pt[0], '10%']
    },
  },
  xAxis: {
    type: 'time',
    boundaryGap: false,
  },
  yAxis: {
    type: 'value',
    boundaryGap: [0, '100%'],
  },
  dataZoom: [
    {
      type: 'inside',
      start: 0,
      end: 20,
    },
    {
      start: 0,
      end: 20,
    },
  ],
  series: [
    {
      name: 'Fake Data',
      type: 'line',
      smooth: true,
      symbol: 'none',
      areaStyle: {},
      data,
    },
  ],
}
// 初始化图表
function initChart() {
  if (!chartDom.value)
    return

  // 校验 Dom 节点上是否已经挂载了 ECharts 实例，只有未挂载时才初始化
  chartInstance.value = echarts.getInstanceByDom(chartDom.value)

  if (!chartInstance.value) {
    chartInstance.value = echarts.init(chartDom.value)
    chartInstance.value.setOption(chartOptions)
  }
  else {
    chartInstance.value.resize()
  }
}

onMounted(() => {
  initChart()
  window.addEventListener('resize', initChart)
})

onBeforeUnmount(() => {
  // 容器被销毁之后，销毁实例，避免内存泄漏
  chartInstance.value?.dispose()
  window.removeEventListener('resize', initChart)
})
</script>

<template>
  <div ref="chartDom" class="h-full w-full" />
</template>
