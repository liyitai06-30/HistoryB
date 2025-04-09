<template>
  <div class="chart-container">
    <div class="section">
      <h2 class="chart-title">北京历史名胜词云</h2>
      <div ref="wordCloud" class="chart-item"></div>
    </div>

    <div class="section">
      <h2 class="chart-title">北京市历史名胜及遗址占比</h2>
      <div ref="pieChart" class="chart-item"></div>
    </div>

    <div class="section">
      <h2 class="chart-title">不同时期历史名胜数量统计</h2>
      <div ref="barChart" class="chart-item"></div>
    </div>

    <div class="section">
      <h2 class="chart-title">北京市历年历史名胜及遗址申报趋势（2000-2024）</h2>
      <div ref="lineChart" class="chart-item"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import * as echarts from 'echarts'
import 'echarts-wordcloud'

const wordCloud = ref(null)
const pieChart = ref(null)
const barChart = ref(null)
const lineChart = ref(null)
let charts = []

onMounted(() => {
  initWordCloud()
  initPieChart()
  initBarChart()
  initLineChart()
})

onBeforeUnmount(() => {
  charts.forEach(chart => chart.dispose())
})

const initWordCloud = () => {
  const data = [
    { name: "故宫", value: 120 },
    { name: "长城", value: 100 },
    { name: "天坛", value: 90 },
    { name: "颐和园", value: 85 },
    { name: "圆明园", value: 80 },
    { name: "雍和宫", value: 75 },
    { name: "钟鼓楼", value: 70 },
    { name: "前门大街", value: 65 },
    { name: "北海公园", value: 60 },
    { name: "南锣鼓巷", value: 55 },
    { name: "雍和宫", value: 53 },
    { name: "正阳门", value: 50 },
    { name: "卢沟桥", value: 47 },
    { name: "国子监", value: 45 },
    { name: "东交民巷", value: 42 }
  ]

  const chart = echarts.init(wordCloud.value)
  charts.push(chart)

  chart.setOption({
    tooltip: {},
    series: [{
      type: 'wordCloud',
      shape: 'circle',
      sizeRange: [18, 70],
      rotationRange: [-45, 90],
      gridSize: 8,
      drawOutOfBound: false,
      textStyle: {
        color: () => `hsl(${Math.random() * 360}, 70%, 50%)`
      },
      data
    }]
  })
}

const initPieChart = () => {
  const data = [
    { value: 3, name: '古建筑' },
    { value: 2, name: '历史名胜' },
    { value: 2, name: '考古遗址' },
    { value: 1, name: '传统聚落' },
    { value: 1, name: '历史街区' }
  ]

  const chart = echarts.init(pieChart.value)
  charts.push(chart)

  chart.setOption({
    tooltip: { trigger: 'item' },
    legend: { top: 'bottom' },
    series: [{
      name: '历史名胜及遗址分类',
      type: 'pie',
      radius: '55%',
      data,
      label: { formatter: '{b}: {d}%' },
      emphasis: { itemStyle: { shadowBlur: 10, shadowColor: 'rgba(0, 0, 0, 0.5)' } }
    }]
  })
}

const initBarChart = () => {
  const periods = ['元代', '明代', '清代', '民国', '新中国初期', '改革开放后', '21世纪初', '当代']
  const values = [3, 6, 7, 4, 5, 2, 1, 6]

  const chart = echarts.init(barChart.value)
  charts.push(chart)

  chart.setOption({
    xAxis: { type: 'category', data: periods },
    yAxis: { type: 'value' },
    series: [{
      type: 'bar',
      data: values,
      itemStyle: {
        color: (params) => {
          const colors = ['#5470C6', '#91CC75', '#EE6666', '#FAC858', '#73C0DE', '#3BA272', '#FC8452', '#9A60B4']
          return colors[params.dataIndex]
        },
        borderRadius: [4, 4, 0, 0]
      },
      label: { show: true, position: 'top' }
    }]
  })
}

const initLineChart = () => {
  const years = Array.from({ length: 25 }, (_, i) => 2000 + i)
  const values = years.map((y, i) => Math.floor(1 + i * 0.4 + Math.random() * 1.5)) // 稍微浮动的增长

  const chart = echarts.init(lineChart.value)
  charts.push(chart)

  chart.setOption({
    xAxis: { type: 'category', data: years },
    yAxis: { type: 'value' },
    series: [{
      type: 'line',
      data: values,
      smooth: true,
      areaStyle: { color: 'rgba(84, 112, 198, 0.2)' },
      symbol: 'circle',
      symbolSize: 8,
      lineStyle: { width: 3 },
      itemStyle: { color: '#5470C6' }
    }]
  })
}
</script>

<style scoped>
.chart-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 32px;
  padding: 40px;
  background: linear-gradient(to bottom right, #f9f9f9, #e6f0fa);
  min-height: 100vh;
}

.section {
  background: white;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
}

.chart-title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 15px;
  text-align: center;
  color: #333;
  font-family: "Helvetica Neue", sans-serif;
}

.chart-item {
  height: 360px;
}
</style>
