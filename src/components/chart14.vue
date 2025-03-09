  <!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2025-02-25 15:56:27
 * @FilePath: \chart-csw\src\components\chart14.vue
 * @Description: 简易折线图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  xAxis: {
    type: Array,
    default: () => [
      '2024-12-27 10:07:00',
      '2024-12-27 11:07:00',
      '2024-12-27 12:07:00',
      '2024-12-27 13:07:00',
      '2024-12-27 14:07:00',
      '2024-12-27 15:07:00',
      '2024-12-27 16:07:00',
      '2024-12-27 17:07:00',
      '2024-12-27 18:07:00',
      '2024-12-27 19:07:00',
      '2024-12-27 20:07:00',
      '2024-12-27 21:07:00',
      '2024-12-27 22:07:00',
      '2024-12-27 23:07:00',
      '2024-12-28 00:07:00',
      '2024-12-28 01:07:00',
      '2024-12-28 02:07:00',
      '2024-12-28 03:07:00',
      '2024-12-28 04:07:00',
      '2024-12-28 05:07:00',
      '2024-12-28 06:07:00',
      '2024-12-28 07:07:00',
      '2024-12-28 08:07:00',
      '2024-12-28 09:07:00',
    ],
  },
  yAxis: {
    type: Array,
    default: () => [0.1, 0.5, 1, 2, 5],
  },
  forecastYAxis: {
    type: Array,
    default: () => [5, 4, 3, 2, 1],
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const chartOptions = {
  color: [], // '#5470C6', '#616566', '#5470C6', '#616566'
  grid: {
    left: '5%',
    right: '4%',
    bottom: '3%',
    top: '15%',
    containLabel: true,
  },
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
        if (value === '-') {
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
  legend: {
    data: [], // 'A相', '地线', '预测：A相', '预测：地线'
    itemWidth: 30, // 图例标记的图形宽度。
    icon: '',
    //   itemGap: 20, // 图例每项之间的间隔。
    itemHeight: 10, //  图例标记的图形高度。
  },
  xAxis: {
    type: 'category',
    axisTick: {
      alignWithLabel: true,
    },
    // boundaryGap: false,
    data: props.xAxis,
    axisLabel: {
      color: '#000', // set x-axis label color to white
    },
  },
  yAxis: {
    type: 'value',
    max: 1.4,
    min: 0,
    interval: 0.2,
    name: '覆冰比值',
    position: 'left',
    axisPointer: {
      snap: true,
    },
    axisLabel: {
      formatter: '{value}',
      color: '#000',
    },
  },
  series: [],
}

// 初始化图表
function initChart() {
  if (!chartDom.value)
    return

  // 校验 Dom 节点上是否已经挂载了 ECharts 实例，只有未挂载时才初始化
  chartInstance.value = echarts.getInstanceByDom(chartDom.value)

  const colorMap = {
    A相: '#5470C6',
    B相: '#91CC75',
    C相: '#FAC858',
    地线: '#616566',
    光缆: '#213381',
  }

  if (!chartInstance.value) {
    chartInstance.value = echarts.init(chartDom.value)
    props.yAxis.forEach((item) => {
      const name = `${item.chartName}覆冰比值`
      chartOptions.series.push({
        name,
        type: 'line',
        smooth: true,
        data: item.iceRatio,
      })

      chartOptions.legend.data.push(name)
      chartOptions.color.push(colorMap[item.chartName])
    })

    props.forecastYAxis?.forEach((item) => {
      const name = `${item.chartName}覆冰比值预测`
      chartOptions.series.push({
        name,
        type: 'line',
        smooth: true,
        data: item.iceRatio,
        symbol: 'none',
        lineStyle: {
          type: 'dashed',
        },
        markArea: {
          data: [
            [
              {
                yAxis: '0', // 开始
                itemStyle: {
                  color: '#E9FCE9', // 绿色
                  // borderWidth: 1,
                  // borderType: 'dashed',
                  opacity: 1,
                  emphasis: { // 重点：禁用高亮时的透明度变化
                    opacity: 1, // 保持与原透明度一致
                  },
                },
              },
              {
                yAxis: '0.5', // 结束
              },
            ],
            [
              {
                yAxis: '0.5', // 开始
                itemStyle: {
                  color: '#FFDACC',
                  // borderWidth: 1,
                  // borderType: 'dashed',
                  opacity: 1,
                  emphasis: { // 重点：禁用高亮时的透明度变化
                    opacity: 1, // 保持与原透明度一致
                  },
                },
              },
              {
                yAxis: '0.7', // 结束
              },
            ],
            [
              {
                yAxis: '0.7', // 开始
                itemStyle: {
                  color: '#FFCCCC',
                  // borderWidth: 1,
                  // borderType: 'dashed',
                  opacity: 1,
                  emphasis: { // 重点：禁用高亮时的透明度变化
                    opacity: 1, // 保持与原透明度一致
                    color: '#FFCCCC',
                  },
                },
              },
              {
                yAxis: '2', // 结束
              },
            ],
            //  如果有多种颜色，就继续在这里写区间数组，复制上面的下来改颜色
          ],
          itemStyle: {
            opacity: 1,
            emphasis: {
              opacity: 1, // 保持透明度不变
            },
          },
          // 可选：阻止触发任何交互事件（如Tooltip）
          silent: true,
        },
        markLine: {
          silent: true,
          lineStyle: {
            color: '#333',
          },
          data: [
            {
              yAxis: 0.7,
            },
          ],
        },

      })
      chartOptions.legend.data.push(name)
      chartOptions.color.push(colorMap[item.chartName])
    })
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
