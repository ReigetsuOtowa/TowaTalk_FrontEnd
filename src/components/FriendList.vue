<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { ElMessage } from 'element-plus'

const friends = ref([])
const baseUrl = 'http://127.0.0.1:8578'

const loadFriends = async () => {
  const userId = JSON.parse(localStorage.getItem('userInfo')).id
  try {
    const response = await axios.get(`${baseUrl}/friend/list/${userId}`)
    friends.value = response.data.data
  } catch (error) {
    ElMessage.error('加载好友列表失败')
    console.error(error)
  }
}

const chatWith = (id: string, username: string) => {
  // TODO: 实现聊天功能
}

onMounted(() => {
  loadFriends()
  // 每3秒刷新一次列表
  setInterval(loadFriends, 3000)
})
</script>

<template>
  <div class="friend-list">
    <div class="list-header">好友列表 ({{ friends.length }})</div>
    <div class="list-content">
      <div v-for="friend in friends" 
           :key="friend.id" 
           class="friend-item"
           @click="chatWith(friend.id, friend.userName)">
        <img :src="'../static/img/dog.png'" alt="avatar" class="avatar"/>
        <div class="info">
          <span class="name">{{ friend.nickName }}</span>
          <span class="time">1:44 PM</span>
        </div>
      </div>
    </div>
  </div>
</template>