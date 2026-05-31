<template>
  <div class="map-container">
    <div
      class="image-and-hotspots-wrapper"
      ref="wrapperRef"
      :style="{ '--hotspot-scale': hotspotScale }"
    >
      <img
        src="../assets/Travels_of_Marco_Polo.jpg"
        alt="Marco Polo's Journey Map"
        class="world-map"
        ref="imageRef"
        @load="updateLayout"
      />
      <div
        v-for="(hotspot, index) in hotspots"
        :key="hotspot.name"
        class="hotspot"
        :class="{ 'hotspot--active': activeIndex === index }"
        :style="{ left: hotspot.x + '%', top: hotspot.y + '%' }"
        @mouseenter="activeIndex = index"
        @mouseleave="activeIndex = null"
      >
        <span class="hotspot-marker"></span>
        <span class="hotspot-year">{{ hotspot.year }}</span>
        <TextHoverBox v-if="activeIndex === index" :hotspot="hotspot" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';
import './MapPage.css';
import rawHotspots from '../assets/hotspots.json';
import TextHoverBox from './TextHoverBox.vue';

const IMG_W = 1355;
const IMG_H = 692;

const wrapperRef = ref<HTMLElement | null>(null);
const imageRef = ref<HTMLImageElement | null>(null);
const layout = ref({ scale: 0, offsetX: 0, offsetY: 0, w: 0, h: 0 });
const activeIndex = ref<number | null>(null);

function updateLayout() {
  const wrapper = wrapperRef.value;
  const image = imageRef.value;
  if (!wrapper || !image) return;

  const w = wrapper.clientWidth;
  const h = wrapper.clientHeight;
  const imgRatio = IMG_W / IMG_H;
  const boxRatio = w / h;

  let scale: number;
  let offsetX: number;
  let offsetY: number;

  if (imgRatio > boxRatio) {
    scale = h / IMG_H;
    offsetX = (w - IMG_W * scale) / 2;
    offsetY = 0;
  } else {
    scale = w / IMG_W;
    offsetY = (h - IMG_H * scale) / 2;
    offsetX = 0;
  }

  layout.value = { scale, offsetX, offsetY, w, h };
}

const hotspots = computed(() => {
  const { scale, offsetX, offsetY, w, h } = layout.value;
  if (!w || !h || !scale) return [];

  return rawHotspots.map((spot) => ({
    ...spot,
    x: ((spot.x * scale + offsetX) / w) * 100,
    y: ((spot.y * scale + offsetY) / h) * 100,
  }));
});

const hotspotScale = computed(() => {
  const { scale } = layout.value;
  if (!scale) return '1';
  const base = 600 / IMG_H;
  return String(Math.min(2.2, Math.max(0.85, scale / base)));
});

onMounted(() => {
  window.addEventListener('resize', updateLayout);
  updateLayout();
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', updateLayout);
});
</script>
