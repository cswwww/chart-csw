<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwww@163.com
 * @LastEditTime: 2024-02-23 13:54:09
 * @FilePath: \satellite-web\src\components\charts\stackBar.vue
 * @Description: 堆叠的柱图
 * @Ref: https://juejin.cn/post/7245183742264377401
-->
<script setup>
import * as echarts from 'echarts'

const colors1 = [
  '#63b2ee',
  '#7cd6cf',
  '#7898e1',
  '#9987ce',
  '#63b2ee',
]

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  title: {},
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow',
    },
  },
  legend: {
    textStyle: {
      color: '#eff6ff',
    },
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true,
  },
  xAxis: [
    {
      type: 'category',
      axisLabel: {
        color: 'white',
      },
      data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'],
    },
  ],
  yAxis: [
    {
      type: 'value',
      axisLabel: {
        color: 'white',
      },
    },
  ],
  series: [
    {
      name: '未处理',
      type: 'bar',
      stack: 'Ad',
      color: colors1[0],
      emphasis: {
        focus: 'series',
      },
      data: [120, 132, 101, 134, 90, 230, 210],
    },
    {
      name: '已处理',
      type: 'bar',
      color: colors1[1],
      stack: 'Ad',
      emphasis: {
        focus: 'series',
      },
      data: [220, 182, 191, 234, 290, 330, 310],
    },
    {
      name: '误告警',
      type: 'bar',
      color: colors1[2],
      stack: 'Ad',
      emphasis: {
        focus: 'series',
      },
      data: [150, 232, 201, 154, 190, 330, 410],
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
