<template>
  <el-card>
    <template #header>
      <span>举报反馈管理</span>
    </template>
    <el-form :inline="true" :model="searchForm" class="search-form">
      <el-form-item label="反馈ID">
        <el-input v-model="searchForm.id" placeholder="请输入反馈ID" clearable />
      </el-form-item>
      <el-form-item label="举报ID">
        <el-input v-model="searchForm.reportId" placeholder="请输入举报ID" clearable />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="handleSearch">查询</el-button>
        <el-button @click="handleReset">重置</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="feedbackList" stripe style="width: 100%">
      <el-table-column prop="id" label="反馈ID" width="80" />
      <el-table-column prop="reportId" label="举报ID" width="100" />
      <el-table-column prop="content" label="反馈内容" show-overflow-tooltip />
      <el-table-column prop="createTime" label="反馈时间" width="180" />
      <el-table-column label="操作" width="120">
        <template #default="scope">
          <el-button size="small" @click="handleView(scope.row)">查看</el-button>
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
  reportId: ''
})

const feedbackList = ref([])

const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(112)

const handleSearch = () => {
  console.log('搜索', searchForm)
}

const handleReset = () => {
  Object.keys(searchForm).forEach(key => {
    searchForm[key] = ''
  })
}

const handleView = (row: any) => {
  console.log('查看反馈', row)
}
</script>