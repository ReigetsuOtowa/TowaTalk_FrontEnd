<script setup>
import ProfileIcon from './components/icons/IconProfile.vue'
import ChatIcon from './components/icons/IconMessage.vue'
import { ref, onMounted } from 'vue'

const isLoggedIn = ref(localStorage.getItem('isLoggedIn') === 'true')

function handleLoginSuccess() {
  isLoggedIn.value = true
}

onMounted(() => {
  isLoggedIn.value = localStorage.getItem('isLoggedIn') === 'true'
})
</script>

<template>
    <div id="root" :class="{ 'not-logged-in': !isLoggedIn }">
      <header id="header" role="banner">
        <div id="header_left">
          <span id="header_left_title">TowaTalk</span>
        </div>
        <div v-if="isLoggedIn" id="header_right">
          <button @click="logout">退出登录</button>
        </div>
      </header>
  
      <div class="main-container">
        <!-- 登录状态显示侧边栏和主内容 -->
        <template v-if="isLoggedIn">
          <nav id="sidebar" role="navigation">
            <div id="sidebar_top">
              <RouterLink to="/" title="Info">
                <ProfileIcon class="icon info" />
              </RouterLink>
              <RouterLink to="/chat" title="Chat">
                <ChatIcon class="icon message" />
              </RouterLink>
            </div>
          </nav>
  
          <div id="profiles">
            <div id="profiles_body">
              <RouterView @login-success="handleLoginSuccess" />
            </div>
          </div>
        </template>
        
        <!-- 未登录状态显示登录页面 -->
        <div v-else class="login-view-container">
          <RouterView @login-success="handleLoginSuccess" />
        </div>
      </div>
    </div>
  </template>
  

<script>
export default {
  methods: {
    logout() {
      localStorage.removeItem('isLoggedIn')
      this.$router.push('/login')
      location.reload()
    }
  }
}
</script>

<style>
@import './App.css';

#header_right {
  padding: 0 1rem;
}

#header_right button {
  padding: 0.5rem 1rem;
  background: transparent;
  border: 1px solid white;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

.main-container {
  display: flex;
  flex: 1;
  height: calc(100vh - 60px); /* 减去header高度 */
  position: relative;
}

.login-view-container {
  flex: 1;
  width: 100%;
  height: 100%;
  position: relative;
}
</style>