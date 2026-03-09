<template>
  <div class="bg">
    <div class="login-box">
      <el-form ref="formRef" :model="data.form" :rules="data.rules">
        <div class="title">违规违法举报平台管理端</div>
        
        <el-form-item prop="username">
          <el-input 
            size="large" 
            v-model="data.form.username" 
            autocomplete="off" 
            placeholder="请输入账号"
          >
            <template #prefix>
              <el-icon><User /></el-icon>
            </template>
          </el-input>
        </el-form-item>
        
        <el-form-item prop="password">
          <el-input 
            size="large" 
            :show-password="true" 
            v-model="data.form.password" 
            autocomplete="off" 
            placeholder="请输入密码"
          >
            <template #prefix>
              <el-icon><Lock /></el-icon>
            </template>
          </el-input>
        </el-form-item>
        
        <el-form-item prop="code">
          <el-input 
            size="large" 
            v-model="data.form.code" 
            autocomplete="off" 
            placeholder="请输入验证码"
            style="width: 60%"
          >
            <template #prefix>
              <el-icon><SFinance /></el-icon>
            </template>
          </el-input>
          <div style="width: 35%; float: right; height: 40px; line-height: 40px;">
            <!-- 使用占位符图片 -->
            <img 
              :src="captchaUrl"
              alt="验证码" 
              style="cursor: pointer; height: 100%; width: 100%; object-fit: cover;"
              @click="refreshCaptcha"
            />
          </div>
        </el-form-item>
        
        <div style="clear: both;"></div>
        
        <div>
          <el-button 
            style="width: 100%" 
            size="large" 
            type="primary" 
            @click="handleLogin"
            :loading="loading" 
          >
            {{ loading ? '登录中...' : '登 录' }}
          </el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import axios from 'axios';
import { useRouter } from 'vue-router';

const router = useRouter(); 
const formRef = ref();
const loading = ref(false);
// 验证码图片
const captchaUrl = ref('');

// 加载验证码
const loadCaptcha = async () => {
  try {
    const response = await axios.get('/api/admin/captcha');
    if (response.data && response.data.code === 1002) {
      captchaUrl.value = response.data.data;
    }
  } catch (error) {
    console.error('加载验证码失败:', error);
    // 加载失败时使用占位符
    captchaUrl.value = 'https://via.placeholder.com/120x40?text=验证码';
  }
};

// 初始化加载验证码
loadCaptcha();

const data = reactive({
  form: {
    username: '',
    password: '',
    code: ''
  },
  rules: {
    username: [
      { required: true, message: '请输入账号', trigger: 'blur' },
      { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
    ],
    password: [
      { required: true, message: '请输入密码', trigger: 'blur' }
    ],
    code: [
      { required: true, message: '请输入验证码', trigger: 'blur' },
      { min: 4, max: 4, message: '验证码长度为4个字符', trigger: 'blur' }
    ]
  }
});

const refreshCaptcha = () => {
  // 刷新验证码图片
  loadCaptcha();
};

const handleLogin = async () => {
  if (!formRef.value) return;

  try {
    // 第一步：表单验证
    await formRef.value.validate();

    // 第二步：开始加载
    loading.value = true;

    console.log('验证通过，正在请求后端...');
    // 连接后端服务器，使用 await 等待响应
    const result = await axios.post('/api/admin/login', {
      name: data.form.username,
      password: data.form.password,
      code: data.form.code
    }, {
      headers: {
        'Content-Type': 'application/json'
      }
    });

    console.log('后端响应:', result);

    // 第三步：根据后端响应判断登录状态
    if (result.data && result.data.code === 1002) {
      // 登录成功，跳转页面
      router.push("/home/dashboard");
    } else {
      // 登录失败，显示错误信息
      alert('登录失败：' + (result.data?.msg || '账号或密码错误'));
    }
    
  } catch (error) {
    console.log('验证失败或请求出错', error);
    // 显示错误信息
    alert('登录失败：网络错误或服务器异常');
  } finally {
    // 第四步：结束加载
    loading.value = false;
  }
};
</script>

<style scoped>
.bg {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  background-image: url("../assets/imgs/Login.jpg");
  background-size: cover;
  background-position: center;
}

.login-box {
  width: 350px;
  background-color: #f8ffff;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  padding: 40px 20px;
}

.title {
  margin-bottom: 40px;
  text-align: center;
  font-weight: bold;
  font-size: 24px;
  color: #333;
}
</style>