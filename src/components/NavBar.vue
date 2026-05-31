<template>
  <nav class="navbar">
    <ul class="nav-list">
      <li class="nav-item"><a href="#" @click.prevent="$emit('navigate', 'Home')" class="nav-link">Home</a></li>
      <li class="nav-item"><a href="#" @click.prevent="$emit('navigate', 'Timeline')" class="nav-link">Timeline</a></li>
      <li class="nav-item"><a href="#" @click.prevent="$emit('navigate', 'Stories')" class="nav-link">Stories</a></li>
      <li class="nav-item"><a href="#" @click.prevent="$emit('navigate', 'Game')" class="nav-link">Game</a></li>
    </ul>
  </nav>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import './NavBar.css'; // Import the CSS file for the navbar

export default defineComponent({
  name: 'NavBar',
  emits: ['navigate'], // Declare the custom event
  setup() {
    return {};
  },
});
</script>