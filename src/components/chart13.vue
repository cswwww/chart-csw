<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2025-01-02 16:00:49
 * @FilePath: \chart-csw\src\components\chart13.vue
 * @Description: 台区覆冰厚度
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => ['N1', 'N2', 'N3', 'N4', 'N5', 'N6', 'N7', 'N8', 'N9', 'N10', 'N11', 'N12', 'N13', 'N14', 'N15'],
  },
  yAxis1: {
    type: Array,
    default: () => [900, 345, 393, '-', '-', 135, 178, 286, '-', '-', '-'],
  },
  yAxis2: {
    type: Array,
    default: () => ['-', '-', '-', 108, 154, '-', '-', '-', 119, 361, 203],
  },
  yName: {
    type: String,
    default: '覆冰厚度',
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  title: {
    text: '单位：mm',
    top: 0,
    right: 0,
    textStyle: {
      color: '#909399',
      fontSize: 12,
    },
  },
  legend: {
    data: ['装置监测点', '人工观冰点'], // 图例项
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow',
    },
    formatter(params) {
      let result = ''
      params.forEach((item) => {
        if (item.value !== '-') {
          result
            += `${item.marker} ${item.seriesName}: ${item.value}<br>`
        }
      })
      return result
    },
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true,
  },
  xAxis: {
    type: 'category',
    axisLabel: {
      color: '#000',
      interval: 0,
      textStyle: {
        fontSize: 12,
      },
      formatter: '{value}',
      // rotate: 90, // 旋转角度
    },
    data: props.xAxis,
  },
  yAxis: {
    name: '覆冰厚度',
    axisLabel: {
      formatter: '{value}',
      color: '#000',
    },
    axisLine: {
      show: false,
      lineStyle: {
        color: '#000',
      },
    },
    type: 'value',
  },
  series: [
    {
      name: '装置监测点',
      type: 'bar',
      color: '#7B7B7B',
      stack: 'Total',
      label: {
        show: true,
        position: 'top',
      },
      data: props.yAxis1,
    },
    {
      name: '人工观冰点',
      color: '#3199D0',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true,
        position: 'top',
      },
      data: props.yAxis2,
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
