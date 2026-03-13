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
          <el-form-item>
            <el-button type="primary" @click="openChangePasswordDialog" style="width: 100%">修改密码</el-button>
          </el-form-item>
        </el-form>
      </div>
    </el-drawer>
    
    <!-- 修改密码对话框 -->
    <el-dialog
      v-model="changePasswordDialogVisible"
      title="修改密码"
      width="400px"
    >
      <el-form :model="changePasswordForm" :rules="changePasswordRules" ref="changePasswordFormRef" label-width="80px">
        <el-form-item label="旧密码" prop="oldPassword">
          <el-input v-model="changePasswordForm.oldPassword" type="password" show-password />
        </el-form-item>
        <el-form-item label="新密码" prop="newPassword">
          <el-input v-model="changePasswordForm.newPassword" type="password" show-password />
        </el-form-item>
        <el-form-item label="确认密码" prop="confirmPassword">
          <el-input v-model="changePasswordForm.confirmPassword" type="password" show-password />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="changePasswordDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleChangePassword" :loading="changePasswordLoading">确定</el-button>
      </template>
    </el-dialog>
  </el-container>
</template>

<script setup lang="ts">
import { useRoute, useRouter } from 'vue-router'
import { ref, reactive } from 'vue'
import axios from 'axios'
import { ElMessage } from 'element-plus'
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

// 修改密码相关
const changePasswordDialogVisible = ref(false)
const changePasswordFormRef = ref()
const changePasswordLoading = ref(false)

const changePasswordForm = reactive({
  oldPassword: '',
  newPassword: '',
  confirmPassword: ''
})

const validateConfirmPassword = (rule: any, value: any, callback: any) => {
  if (value !== changePasswordForm.newPassword) {
    callback(new Error('两次输入的密码不一致'))
  } else {
    callback()
  }
}

const changePasswordRules = {
  oldPassword: [
    { required: true, message: '请输入旧密码', trigger: 'blur' }
  ],
  newPassword: [
    { required: true, message: '请输入新密码', trigger: 'blur' },
    { min: 6, message: '密码长度至少6位', trigger: 'blur' }
  ],
  confirmPassword: [
    { required: true, message: '请确认新密码', trigger: 'blur' },
    { validator: validateConfirmPassword, trigger: 'blur' }
  ]
}

const openChangePasswordDialog = () => {
  changePasswordDialogVisible.value = true
  changePasswordForm.oldPassword = ''
  changePasswordForm.newPassword = ''
  changePasswordForm.confirmPassword = ''
}

const handleChangePassword = async () => {
  if (!changePasswordFormRef.value) return
  
  try {
    await changePasswordFormRef.value.validate()
    changePasswordLoading.value = true
    
    const result = await axios.post('/api/admin/update-passwords', {
      oldPassword: changePasswordForm.oldPassword,
      newPassword: changePasswordForm.newPassword
    })
    
    if (result.data && result.data.code === 1002) {
      ElMessage.success('密码修改成功')
      changePasswordDialogVisible.value = false
      // 更新本地显示的密码
      adminInfo.uPwd = changePasswordForm.newPassword
    } else {
      ElMessage.error(result.data?.msg || '密码修改失败')
    }
  } catch (error) {
    console.error('修改密码失败:', error)
    ElMessage.error('密码修改失败，请稍后重试')
  } finally {
    changePasswordLoading.value = false
  }
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