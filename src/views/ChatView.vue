<template>
    <div class="chat">
        <div class="chat-header">
            <h2>{{ currentUser?.name }}</h2>
        </div>

        <div class="messages" ref="messageContainer">
            <div v-for="msg in messages" :key="msg.id" :class="['message', msg.type]">
                <div class="message-content">{{ msg.content }}</div>
                <div class="message-time">{{ msg.time }}</div>
            </div>
        </div>

        <div class="chat-input">
            <input v-model="newMessage" @keyup.enter="sendMessage" type="text" placeholder="输入消息...">
            <button @click="sendMessage">发送</button>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ChatView',
    data() {
        return {
            currentUser: null,
            messages: [],
            newMessage: ''
        }
    },
    methods: {
        sendMessage() {
            if (!this.newMessage.trim()) return

            this.messages.push({
                id: Date.now(),
                content: this.newMessage,
                type: 'sent',
                time: new Date().toLocaleTimeString()
            })

            this.newMessage = ''
            this.$nextTick(() => {
                this.scrollToBottom()
            })
        },
        scrollToBottom() {
            const container = this.$refs.messageContainer
            container.scrollTop = container.scrollHeight
        }
    },
    mounted() {
        const userId = this.$route.query.id
        // 这里应该根据userId获取用户信息
        this.currentUser = { name: '用户 ' + userId }
    }
}
</script>

<style>
/* 修改聊天界面布局样式 */
.chat {
    height: 520px; /* 与根容器高度一致 */
    display: flex;
    flex-direction: column;
    background: #fff;
    grid-area: 2 / 3 / 3 / 4; /* 指定在grid中的位置 */
}

/* 调整头部样式 */
.chat-header {
    padding: 15px;
    background: var(--theme-color);
    color: white;
    height: 60px;
    display: flex;
    align-items: center;
}

.chat-header h2 {
    margin: 0;
    font-size: 1.2rem;
}

/* 调整消息区域样式 */
.messages {
    flex: 1;
    overflow-y: auto;
    padding: 20px 40px;
    background: #f5f5f5;
    height: calc(100% - 120px); /* 减去头部和输入框的高度 */
}

.message.received {
    background: white;
    margin-right: auto;
}

.message.sent {
    background: var(--theme-color);
    color: white;
    margin-left: auto;
}

.message-time {
    font-size: 12px;
    margin-top: 5px;
    opacity: 0.7;
}

/* 调整输入框区域样式 */
.chat-input {
    padding: 15px;
    background: white;
    display: flex;
    gap: 10px;
    height: 60px;
    border-top: 1px solid #eee;
}


.chat-input input {
    flex: 1;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.chat-input button {
    padding: 10px 20px;
    background: var(--theme-color);
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}
</style>