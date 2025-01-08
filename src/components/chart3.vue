<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-13 16:24:03
 * @FilePath: \chart-csw\src\components\chart3.vue
 * @Description: 带装饰环的圆环饼图
 * @Ref: https://echarts.apache.org/examples/zh/editor.html?c=line-sections
-->
<script setup>
import * as echarts from 'echarts'

const props = defineProps({
  chartData: {
    type: Array,
    default: () => [
      { value: 1048, name: '星巡' },
      { value: 735, name: '机巡' },
      { value: 580, name: '视频' },
      { value: 484, name: '监测' },
    ],
  },
})
const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const BigRadius = 96
const normalRadius = 82
const tinyRadius = 40

const chartOptions = markRaw({
  tooltip: {
    trigger: 'item',
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
  legend: {
    type: 'scroll',
    orient: 'vertical',
    // icon: 'path://M480 64C250.24 64 64 250.24 64 480 64 709.76 250.24 896 480 896c229.76 0 416-186.24 416-416C896 250.24 709.76 64 480 64zM480 832C285.44 832 128 674.56 128 480 128 285.44 285.44 128 480 128 674.56 128 832 285.44 832 480 832 674.56 674.56 832 480 832z',
    icon: 'path://M512 960C264.576 960 64 759.424 64 512S264.576 64 512 64s448 200.576 448 448-200.576 448-448 448z m0-268.8a179.2 179.2 0 1 0 0-358.4 179.2 179.2 0 0 0 0 358.4z',
    right: 0,
    top: 'center',
    // bottom: 10,
    itemGap: 15, // 图例每项之间的间隔。
    formatter: (name) => {
      // 重点在这里  自定义图例
      const obj = props.chartData.find(item => item.name === name)
      if (name.length > 12) {
        name = `${name.slice(0, 12)}...`
      }
      obj.value = obj.value === null ? '--' : obj.value
      obj.value = obj.value === undefined ? '--' : obj.value
      return [`{a|${name}}`, `{b|${obj.value}}`].join('')
    },
    textStyle: {
      width: 90, // 每个图例的宽度，为了实现数字右对齐
      rich: {
        overflow: 'truncate',
        ellipsis: '...',
        a: {
          fontSize: 12,
          width: 60,
          color: '#32e4fe', // ['#4E31AA', '#47D468', '#03B8BD', '#4283FE'],
          overflow: 'truncate',
          ellipsis: '...',
        },
        b: {
          fontSize: 18,
          color: '#32e4fe', // ['#4E31AA', '#47D468', '#03B8BD', '#4283FE'],
          fontWeight: '500',
          align: 'right',
        },
      },
    },
  },
  series: [
    // 外装饰圆环
    {
      name: name || '',
      type: 'pie',
      silent: true,
      label: {
        normal: {
          show: false,
        },
      },
      radius: [normalRadius, BigRadius], // 半径
      center: ['30%', '55%'], // 位置
      data: [],
      emptyCircleStyle: {
        color: '#051c4580',
      },
    },
    // 内主体圆环
    {
      name: name || '',
      type: 'pie',
      radius: [tinyRadius, normalRadius], // 半径
      center: ['30%', '55%'], // 位置
      color: ['#4E31AA', '#47D468', '#03B8BD', '#4283FE'],
      label: {
        show: true,
        position: 'inner',
        // formatter: '{d}%',
        color: '#fff',
        fontSize: 13,
      },
      labelLayout: {
        hideOverlap: false,
      },
      data: props.chartData,
      itemStyle: {
        emphasis: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)',
        },
      },
    },
  ],
})

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
