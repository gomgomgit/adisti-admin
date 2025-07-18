<script setup>
import ConversationDashboard from './components/ConversationDashboard.vue'
</script>

<template>
  <div id="app">
    <ConversationDashboard />
  </div>
</template>

<style>
/* Global styles for full page layout */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  height: 100%;
  width: 100%;
  overflow-x: hidden;
}

#app {
  height: 100vh;
  width: 100vw;
  margin: 0;
  padding: 0;
}
</style>
