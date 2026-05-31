<script setup lang="ts">
import { shallowRef, ref, type Component } from 'vue';
import NavBar from './components/NavBar.vue';
import HomePage from './components/HomePage.vue';
import MapPage from './components/MapPage.vue';
import StoriesPage from './components/StoriesPage.vue';
import GamePage from './components/GamePage.vue';

const pages: Record<string, Component> = {
  Home: HomePage,
  Timeline: MapPage,
  Stories: StoriesPage,
  Game: GamePage,
};

const currentPage = shallowRef<Component>(HomePage);
const activePage = ref('Home');

function handleNavigation(page: string) {
  if (pages[page]) {
    currentPage.value = pages[page];
    activePage.value = page;
  }
}
</script>

<template>
  <div class="app-container">
    <NavBar :active-page="activePage" @navigate="handleNavigation" />
    <main class="page-slot">
      <KeepAlive>
        <component :is="currentPage" />
      </KeepAlive>
    </main>
  </div>
</template>

<style>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}

.page-slot {
  flex: 1;
  min-height: 0;
  display: flex;
  flex-direction: column;
}

.page-slot > div {
  flex: 1;
  min-height: 0;
  display: flex;
  flex-direction: column;
}
</style>
