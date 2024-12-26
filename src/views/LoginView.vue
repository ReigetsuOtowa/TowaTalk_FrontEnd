<template>
    <div class="login-container">
      <div class="login-box">
        <h1>TowaTalk 登录</h1>
        <div class="login-form">
          <input 
            type="text" 
            v-model="username" 
            placeholder="用户名"
            @keyup.enter="handleLogin"
          >
          <input 
            type="password" 
            v-model="password" 
            placeholder="密码"
            @keyup.enter="handleLogin"
          >
          <button @click="handleLogin" :disabled="isLoading">
            {{ isLoading ? '登录中...' : '登录' }}
          </button>
          <p v-if="error" class="error">{{ error }}</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        username: '',
        password: '',
        isLoading: false,
        error: '',
        ws: null
      }
    },
    created() {
      this.initWebSocket()
    },
    methods: {
      initWebSocket() {
        this.ws = new WebSocket('ws://localhost:8080/websocket')
        
        this.ws.onopen = () => {
          console.log('Connected to server')
        }
        
        this.ws.onmessage = (event) => {
          const response = JSON.parse(event.data)
          if (response.type === 'LOGIN') {
            if (response.success) {
              this.$router.push('/')
              localStorage.setItem('isLoggedIn', 'true')
              this.$emit('login-success')
            } else {
              this.error = response.message || '登录失败'
            }
            this.isLoading = false
          }
        }
      },
      handleLogin() {
        if (!this.username || !this.password) {
          this.error = '请输入用户名和密码'
          return
        }
        
        this.isLoading = true
        this.error = ''
        
        const loginRequest = {
          type: 'LOGIN',
          username: this.username,
          password: this.password
        }
        this.ws.send(JSON.stringify(loginRequest))
      }
    }
  }
  </script>
  
  <style scoped>
  .login-container {
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f5f5f5;
  }
  
  .login-box {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    width: 100%;
    max-width: 400px;
  }
  
  .login-form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  
  input {
    padding: 0.8rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
  }
  
  button {
    padding: 0.8rem;
    background: var(--theme-color, #007fff);
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
  }
  
  button:disabled {
    opacity: 0.7;
    cursor: not-allowed;
  }
  
  .error {
    color: red;
    font-size: 0.9rem;
  }
  </style>