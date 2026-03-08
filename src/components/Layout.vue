<template>
  <el-container style="height: 100vh">
    <el-aside width="220px" style="background-color: #2f4050">
      <div style="height: 60px; line-height: 60px; text-align: center; color: white; font-size: 18px; font-weight: bold">
        违法违规举报平台
      </div>
      <el-menu
        :default-active="route.path"
        class="el-menu-vertical-demo"
        background-color="#2f4050"
        text-color="#bfcbd9"
        active-text-color="#409eff"
        router
      >
        <el-menu-item index="/home/dashboard">
          <el-icon><House /></el-icon>
          <span>首页</span>
        </el-menu-item>
        <el-menu-item index="/home/users">
          <el-icon><User /></el-icon>
          <span>用户信息</span>
        </el-menu-item>
        <el-menu-item index="/home/reports">
          <el-icon><Document /></el-icon>
          <span>举报记录</span>
        </el-menu-item>
        <el-menu-item index="/home/feedback">
          <el-icon><ChatLineRound /></el-icon>
          <span>举报反馈</span>
        </el-menu-item>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header style="background-color: #fff; border-bottom: 1px solid #e6e6e6; display: flex; justify-content: flex-end; align-items: center">
        <el-dropdown>
          <span class="user-info">
            <el-avatar size="small" :src="'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png'"></el-avatar>
            <span style="margin-left: 8px">管理员</span>
          </span>
          <template #dropdown>
            <el-dropdown-menu>
              <el-dropdown-item>个人中心</el-dropdown-item>
              <el-dropdown-item>退出登录</el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>
      </el-header>
      <el-main style="background-color: #f3f3f4">
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router'
const route = useRoute()
</script>

<style scoped>
.user-info {
  display: flex;
  align-items: center;
  cursor: pointer;
}
</style>