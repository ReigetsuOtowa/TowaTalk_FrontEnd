<template>
    <div id="root">
        <!-- 登录/注册对话框 -->
        <el-dialog v-model="dialogVisible" :close-on-click-modal="false" :show-close="false" width="400px">
            <el-tabs v-model="activeTab" class="demo-tabs">
                <el-tab-pane label="登录" name="login">
                    <el-form :model="loginForm" :rules="rules" label-width="80px">
                        <el-form-item label="用户名" prop="userName">
                            <el-input v-model="loginForm.userName" />
                        </el-form-item>
                        <el-form-item label="密码" prop="password">
                            <el-input v-model="loginForm.password" type="password" />
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="handleLogin">登录</el-button>
                        </el-form-item>
                    </el-form>
                </el-tab-pane>

                <el-tab-pane label="注册" name="register">
                    <el-form :model="registerForm" :rules="rules" label-width="80px">
                        <el-form-item label="用户名" prop="userName">
                            <el-input v-model="registerForm.userName" />
                        </el-form-item>
                        <el-form-item label="密码" prop="password">
                            <el-input v-model="registerForm.password" type="password" />
                        </el-form-item>
                        <el-form-item label="确认密码">
                            <el-input v-model="registerForm.confirmPassword" type="password" />
                        </el-form-item>
                        <el-form-item label="性别">
                            <el-select v-model="registerForm.sex">
                                <el-option label="女" value="0" />
                                <el-option label="男" value="1" />
                            </el-select>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="handleRegister">注册</el-button>
                        </el-form-item>
                    </el-form>
                </el-tab-pane>
            </el-tabs>
        </el-dialog>

        <header id="header" role="banner">
            <div id="header__left">
                <span id="header__title">
                    TowaTalk
                </span>
            </div>
            <div id="header__right">
                <LogoutIcon class="logout-icon" @click="handleLogout" />
            </div>
        </header>

        <!-- 侧边栏 -->
        <nav id="sidebar" role="navigation">
            <div id="sidebar__top">
                <div class="icon-wrapper" :class="{ active: panelState === 0 }" @click="switchPanel(0)">
                    <IconFriend />
                </div>
                <div class="icon-wrapper" :class="{ active: panelState === 1 }" @click="switchPanel(1)">
                    <IconFriendAdd />
                </div>
                <div class="icon-wrapper" :class="{ active: panelState === 2 }" @click="switchPanel(2)">
                    <IconFriendCheck />
                </div>
            </div>
        </nav>

        <!-- 列表面板 -->
        <div id="listcard">
            <!-- 好友列表 -->
            <div v-if="panelState === 0" class="friend-list">
                <el-empty v-if="friends.length === 0" description="暂无好友" />
                <div v-else class="list-item" v-for="friend in friends" :key="friend.id" @click="selectChat(friend)">
                    <el-avatar :size="40" :src="friend.avatar">
                        {{ friend.nickName?.[0] || friend.userName[0] }}
                    </el-avatar>
                    <div class="list-item__info">
                        <span class="name">{{ friend.nickName || friend.userName }}</span>
                    </div>
                </div>
            </div>

            <!-- 陌生人列表 -->
            <div v-if="panelState === 1" class="stranger-list">
                <el-empty v-if="strangers.length === 0" description="暂无可添加的用户" />
                <div v-else class="list-item" v-for="stranger in strangers" :key="stranger.id">
                    <el-avatar :size="40" :src="stranger.avatar">
                        {{ stranger.userName[0] }}
                    </el-avatar>
                    <div class="list-item__info">
                        <span class="name">{{ stranger.userName }}</span>
                        <el-button type="primary" size="small" @click="addFriend(stranger.id)">
                            添加好友
                        </el-button>
                    </div>
                </div>
            </div>

            <!-- 请求列表 -->
            <div v-if="panelState === 2" class="request-list">
                <el-empty v-if="friendRequests.length === 0" description="暂无好友请求" />
                <div v-else class="list-item" v-for="request in friendRequests" :key="request.id">
                    <el-avatar :size="40">{{ request.userName[0] }}</el-avatar>
                    <div class="list-item__info">
                        <span class="name">{{ request.userName }}</span>
                        <div class="actions">
                            <el-button type="success" size="small" @click="handleFriendRequest(request.id, true)">
                                接受
                            </el-button>
                            <el-button type="danger" size="small" @click="handleFriendRequest(request.id, false)">
                                拒绝
                            </el-button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <section id="chatcard">
            <template v-if="currentChat">
                <!-- 聊天头部 -->
                <div class="chat-header">
                    <span class="chat-title">{{ currentChat.userName }}</span>
                </div>

                <!-- 聊天消息区域 -->
                <div class="chat-messages" ref="messageContainer">
                    <div v-for="(msg, index) in currentChat.messages" :key="index"
                        :class="['message', msg.isSelf ? 'message--self' : 'message--other']">
                        <div class="message__content">
                            {{ msg.content }}
                        </div>
                        <div class="message__time">
                            {{ formatTime(msg.timestamp) }}
                        </div>
                    </div>
                </div>

                <!-- 输入区域 -->
                <div class="chat-input">
                    <el-input v-model="messageInput" type="text" placeholder="输入消息..." @keyup.enter="sendMessage">
                        <template #append>
                            <el-button @click="sendMessage">发送</el-button>
                        </template>
                    </el-input>
                </div>
            </template>

            <div v-else class="chat-placeholder">
                <el-empty description="选择一个好友开始聊天" />
            </div>
        </section>
    </div>

