<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { ElMessage } from 'element-plus'

interface Request {
  id: string;
  userName: string;
}

const requests = ref<Request[]>([])
const baseUrl = 'http://127.0.0.1:8578'

const loadRequests = async () => {
  const userInfo = window.localStorage.getItem('userInfo')
  const userId = userInfo ? JSON.parse(userInfo).id : null
  if (!userId) {
    ElMessage.error('用户信息未找到')
    return
  }
  try {
    const response = await axios.get(`${baseUrl}/friend/add/list/${userId}`)
    requests.value = response.data.data
  } catch (error) {
    ElMessage.error('加载好友请求失败')
    console.error(error)
  }
}

const handleRequest = async (id: string, isReceive: number) => {
  try {
    const response = await axios.post(`${baseUrl}/friend/add/list/handle`, {
      addFriendId: id,
      isReceive: isReceive
    })
    ElMessage.success(response.data.message)
    await loadRequests()
  } catch (error) {
    ElMessage.error('处理好友请求失败')
    console.error(error)
  }
}

onMounted(() => {
  loadRequests()
  // 每3秒刷新一次列表
  setInterval(loadRequests, 3000)
})
</script>

<template>
  <div class="request-list">
    <div class="list-header">好友请求 ({{ requests.length }})</div>
    <div class="list-content">
      <div v-for="request in requests" :key="request.id" class="request-item">
        <span class="name">{{ request.userName }}</span>
        <div class="buttons">
          <el-button type="success" round @click="handleRequest(request.id, 1)">
            同意
          </el-button>
          <el-button type="danger" round @click="handleRequest(request.id, 2)">
            拒绝
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>