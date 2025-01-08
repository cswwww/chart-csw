<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-13 11:07:18
 * @FilePath: \zhongshan-web\src\components\charts\line-chart2.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => ['1', '2', '3', '4', '5', '6', '7'],
  },
  yAxis: {
    type: Array,
    default: () => [
      Math.sin(0),
      Math.sin(1 * Math.PI / 3),
      Math.sin(2 * Math.PI / 3),
      Math.sin(Math.PI),
      Math.sin(4 * Math.PI / 3),
      Math.sin(5 * Math.PI / 3),
      Math.sin(2 * Math.PI),
    ],
  },
  title: {
    type: String,
    default: '告警水位',
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  grid: {
    top: '15%',
    right: '0%',
    left: '5%',
    bottom: '5%',
  },
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
    show: false, // 隐藏 x 轴
    axisLabel: {
      color: '#eff6ff', // set x-axis label color to white
    },
  },
  yAxis: {
    type: 'value',
    splitLine: {
      show: false, // 隐藏 y 轴的分割线
    },
    axisLine: {
      show: true, // 显示 y 轴的轴线
    },
    min: -1, // 设置 y 轴的最小值
    max: 1, // 设置 y 轴的最大值
    interval: 1, // 设置 y 轴的刻度间隔
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
