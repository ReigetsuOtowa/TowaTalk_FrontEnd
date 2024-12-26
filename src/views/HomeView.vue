<template>
  <div class="home">
    <div class="user-list">
      <div v-for="user in users" :key="user.id" class="user-card" @click="selectUser(user)">
        <img :src="user.avatar" :alt="user.name" class="user-avatar">
        <div class="user-info">
          <h3>{{ user.name }}</h3>
          <p>{{ user.status }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      users: [
        { id: 1, name: '用户1', status: '在线', avatar: '/avatar1.png' },
        { id: 2, name: '用户2', status: '离线', avatar: '/avatar2.png' }
      ]
    }
  },
  methods: {
    selectUser(user) {
      this.$router.push({ path: '/chat', query: { id: user.id } })
    }
  }
}
</script>

<style>
.home {
  padding: 20px 40px;
  height: 100%;
  overflow-y: auto;
}

.user-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin: 0 auto;
}

.user-card {
  display: flex;
  align-items: center;
  padding: 20px;
  background: white;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.user-card:hover {
  background: #f5f5f5;
}

.user-avatar {
  width: 50px;
  height: 50px;
  border-radius: 25px;
  margin-right: 15px;
}

.user-info h3 {
  margin: 0;
  color: var(--text-color);
}

.user-info p {
  margin: 5px 0 0;
  color: var(--grey-color);
}
</style>