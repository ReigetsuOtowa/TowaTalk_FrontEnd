<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { ElMessage } from 'element-plus'

const strangers = ref([])
const baseUrl = 'http://127.0.0.1:8578'

const loadStrangers = async () => {
  const userId = JSON.parse(localStorage.getItem('userInfo')).id
  try {
    const response = await axios.get(`${baseUrl}/friend/list/un/${userId}`)
    strangers.value = response.data.data
  } catch (error) {
    ElMessage.error('加载陌生人列表失败')
    console.error(error)
  }
}

const addFriend = async (sendUserId: string, receiveUserId: string) => {
  try {
    const response = await axios.post(`${baseUrl}/friend/add`, {
      sendUserId,
      receiveUserId,
      message: '你好啊'
    })
    ElMessage.success(response.data.message)
  } catch (error) {
    ElMessage.error('添加好友失败')
    console.error(error)
  }
}

onMounted(() => {
  loadStrangers()
})
</script>

<template>
  <div class="stranger-list">
    <div class="list-header">陌生人列表 ({{ strangers.length }})</div>
    <div class="list-content">
      <div v-for="stranger in strangers" :key="stranger.id" class="stranger-item">
        <span class="name">{{ stranger.userName }}</span>
        <el-button 
          type="warning" 
          round 
          @click="addFriend(JSON.parse(localStorage.getItem('userInfo')).id, stranger.id)">
          添加好友
        </el-button>
      </div>
    </div>
  </div>
</template>