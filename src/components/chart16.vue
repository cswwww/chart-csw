<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX cswwwx@gmail.com
 * @LastEditTime: 2025-03-25 17:19:43
 * @FilePath: /chart-csw/src/components/chart16.vue
 * @Description: 散点图 - 波形图专用
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'
import { data } from '../data/data.js'

defineProps({
  chartData: {
    type: Array,
    default: () => [],
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = markRaw({
  xAxis: {},
  yAxis: {},
  dataZoom: [
    {
      type: 'slider',
    },
    {
      type: 'slider',
      orient: 'vertical',
    },
  ],
  series: [],
})

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

// 获取数据
async function fetchData() {
  data.forEach((item) => {
    const serieItem = {
      symbolSize: 4,
      data: [],
      type: 'scatter',
    }
    item.times.forEach((ele, index) => {
      serieItem.data.push([item.times[index], item.values[index]])
    })
    chartOptions.series.push(serieItem)
  })

  initChart()
}

onMounted(() => {
  window.addEventListener('resize', initChart)

  fetchData()
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
