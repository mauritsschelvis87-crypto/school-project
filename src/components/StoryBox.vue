<template>
  <div class="story-box">
    <span v-if="showYear" class="story-box-year">{{ year }}</span>
    <h3 class="story-box-name">{{ title }}</h3>
    <p class="story-box-summary">{{ description }}</p>
    <p v-if="story" class="story-box-story">{{ story }}</p>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import './StoryBox.css';

const props = withDefaults(
  defineProps<{
    year: string;
    name: string;
    description: string;
    story?: string;
    showYear?: boolean;
    country?: string;
  }>(),
  { story: '', showYear: true, country: '' },
);

const title = computed(() =>
  props.country ? `${props.name} - ${props.country}` : props.name,
);
</script>
