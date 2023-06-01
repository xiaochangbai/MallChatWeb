<script setup lang="ts">
import ToolBar from './components/ToolBar/index.vue'
import SideBar from './components/SideBar/index.vue'
import ChatBox from './components/ChatBox/index.vue'
</script>

<template>
  <main class="home">
    <div class="wrapper">
      <ToolBar />
      <SideBar />
      <ChatBox />
    </div>
    <footer class="footer">
      <a target="_blank" rel="noopener">@万人大群聊</a>
    </footer>

    <LoginBox />
  </main>
</template>

<style lang="scss" src="./styles.scss" scoped />
