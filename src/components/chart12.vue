<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-19 14:04:01
 * @FilePath: \chart-csw\src\components\chart12.vue
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
  yAxis1: {
    type: Array,
    default: () => [2.0, 4.9, 7.0, 23.2, 25.6, 26.7, 15.6, 12.2, 22.6, 20.0, 6.4, 3.3],
  },
  yAxis2: {
    type: Array,
    default: () => [5.0, 4.2, 3.3, 2.5, 1.3, 1.2, 2.3, 2.4, 2.0, 6.5, 5.0, 6.2],
  },
  yAxis3: {
    type: Array,
    default: () => ['-', '-', '-', '-', '-', '-', '-', '-', '-', '-', '-', '3.3', '1.5', '2.5', '3.2', '4.1', '3.2', '5.5', '1.5', '1.5'],
  },
  yAxis4: {
    type: Array,
    default: () => ['-', '-', '-', '-', '-', '-', '-', '-', '-', '-', '-', '6.2', '7.5', '5.5', '3.2', '2.1', '4.2', '5.1', '1.2', '2.5'],
  },
  title: {
    type: String,
    default: '告警水位',
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  color: ['#0099FF', '#FF9900'],
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross',
    },
    formatter: (params) => {
      let htmlStr = ''
      const valMap = {}
      for (let i = 0; i < params.length; i++) {
        const param = params[i]
        const xName = param.name // x轴的名称
        const seriesName = param.seriesName // 图例名称
        const value = param.value // y轴值
        const color = param.color // 图例颜色
        // 过滤无效值
        if ((!value) || value === '-') {
          continue
        }
        // 过滤重叠值
        if (valMap[seriesName] === value) {
          continue
        }
        if (i === 0) {
          htmlStr += `${xName}<br/>` // x轴的名称
        }
        htmlStr += '<div>'
        htmlStr += `<span style="margin-right:5px;display:inline-block;width:10px;height:10px;border-radius:5px;background-color:${color};"></span>`
        htmlStr += `${seriesName}：${value}` // 圆点后面显示的文本
        htmlStr += '</div>'
        valMap[seriesName] = value
      }
      return htmlStr
    },
  },
  grid: {
    right: '20%',
    bottom: '10%',
    left: '20%',
  },
  // toolbox: {
  //   feature: {
  //     dataView: { show: true, readOnly: false },
  //     restore: { show: true },
  //     saveAsImage: { show: true },
  //   },
  // },
  legend: {
    show: true,
    data: ['覆冰厚度', '温度'],
    top: '0%',
    itemWidth: 30, // 图例标记的图形宽度。
    //   itemGap: 20, // 图例每项之间的间隔。
    itemHeight: 10, //  图例标记的图形高度。
    textStyle: {
      color: '#aed2ff',
      fontSize: 14,
      padding: [0, 8, 0, 8],
    },
  },
  xAxis: [
    {
      type: 'category',
      axisTick: {
        alignWithLabel: true,
      },
      // boundaryGap: false,
      data: props.xAxis,
      axisLabel: {
        color: '#aed2ff',
      },
    },
  ],
  yAxis: [
    {
      type: 'value',
      name: '覆冰厚度',
      position: 'left',
      alignTicks: true,
      axisTick: {
        show: false,
      },
      axisLine: {
        show: true,
        lineStyle: {
          color: '#0099FF',
        },
      },
      splitLine: {
        show: true,
        lineStyle: {
          color: '#1160a0',
          type: 'dashed',
        },
      },
      axisLabel: {
        formatter: '{value} mm',
      },
    },
    {
      type: 'value',
      name: '温度',
      position: 'right',
      alignTicks: true,
      axisTick: {
        show: false,
      },
      axisLine: {
        show: true,
        lineStyle: {
          color: '#FF9900',
        },
      },
      splitLine: {
        show: true,
        lineStyle: {
          color: '#1160a0',
          type: 'dashed',
        },
      },
      axisLabel: {
        formatter: '{value} °C',
      },
    },
  ],
  series: [
    {
      name: '覆冰厚度',
      type: 'line',
      symbol: 'circle',
      yAxisIndex: 0,
      data: props.yAxis1,
    },
    {
      name: '温度',
      type: 'line',
      symbol: 'circle',
      yAxisIndex: 1,
      data: props.yAxis2,
    },
    {
      name: '预测：覆冰厚度',
      type: 'line',
      yAxisIndex: 0,
      data: props.yAxis3,
      symbol: 'none',
      lineStyle: {
        type: 'dashed',
      },
    },
    {
      name: '预测：温度',
      type: 'line',
      yAxisIndex: 1,
      data: props.yAxis4,
      symbol: 'none',
      lineStyle: {
        type: 'dashed',
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
