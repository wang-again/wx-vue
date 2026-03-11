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
              <el-dropdown-item @click="openUserCenter">个人中心</el-dropdown-item>
              <el-dropdown-item @click="handleLogout">退出登录</el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>
      </el-header>
      <el-main style="background-color: #f3f3f4">
        <router-view />
      </el-main>
    </el-container>
    
    <!-- 个人中心侧边弹窗 -->
    <el-drawer
      v-model="drawerVisible"
      title="个人中心"
      direction="rtl"
      size="300px"
    >
      <div class="user-center-content">
        <div class="user-avatar">
          <el-avatar size="80" :src="'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png'"></el-avatar>
        </div>
        <h3 style="text-align: center; margin-top: 20px">管理员信息</h3>
        <el-form :model="adminInfo" label-width="80px" style="margin-top: 30px">
          <el-form-item label="账户名称">
            <el-input v-model="adminInfo.uName" disabled />
          </el-form-item>
          <el-form-item label="密码">
            <el-input v-model="adminInfo.uPwd" type="password" disabled />
          </el-form-item>
        </el-form>
      </div>
    </el-drawer>
  </el-container>
</template>

<script setup lang="ts">
import { useRoute, useRouter } from 'vue-router'
import { ref, reactive } from 'vue'
const route = useRoute()
const router = useRouter()

// 侧边弹窗状态
const drawerVisible = ref(false)

// 管理员信息
const adminInfo = reactive({
  uName: 'admin',
  uPwd: '123456' // 实际应用中应该从后端获取
})

// 打开个人中心
const openUserCenter = () => {
  drawerVisible.value = true
}

// 退出登录
const handleLogout = () => {
  // 清除登录状态（实际应用中可能需要清除token等）
  // 导航到登录页面
  router.push('/login')
}
</script>

<style scoped>
.user-info {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.user-center-content {
  padding: 20px;
}

.user-avatar {
  display: flex;
  justify-content: center;
}
</style>