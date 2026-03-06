<template>
  <el-card>
    <template #header>
      <span>举报记录管理</span>
    </template>
    <el-form :inline="true" :model="searchForm" class="search-form">
      <el-form-item label="举报ID">
        <el-input v-model="searchForm.id" placeholder="请输入举报ID" clearable />
      </el-form-item>
      <el-form-item label="举报人">
        <el-input v-model="searchForm.reporter" placeholder="请输入举报人昵称" clearable />
      </el-form-item>
      <el-form-item label="状态">
        <el-select v-model="searchForm.status" placeholder="请选择状态" clearable>
          <el-option label="待处理" value="pending" />
          <el-option label="处理中" value="processing" />
          <el-option label="已办结" value="completed" />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSearch">查询</el-button>
        <el-button @click="handleReset">重置</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="reportList" stripe style="width: 100%">
      <el-table-column prop="id" label="举报ID" width="80" />
      <el-table-column prop="reporter" label="举报人" />
      <el-table-column prop="target" label="被举报人" />
      <el-table-column prop="type" label="举报类型" width="120" />
      <el-table-column prop="content" label="举报内容" show-overflow-tooltip />
      <el-table-column prop="createTime" label="举报时间" width="180" />
      <el-table-column prop="status" label="状态" width="100">
        <template #default="scope">
          <el-tag :type="getStatusType(scope.row.status)">
            {{ getStatusText(scope.row.status) }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="180">
        <template #default="scope">
          <el-button size="small" @click="handleDetail(scope.row)">详情</el-button>
          <el-button size="small" type="primary" @click="handleProcess(scope.row)">处理</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :current-page="currentPage"
      :page-size="pageSize"
      :page-sizes="[10, 20, 50, 100]"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
      style="margin-top: 20px; text-align: right"
    />
  </el-card>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'

const searchForm = reactive({
  id: '',
  reporter: '',
  status: ''
})

const reportList = ref([
  { id: 1, reporter: '张三', target: '李四', type: '色情内容', content: '用户李四在群聊中发布色情图片', createTime: '2024-03-01 10:00:00', status: 'pending' },
  { id: 2, reporter: '王五', target: '赵六', type: '诈骗', content: '用户赵六诱导用户转账，涉嫌诈骗', createTime: '2024-03-02 14:30:00', status: 'processing' }
])

const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(124)

const getStatusType = (status: string) => {
  switch (status) {
    case 'pending': return 'warning'
    case 'processing': return 'primary'
    case 'completed': return 'success'
    default: return 'info'
  }
}

const getStatusText = (status: string) => {
  switch (status) {
    case 'pending': return '待处理'
    case 'processing': return '处理中'
    case 'completed': return '已办结'
    default: return '未知'
  }
}

const handleSearch = () => {
  console.log('搜索', searchForm)
}

const handleReset = () => {
  Object.keys(searchForm).forEach(key => {
    searchForm[key] = ''
  })
}

const handleDetail = (row: any) => {
  console.log('查看详情', row)
}

const handleProcess = (row: any) => {
  console.log('处理举报', row)
}
</script>