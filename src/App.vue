<template>
  <div id="app" class="d-flex">
    <SideBar v-if="showSidebar" />
    
    <div class="flex-grow-1 p-4">
      <router-view />
    </div>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router'
import { computed } from 'vue'
import SideBar from './components/SideBar.vue'

const route = useRoute()
const showSidebar = computed(() => route.path !== '/login')
</script>

<style>
@import "https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css";

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  min-height: 100vh;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}

body, html {
  overflow: hidden;
  height: 100%;
}
</style>
