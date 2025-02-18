<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2025-02-18 15:43:44
 * @FilePath: \chart-csw\src\components\chart1.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => ['500kV线路', '220kV线路', '110kV线路', '35kV线路'],
  },
  yAxis: {
    type: Array,
    default: () => [32, 96, 150, 8],
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  grid: {
    top: '15%',
    right: '0%',
    left: '10%',
    bottom: '10%',
  },
  title: {
    text: '单位：处',
    top: 5,
    right: 5,
    textStyle: {
      color: '#909399',
      fontSize: 12,
    },
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow',
    },
  },
  xAxis: [
    {
      type: 'category',
      data: props.xAxis,
      axisLine: {
        lineStyle: {
          color: 'rgba(255,255,255,0.12)',
        },
      },
      axisLabel: {
        color: '#e2e9ff',
        interval: 0,
        fontSize: 12,
      },
    },
  ],
  yAxis: [
    {
      // name: '单位：处',
      axisLabel: {
        formatter: '{value}',
        color: '#e2e9ff',
      },
      axisLine: {
        show: false,
        lineStyle: {
          color: 'rgba(255,255,255,1)',
        },
      },
      splitLine: {
        lineStyle: {
          color: 'rgba(255,255,255,0.12)',
        },
      },
    },
  ],
  series: [
    {
      type: 'bar',
      data: props.yAxis,
      barWidth: '20px',
      itemStyle: {
        color: new echarts.graphic.LinearGradient(
          0,
          0,
          0,
          1,
          [
            {
              offset: 0,
              color: 'rgba(0,244,255,1)', // 0% 处的颜色
            },
            {
              offset: 1,
              color: 'rgba(0,77,167,1)', // 100% 处的颜色
            },
          ],
          false,
        ),
        borderRadius: [30, 30, 0, 0],
        shadowColor: 'rgba(0,160,221,1)',
        shadowBlur: 4,
      },
      label: {
        show: true,
        position: 'top',
        color: '#e2e9ff',
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
