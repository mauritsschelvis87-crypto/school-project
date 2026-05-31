<template>
  <div class="shared-page-background stories-page">
    <div class="content-page">
      <div class="centered-content">
        <aside class="timeline-sidebar">
          <div class="timeline-bar"></div>
        </aside>
        <div class="stories-stream">
          <article v-for="entry in stories" :key="entry.name" class="story-row">
            <div class="story-row-timeline">
              <span class="story-row-year">{{ entry.year }}</span>
              <span class="story-row-dot"></span>
            </div>
            <StoryBox
              :year="entry.year"
              :name="entry.name"
              :country="entry.country"
              :description="entry.description"
              :story="entry.story"
              :show-year="false"
            />
          </article>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import StoryBox from './StoryBox.vue';
import stories from '../assets/stories.json';
import '../styles/shared-page.css';
import './StoriesPage.css';
</script>
