<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2025-02-18 15:51:34
 * @FilePath: \chart-csw\src\components\chart15.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器
const data = [[19.9, 0.285], [5, 14], [2, 1]]

const chartOptions = {
  tooltip: {},
  axisPointer: {
    show: true,
  },
  xAxis: {
    name: '时间戳',
  },
  yAxis: {
    name: '幅值',
  },
  dataZoom: [
    {
      type: 'slider',
    },
    {
      type: 'slider',
      orient: 'vertical',
    },
  ],
  series: [{
    type: 'line', // scatter
    smooth: true,
    name: '数据',
    emphasis: {
      itemStyle: {
        color: '#fff',
      },
    },
    data,
  }],
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
  // 监听点击事件
  chartInstance.value.getZr().on('click', (e) => {
    // 获取点击位置的像素坐标
    let offsetX = e.offsetX
    let offsetY = e.offsetY

    // 将像素坐标转换为逻辑坐标
    let pointInGrid = chartInstance.value.convertFromPixel({ seriesIndex: 0 }, [offsetX, offsetY])

    // 输出点击位置的逻辑坐标
    console.log('点击位置的逻辑坐标：', pointInGrid)
  })
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
