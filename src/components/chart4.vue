<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-13 16:16:24
 * @FilePath: \chart-csw\src\components\chart4.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => [
      '00:00',
      '01:15',
      '02:30',
      '03:45',
      '05:00',
      '06:15',
      '07:30',
      '08:45',
      '10:00',
      '11:15',
      '12:30',
      '13:45',
      '15:00',
      '16:15',
      '17:30',
      '18:45',
      '20:00',
      '21:15',
      '22:30',
      '23:45',
    ],
  },
  yAxis: {
    type: Array,
    default: () => [300, 280, 250, 260, 270, 300, 550, 500, 400, 390, 380, 390, 400, 500, 600, 750, 800, 700, 600],
  },
  title: {
    type: String,
    default: '告警水位',
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  // title: {
  //   text: '告警细节数据',
  //   textStyle: {
  //     color: '#eff6ff'
  //   }
  // },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross',
    },
  },
  xAxis: {
    type: 'category',
    boundaryGap: false,
    data: props.xAxis,
    axisLabel: {
      color: '#eff6ff', // set x-axis label color to white
    },
  },
  yAxis: {
    type: 'value',
    axisPointer: {
      snap: true,
    },
    axisLabel: {
      color: '#eff6ff', // set y-axis label color to white
    },
  },
  series: [
    {
      name: props.title,
      type: 'line',
      smooth: true,
      data: props.yAxis,
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
