<!--
 * @Date: 2022-08-08 10:26:39
 * @LastEditors: ReBeX  cswwwx@gmail.com
 * @LastEditTime: 2024-12-13 16:23:24
 * @FilePath: \chart-csw\src\components\chart5.vue
 * @Description: 带装饰环的圆环饼图
 * @Ref: https://juejin.cn/post/7245183742264377401
-->
<script setup>
import * as echarts from 'echarts'

const chartInstance = shallowRef(null) // 图表实例
const chartDom = ref(null) // 图表容器

const colors1 = ['#63b2ee', '#7cd6cf', '#7898e1', '#9987ce', '#63b2ee']

const chartData = [
  { value: '20', name: '在线' },
  { value: '50', name: '离线' },
]
const BigRadius = '100%'
const normalRadius = '90%'
const tinyRadius = '50%'

const chartOptions = {
  tooltip: {
    trigger: 'item',
  },
  // title: {
  //   text: '地钉设备状态',
  //   bottom: 30,
  //   // subtext: '单位：个',
  //   textStyle: {
  //     color: '#eff6ff'
  //   },
  //   left: 'center'
  // },

  legend: {
    orient: 'verticalAlign',
    icon: 'path://M512 960C264.576 960 64 759.424 64 512S264.576 64 512 64s448 200.576 448 448-200.576 448-448 448z m0-268.8a179.2 179.2 0 1 0 0-358.4 179.2 179.2 0 0 0 0 358.4z',
    left: 'right',
    // width: 380,
    bottom: '50%',
    // itemGap: 10, // 图例每项之间的间隔。
    formatter: (name) => {
      // 重点在这里  自定义图例
      const obj = chartData.find(item => item.name === name)
      if (name.length > 12) {
        name = `${name.slice(0, 12)}...`
      }
      obj.value = obj.value === null ? '--' : obj.value
      return [`{a|${name}}`, `{b|${obj.value}\u00A0\u00A0\u00A0\u00A0\u00A0` + `}`].join(
        '',
      )
    },
    textStyle: {
      width: 120, // 每个图例的宽度，具体根据字数调整
      lineHeight: 16, // 为了让图例换行对齐
      backgroundColor: 'transparent', // 为了让图例换行对齐
      rich: {
        overflow: 'truncate',
        ellipsis: '...',
        a: {
          fontSize: 12,
          color: '#eff6ff',
          overflow: 'truncate',
          ellipsis: '...',
        },
        b: {
          fontSize: 16,
          color: '#eff6ff',
          fontWeight: '500',
          align: 'right',
        },
      },
    },
  },
  series: [
    // 外装饰圆环
    {
      type: 'pie',
      silent: true,
      label: {
        show: false,
      },
      radius: [normalRadius, BigRadius], // 半径
      left: 'left', // 位置
      right: '180',
      emptyCircleStyle: {
        color: '#3d6dbc',
      },
    },
    // 内主体圆环
    {
      type: 'pie',
      radius: [tinyRadius, normalRadius], // 半径
      left: 'left', // 位置
      right: '180',
      color: colors1,
      label: {
        show: true,
        position: 'inner',
        // formatter: '{d}%',
        color: '#000c29',
        // fontSize: 10
      },
      data: chartData,
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
