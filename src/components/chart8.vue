<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwww@163.com
 * @LastEditTime: 2024-02-22 19:06:46
 * @FilePath: \satellite-web\src\components\charts\pieChart.vue
 * @Description: 饼图
 * @Ref: https://juejin.cn/post/7245183742264377401
-->
<script setup>
import * as echarts from 'echarts'

const colors1 = ['#63b2ee', '#7cd6cf', '#7898e1', '#9987ce', '#63b2ee']

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartData = [
  { value: 1048, name: '未处理' },
  { value: 735, name: '已处理' },
  { value: 580, name: '误告警' },
]

const chartOptions = {
  title: {
    text: '告警处理结果',
    bottom: 0,
    // subtext: '',
    textStyle: {
      color: '#eff6ff',
    },
    left: 'center',
  },
  tooltip: {
    trigger: 'item',
  },
  legend: {
    orient: 'horizontal',
    left: 'center',
    // bottom: 'bottom',
    top: 0,

    textStyle: {
      lineHeight: 16, // 为了让图例换行对齐
      backgroundColor: 'transparent', // 为了让图例换行对齐
      color: '#eff6ff',
    },
  },
  series: [
    {
      type: 'pie',
      radius: '80%',
      color: colors1,
      data: chartData,
      label: {
        show: true,
        position: 'inner',
        // formatter: '{d}%',
        color: '#000c29',
        fontSize: 15,
      },
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)',
        },
      },
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
