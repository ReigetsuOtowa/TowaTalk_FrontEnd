<script setup lang="js">
import ProfileIcon from './components/icons/IconProfile.vue'
import ChatIcon from './components/icons/IconMessage.vue'
</script>

<template>
    <div id="root">
        <header id="header" role="banner">
            <div id="header_left">
                <span id="header_left_title">TowaTalk</span>
            </div>
        </header>

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
                
            </div>
        </div>
    </div>
</template>

<script lang="js">

</script>

<style lang="css" scoped>
@import './App.css';
</style>