<template>
  <div ref="boxRef" class="text-hover-box" :style="style">
    <StoryBox :year="hotspot.year" :name="hotspot.name" :description="hotspot.description" />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, onBeforeUnmount, nextTick } from 'vue';
import StoryBox from './StoryBox.vue';
import './TextHoverBox.css';

const props = defineProps<{
  hotspot: { name: string; description: string; year: string };
}>();

const PAD = 8;
const GAP = 10;

const boxRef = ref<HTMLElement | null>(null);
const style = ref<Record<string, string>>({});

function place() {
  const box = boxRef.value;
  const hotspotEl = box?.parentElement;
  const wrapper = hotspotEl?.parentElement;
  if (!box || !hotspotEl || !wrapper) return;

  const ww = wrapper.clientWidth;
  const wh = wrapper.clientHeight;
  const wr = wrapper.getBoundingClientRect();
  const hr = hotspotEl.getBoundingClientRect();
  const hl = hr.left - wr.left;
  const ht = hr.top - wr.top;
  const ax = hl + hr.width / 2;
  const ay = ht + hr.height / 2;
  const bw = box.offsetWidth;
  const bh = box.offsetHeight;

  let top = ay - bh - GAP;
  if (top < PAD) top = ay + hr.height / 2 + GAP;
  if (top + bh > wh - PAD) {
    top = Math.max(PAD, Math.min(ay - bh - GAP, wh - PAD - bh));
  }

  const left = Math.max(PAD, Math.min(ax - bw / 2, ww - PAD - bw));

  style.value = {
    top: `${top - ht}px`,
    left: `${left - hl}px`,
  };
}

const refresh = () => nextTick(() => requestAnimationFrame(place));

onMounted(() => {
  refresh();
  window.addEventListener('resize', refresh);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', refresh);
});

watch(() => props.hotspot, refresh);
</script>
