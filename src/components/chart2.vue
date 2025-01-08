<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-10-08 09:04:28
 * @FilePath: \zhuhai-web\src\components\charts\chart2.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => ['2024年6月', '2024年7月', '2024年8月', '2024年9月'],
  },
  yAxis1: {
    type: Array,
    default: () => [2, 5, 3, 9],
  },
  yAxis2: {
    type: Array,
    default: () => [2, 5, 3, 2],
  },
  yAxis3: {
    type: Array,
    default: () => [11, 18, 23, 41],
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
    top: '10%',
    right: '0%',
    left: '10%',
    bottom: '20%',
  },
  title: {
    text: '单位：处',
    top: 5,
    right: 5,
    textStyle: {
      color: '#909399',
      fontWeight: 'normal',
      fontSize: 12,
    },
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow',
    },
  },
  legend: {
    top: 'bottom',
    textStyle: {
      color: '#e2e9ff',
    },
  },
  xAxis: {
    type: 'category',
    data: props.xAxis,

    axisLabel: {
      color: '#eff6ff',
      interval: 0,
    },
  },
  yAxis: {
    type: 'value',
    splitLine: {
      lineStyle: {
        color: 'rgba(255,255,255,0.12)',
      },
    },
    axisLabel: {
      color: '#eff6ff',
    },
  },
  series: [
    {
      data: props.yAxis3,
      barWidth: '20px',
      itemStyle: {
        normal: {
          color: '#4E31AA',
        },
      },
      type: 'bar',
      stack: 'a',
      name: '已处理',
    },
    {
      data: props.yAxis2,
      barWidth: '20px',
      itemStyle: {
        normal: {
          color: '#47D468',
        },
      },
      type: 'bar',
      stack: 'a',
      name: '监控中',
    },
    {
      data: props.yAxis1,
      barWidth: '20px',
      itemStyle: {
        normal: {
          color: '#03B8BD',
        },
      },
      type: 'bar',
      stack: 'a',
      name: '待处理',
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
