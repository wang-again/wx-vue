<template>
  <!-- 路由出口：所有页面都会渲染到这里 -->
  <router-view />
</template>

<script setup lang="ts">
// 该文件无需任何脚本逻辑，所有布局在 Layout.vue 中实现
</script>

<style scoped>
/* 全局基础样式，消除默认边距 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>