</template>

<script setup lang="ts">
import LogoutIcon from './components/icons/IconLogout.vue';
import IconFriend from './components/icons/IconFriend.vue';
import IconFriendAdd from './components/icons/IconFriendAdd.vue';
import IconFriendCheck from './components/icons/IconFriendCheck.vue';
import { ref, onMounted, watch } from 'vue';
import { ElMessage, ElMessageBox } from 'element-plus';
import axios from 'axios';
import dayjs from 'dayjs';
import utc from 'dayjs/plugin/utc';
import timezone from 'dayjs/plugin/timezone';

dayjs.extend(utc);
dayjs.extend(timezone);

// 对话框显示控制
const dialogVisible = ref(false);
const activeTab = ref('login');
const logoutDialogVisible = ref(false);

// WebSocket变量
const ws = ref<WebSocket | null>(null);
const baseUrl = 'ws://127.0.0.1:8578';

// 面板状态：0-好友列表 1-陌生人 2-好友请求
const panelState = ref(0)

const switchPanel = (state: number) => {
    panelState.value = state
}

// 表单数据
const loginForm = ref({
    userName: '',
    password: ''
});

const registerForm = ref({
    userName: '',
    password: '',
    confirmPassword: '',
    sex: '1'
});

// 表单校验规则
const rules = {
    userName: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
    password: [{ required: true, message: '请输入密码', trigger: 'blur' }],
};

// 添加 WebSocket 连接函数
const connectWebSocket = (userId: string) => {
    ws.value = new WebSocket(`${baseUrl}/imserver/${userId}`);

    ws.value.onopen = () => {
        console.log('WebSocket连接已建立');
    };

    ws.value.onmessage = (event) => {
        if (event.data !== "连接成功") {
            try {
                const data = JSON.parse(event.data);
                if (currentChat.value && data.message) {
                    currentChat.value.messages.push({
                        content: data.message,
                        isSelf: false,
                        timestamp: Date.now()
                    });
                }
                console.log('收到消息:', data);
            } catch (error) {
                console.error('解析消息失败:', error);
            }
        }
    };

    ws.value.onclose = () => {
        console.log('WebSocket连接已关闭');
    };

    ws.value.onerror = (error) => {
        console.error('WebSocket错误:', error);
    };
};

// 检查登录状态
onMounted(() => {
    const userInfo = localStorage.getItem('userInfo');
    if (userInfo) {
        const user = JSON.parse(userInfo);
        connectWebSocket(user.id);
        // 添加初始加载好友列表
        loadFriends();
    } else {
        dialogVisible.value = true;
    }
});

// 处理登录
const handleLogin = async () => {
    try {
        const response = await axios.post('http://127.0.0.1:8578/user/login', loginForm.value);
        if (response.data.message === "登录成功") {
            const userInfo = response.data.data;
            localStorage.setItem("userInfo", JSON.stringify(userInfo));

            // 登录成功后建立 WebSocket 连接
            connectWebSocket(userInfo.id);

            dialogVisible.value = false;
            ElMessage.success('登录成功');
        } else {
            ElMessage.error(response.data.message);
        }
    } catch (error) {
        ElMessage.error('登录失败');
        console.error(error);
    }
};

// 处理注册
const handleRegister = async () => {
    if (registerForm.value.password !== registerForm.value.confirmPassword) {
        ElMessage.error('两次密码不匹配');
        return;
    }

    try {
        const response = await axios.post('http://127.0.0.1:8578/user/register', {
            userName: registerForm.value.userName,
            password: registerForm.value.password,
            sex: registerForm.value.sex
        });
        ElMessage.success(response.data.message);
        activeTab.value = 'login';
    } catch (error) {
        ElMessage.error('注册失败');
        console.error(error);
    }
};

// 处理登出
const handleLogout = () => {
    ElMessageBox.confirm('确认退出登录吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
    })
        .then(() => {
            // 关闭 WebSocket 连接
            if (ws.value) {
                ws.value.close();
                ws.value = null;
            }

            localStorage.removeItem('userInfo');
            dialogVisible.value = true;
            ElMessage.success('已退出登录');
        })
        .catch(() => {
            ElMessage.info('已取消退出');
        });
};

// 定义类型
interface User {
    id: string;
    userName: string;
    nickName?: string;
    avatar?: string;
    sex: '0' | '1';
}

interface FriendRequest {
    id: string;
    sendUserId: string;
    receiveUserId: string;
    message: string;
    userName: string;
    status: number;
}

