<template>
  <nav class="navbar">
    <ul class="nav-list">
      <li v-for="item in items" :key="item">
        <a
          href="#"
          class="nav-link"
          :class="{ 'nav-link--active': activePage === item }"
          @click.prevent="$emit('navigate', item)"
        >{{ item }}</a>
      </li>
    </ul>
  </nav>
</template>

<script setup lang="ts">
import './NavBar.css';

defineProps<{ activePage: string }>();
defineEmits<{ navigate: [page: string] }>();

const items = ['Home', 'Timeline', 'Stories', 'Game'];
</script>
