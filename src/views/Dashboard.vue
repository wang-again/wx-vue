<template>
  <div>
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
  </div>
</template>

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

// 生成指定月份的完整日期数据
const generateMonthData = (month: number): { dates: string[], values: number[] } => {
  const dates: string[] = []
  const values: number[] = []
  
  // 计算月份的天数
  let daysInMonth: number
  if (month === 2) {
    // 检查是否为闰年
    const year = new Date().getFullYear()
    daysInMonth = (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0 ? 29 : 28
  } else if ([4, 6, 9, 11].includes(month)) {
    daysInMonth = 30
  } else {
    daysInMonth = 31
  }
  
  // 生成日期和对应的值
  for (let day = 1; day <= daysInMonth; day++) {
    dates.push(`${month}.${day}`)
    // 生成随机但有趋势的值
    const baseValue = 100 + (month - 1) * 10
    const randomVariation = Math.floor(Math.random() * 50) - 25
    const trend = Math.sin(day / daysInMonth * Math.PI * 2) * 30
    values.push(Math.max(50, Math.floor(baseValue + randomVariation + trend)))
  }
  
  return { dates, values }
}

// 生成所有月份的数据
const monthData: Record<string, { dates: string[], values: number[] }> = {
  '1': generateMonthData(1),
  '2': generateMonthData(2),
  '3': generateMonthData(3),
  '4': generateMonthData(4),
  '5': generateMonthData(5),
  '6': generateMonthData(6),
  '7': generateMonthData(7),
  '8': generateMonthData(8),
  '9': generateMonthData(9),
  '10': generateMonthData(10),
  '11': generateMonthData(11),
  '12': generateMonthData(12)
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