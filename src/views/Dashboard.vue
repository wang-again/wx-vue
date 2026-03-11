<template>
  <el-card>
    <template #header>
      <span>数据概览</span>
    </template>
    <el-row :gutter="20">
      <el-col :span="6">
        <div class="stat-card" style="background-color: #1ab394; color: white">
          <h3>总用户数</h3>
          <h2>1,286</h2>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="stat-card" style="background-color: #23c6c8; color: white">
          <h3>今日举报</h3>
          <h2>24</h2>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="stat-card" style="background-color: #f8ac59; color: white">
          <h3>待处理</h3>
          <h2>12</h2>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="stat-card" style="background-color: #ed5565; color: white">
          <h3>已办结</h3>
          <h2>1,124</h2>
        </div>
      </el-col>
    </el-row>
  </el-card>
  >
  <el-card style="margin-top: 20px">
    <template #header>
      <div class="header-content">
        <span>用户提交量趋势</span>
        <el-select v-model="selectedMonth" @change="handleMonthChange" style="width: 120px; margin-left: 20px">
          <el-option
            v-for="month in months"
            :key="month.value"
            :label="month.label"
            :value="month.value"
          />
        </el-select>
      </div>
    </template>
    <div ref="chartRef" class="chart-container"></div>
  </el-card>
</>

<script setup lang="ts">
import { onMounted, ref, onUnmounted } from 'vue'
import * as echarts from 'echarts'

const chartRef = ref<HTMLElement | null>(null)
let chartInstance: echarts.ECharts | null = null
const selectedMonth = ref('3')

const months = [
  { label: '1月', value: '1' },
  { label: '2月', value: '2' },
  { label: '3月', value: '3' },
  { label: '4月', value: '4' },
  { label: '5月', value: '5' },
  { label: '6月', value: '6' },
  { label: '7月', value: '7' },
  { label: '8月', value: '8' },
  { label: '9月', value: '9' },
  { label: '10月', value: '10' },
  { label: '11月', value: '11' },
  { label: '12月', value: '12' }
]

const monthData: Record<string, { dates: string[], values: number[] }> = {
  '1': {
    dates: ['1.1', '1.2', '1.3', '1.4', '1.5', '1.6', '1.7'],
    values: [85, 92, 78, 110, 95, 125, 105]
  },
  '2': {
    dates: ['2.1', '2.2', '2.3', '2.4', '2.5', '2.6', '2.7'],
    values: [98, 115, 88, 122, 108, 135, 118]
  },
  '3': {
    dates: ['3.4', '3.5', '3.6', '3.7', '3.8', '3.9', '3.10'],
    values: [120, 132, 101, 134, 90, 230, 210]
  },
  '4': {
    dates: ['4.1', '4.2', '4.3', '4.4', '4.5', '4.6', '4.7'],
    values: [145, 158, 132, 160, 125, 175, 155]
  },
  '5': {
    dates: ['5.1', '5.2', '5.3', '5.4', '5.5', '5.6', '5.7'],
    values: [168, 182, 155, 190, 148, 205, 188]
  },
  '6': {
    dates: ['6.1', '6.2', '6.3', '6.4', '6.5', '6.6', '6.7'],
    values: [185, 198, 172, 205, 165, 220, 202]
  },
  '7': {
    dates: ['7.1', '7.2', '7.3', '7.4', '7.5', '7.6', '7.7'],
    values: [210, 225, 195, 235, 188, 250, 228]
  },
  '8': {
    dates: ['8.1', '8.2', '8.3', '8.4', '8.5', '8.6', '8.7'],
    values: [220, 235, 205, 245, 198, 260, 238]
  },
  '9': {
    dates: ['9.1', '9.2', '9.3', '9.4', '9.5', '9.6', '9.7'],
    values: [195, 208, 180, 218, 175, 230, 212]
  },
  '10': {
    dates: ['10.1', '10.2', '10.3', '10.4', '10.5', '10.6', '10.7'],
    values: [175, 188, 160, 195, 155, 210, 192]
  },
  '11': {
    dates: ['11.1', '11.2', '11.3', '11.4', '11.5', '11.6', '11.7'],
    values: [155, 168, 140, 175, 135, 190, 172]
  },
  '12': {
    dates: ['12.1', '12.2', '12.3', '12.4', '12.5', '12.6', '12.7'],
    values: [180, 195, 165, 205, 158, 225, 208]
  }
}

const updateChart = (month: string) => {
  if (!chartInstance) return
  
  const data = monthData[month]
  if (data) {
    chartInstance.setOption({
      xAxis: {
        data: data.dates
      },
      series: [
        {
          data: data.values
        }
      ]
    })
  }
}

const handleMonthChange = (value: string) => {
  updateChart(value)
}

onMounted(() => {
  if (chartRef.value) {
    chartInstance = echarts.init(chartRef.value)
    
    const option = {
      tooltip: {
        trigger: 'axis'
      },
      grid: {
        left: '3%',
        right: '4%',
        bottom: '3%',
        containLabel: true
      },
      xAxis: {
        type: 'category',
        boundaryGap: false,
        data: monthData[selectedMonth.value]!.dates
      },
      yAxis: {
        type: 'value',
        name: '提交量'
      },
      series: [
        {
          name: '用户提交量',
          type: 'line',
          smooth: true,
          symbol: 'circle',
          symbolSize: 8,
          data: monthData[selectedMonth.value]!.values,
          areaStyle: {
            color: {
              type: 'linear',
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              colorStops: [
                { offset: 0, color: 'rgba(26, 179, 148, 0.5)' },
                { offset: 1, color: 'rgba(26, 179, 148, 0.1)' }
              ]
            }
          },
          itemStyle: {
            color: '#1ab394'
          },
          lineStyle: {
            color: '#1ab394',
            width: 3
          }
        }
      ]
    }
    
    chartInstance.setOption(option)
    
    window.addEventListener('resize', handleResize)
  }
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  if (chartInstance) {
    chartInstance.dispose()
  }
})

const handleResize = () => {
  if (chartInstance) {
    chartInstance.resize()
  }
}
</script>

<style scoped>
.stat-card {
  padding: 20px;
  border-radius: 4px;
  text-align: center;
}
.stat-card h3 {
  margin: 0 0 10px 0;
  font-size: 16px;
}
.stat-card h2 {
  margin: 0;
  font-size: 28px;
}
.chart-container {
  width: 100%;
  height: 400px;
}
.header-content {
  display: flex;
  align-items: center;
}
</style>