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
</template>

<script setup lang="ts">
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
</style>