// 状态管理
const friends = ref<User[]>([]);
const strangers = ref<User[]>([]);
const friendRequests = ref<FriendRequest[]>([]);
const currentUser = ref<User | null>(null);

// 加载好友列表
const loadFriends = async () => {
    try {
        const userInfo = localStorage.getItem('userInfo');
        if (!userInfo) return;

        const userId = JSON.parse(userInfo).id;
        const response = await axios.get(`http://127.0.0.1:8578/friend/list/${userId}`);
        friends.value = response.data.data;
    } catch (error) {
        console.error('加载好友列表失败:', error);
        ElMessage.error('加载好友列表失败');
    }
};

// 加载陌生人列表
const loadStrangers = async () => {
    try {
        const userInfo = localStorage.getItem('userInfo');
        if (!userInfo) return;

        const userId = JSON.parse(userInfo).id;
        const response = await axios.get(`http://127.0.0.1:8578/friend/list/un/${userId}`);
        strangers.value = response.data.data;
    } catch (error) {
        console.error('加载陌生人列表失败:', error);
        ElMessage.error('加载陌生人列表失败');
    }
};

// 加载好友请求列表
const loadFriendRequests = async () => {
    try {
        const userInfo = localStorage.getItem('userInfo');
        if (!userInfo) return;

        const userId = JSON.parse(userInfo).id;
        const response = await axios.get(`http://127.0.0.1:8578/friend/add/list/${userId}`);
        friendRequests.value = response.data.data;
    } catch (error) {
        console.error('加载好友请求失败:', error);
        ElMessage.error('加载好友请求失败');
    }
};

// 添加好友
const addFriend = async (targetUserId: string) => {
    try {
        const userInfo = localStorage.getItem('userInfo');
        if (!userInfo) return;

        const userId = JSON.parse(userInfo).id;
        const response = await axios.post('http://127.0.0.1:8578/friend/add', {
            sendUserId: userId,
            receiveUserId: targetUserId,
            message: '请求添加好友'
        });

        ElMessage.success(response.data.message);
        await loadStrangers();
    } catch (error) {
        console.error('发送好友请求失败:', error);
        ElMessage.error('发送好友请求失败');
    }
};

// 处理好友请求
const handleFriendRequest = async (requestId: string, isAccept: boolean) => {
    try {
        const params = new URLSearchParams();
        params.append('addFriendId', requestId);
        params.append('isReceive', isAccept ? '1' : '2');

        const response = await axios.post('http://127.0.0.1:8578/friend/add/list/handle',
            params
        );

        ElMessage.success(response.data.message);
        await loadFriendRequests();
        await loadFriends();
    } catch (error) {
        console.error('处理好友请求失败:', error);
        ElMessage.error('处理好友请求失败');
    }
};


// 监听面板切换，加载相应数据
watch(panelState, (newState) => {
    switch (newState) {
        case 0:
            loadFriends();
            break;
        case 1:
            loadStrangers();
            break;
        case 2:
            loadFriendRequests();
            break;
    }
});

// 聊天相关的状态
const currentChat = ref<{
    userId: string;
    userName: string;
    messages: Array<{
        content: string;
        isSelf: boolean;
        timestamp: number;
    }>;
} | null>(null);

const messageInput = ref('');

// 选择聊天对象
const selectChat = async (friend: User) => {
    currentChat.value = {
        userId: friend.id,
        userName: friend.nickName || friend.userName,
        messages: []
    };

    // 加载历史消息
    await loadChatHistory(friend.id);
};

// 获取用户本地时区
const userTimezone = dayjs.tz.guess();

// 加载聊天历史
const loadChatHistory = async (friendId: string) => {
    try {
        const userInfo = localStorage.getItem('userInfo');
        if (!userInfo) return;

        const userId = JSON.parse(userInfo).id;
        const response = await axios.get(`http://127.0.0.1:8578/message/list`, {
            params: {
                sid: userId,
                rid: friendId
            }
        });

        currentChat.value!.messages = response.data.data.map((msg: any) => ({
            content: msg.content,
            isSelf: msg.sendUserId === userId,
            // 将 UTC 时间转换为本地时区时间
            timestamp: dayjs.utc(msg.sendTime).tz(userTimezone).valueOf()
        })).reverse();

    } catch (error) {
        console.error('加载聊天历史失败:', error);
        ElMessage.error('加载聊天历史失败');
    }
};


// 发送消息
const sendMessage = () => {
    if (!messageInput.value.trim() || !currentChat.value || !ws.value) {
        return;
    }

    const msgData = {
        toUserId: currentChat.value.userId,
        message: messageInput.value
    };

    // 发送消息
    ws.value.send(JSON.stringify(msgData));

    // 添加到本地消息列表
    currentChat.value.messages.push({
        content: messageInput.value,
        isSelf: true,
        timestamp: Date.now()
    });

    // 清空输入框
    messageInput.value = '';
};

// 格式化时间
const formatTime = (timestamp: number) => {
    return dayjs(timestamp).format('HH:mm'); // 或者使用其他你想要的格式
};
</script>

<style scoped lang="scss">
@import './app.scss';
</style>