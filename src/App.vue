<script setup lang="ts">
import { shallowRef } from 'vue'; // 'ref' removed as it's not used
import NavBar from './components/NavBar.vue';
import HomePage from './components/HomePage.vue';
import MapPage from './components/MapPage.vue';

const currentPage = shallowRef(HomePage);

const handleNavigation = (pageName: string) => {
  if (pageName === 'Home') {
    currentPage.value = HomePage;
  } else if (pageName === 'Timeline') {
    currentPage.value = MapPage;
  }
  // Add more conditions for 'Stories', 'Game' etc. if you create those components
};
</script>

<template>
  <div class="app-container">
    <NavBar @navigate="handleNavigation" />
    <component :is="currentPage" />
  </div>
</template>

<style>
/* Global styles are in src/style.css */

.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
  overflow: hidden; /* Prevent overall scrolling */
}
</style>