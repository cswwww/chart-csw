<!--
 * @Date: 2024-02-27 11:41:18
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-13 16:21:55
 * @FilePath: \chart-csw\src\components\customChart.vue
 * @Description: 通用图表组件
-->

<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  option: Object,
})

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

// 初始化图表
function initChart() {
  if (!chartDom.value)
    return

  // 校验 Dom 节点上是否已经挂载了 ECharts 实例，只有未挂载时才初始化
  chartInstance.value = echarts.getInstanceByDom(chartDom.value)

  if (!chartInstance.value) {
    chartInstance.value = echarts.init(chartDom.value)
    chartInstance.value.setOption(props.option)
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
