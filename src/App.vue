<script setup lang="ts">
import MessageIcon from './components/icons/IconMessage.vue';
import ProfileIcon from './components/icons/IconProfile.vue';
import LogoutIcon from './components/icons/IconLogout.vue';
</script>

<template>
    <!-- 预留登录对话框弹出 -->
    <!-- <LoginDialog></LoginDialog> -->

    <div id="root">
        <header id="header" role="banner">
            <div id="header__left">
                <span id="header_title">
                    TowaTalk
                </span>
            </div>
            <div id="header__right">
                <LogoutIcon />
            </div>
        </header>

        <nav id="sidebar" role="navigation">
            <div id="sidebar__top">
                <RouterLink to="/" title="Info">
                    <ProfileIcon />
                </RouterLink>
                <RouterLink to="/chat" title="Chat">
                    <MessageIcon />
                </RouterLink>

            </div>
            <div id="sidebar__bottom">
            </div>
        </nav>

        <section id="listcard" role="main">
            <div id="listbody">
                <div class="list-item">
                    <div class="list-item__avatar">

                    </div>
                </div>
                <span class="list-item__name">

                </span>
                <span class="list-item__bio">

                </span>
            </div>
        </section>
        <RouterView id="chatcard" />
    </div>
  
</template>

<script lang="ts">
import { ref, watch } from 'vue'
import { RouterLink, RouterView } from 'vue-router'

</script>

<style scoped lang="scss">
@import './app.scss';
</style>