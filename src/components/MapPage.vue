<template>
  <div class="map-container">
    <div class="image-and-hotspots-wrapper" ref="imageWrapperRef">
      <img
        src="../assets/Travels_of_Marco_Polo.jpg"
        alt="Marco Polo's Journey Map"
        class="world-map"
        ref="imageRef"
        @load="updateImageRenderedSize"
      />

      <div
        v-for="(hotspot, index) in processedHotspots"
        :key="index"
        class="hotspot"
        :style="{ left: hotspot.x + '%', top: hotspot.y + '%' }"
        @mouseover="showInfo(hotspot)"
        @mouseleave="hideInfo()"
      >
        <span class="hotspot-marker"></span>
        <div v-if="activeHotspot === hotspot" class="info-box">
          <h3>{{ hotspot.name }}</h3>
          <p>{{ hotspot.description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted, onBeforeUnmount } from 'vue';
import './MapPage.css'; // Import the CSS file
import rawHotspots from '../assets/hotspots.json'; // Import the JSON file

interface HotspotData {
  name: string;
  description: string;
  x: number; // Pixel X coordinate from original image
  y: number; // Pixel Y coordinate from original image
}

interface ProcessedHotspot extends HotspotData {
  x: number; // Percentage X coordinate relative to rendered image
  y: number; // Percentage Y coordinate relative to rendered image
}

export default defineComponent({
  name: 'MapPage',
  setup() {
    // !!! BELANGRIJK: VERIFIEER DEZE WAARDEN MET DE WERKELIJKE PIXELAFMETINGEN VAN JE AFBEELDING !!!
    const originalImageWidth = 1355; // Controleer deze waarde!
    const originalImageHeight = 692; // Controleer deze waarde!

    const imageWrapperRef = ref<HTMLElement | null>(null);
    const imageRef = ref<HTMLImageElement | null>(null);

    // Houd de werkelijke schaal en offset bij die object-fit: cover toepast
    const imageCoverTransform = ref({
      scale: 1,
      offsetX: 0, // Pixel offset from left of wrapper
      offsetY: 0, // Pixel offset from top of wrapper
      wrapperWidth: 0,
      wrapperHeight: 0,
    });

    const updateImageRenderedSize = () => {
      if (imageRef.value && imageWrapperRef.value) {
        const wrapper = imageWrapperRef.value;
        const wrapperWidth = wrapper.clientWidth;
        const wrapperHeight = wrapper.clientHeight;

        const imageAspectRatio = originalImageWidth / originalImageHeight;
        const wrapperAspectRatio = wrapperWidth / wrapperHeight;

        let scale = 1;
        let offsetX = 0;
        let offsetY = 0;

        if (imageAspectRatio > wrapperAspectRatio) {
          // Afbeelding is breder dan wrapper (relatief). Schaal op basis van hoogte.
          scale = wrapperHeight / originalImageHeight;
          offsetX = (wrapperWidth - (originalImageWidth * scale)) / 2;
          offsetY = 0;
        } else {
          // Afbeelding is smaller of even hoog als wrapper (relatief). Schaal op basis van breedte.
          scale = wrapperWidth / originalImageWidth;
          offsetY = (wrapperHeight - (originalImageHeight * scale)) / 2;
          offsetX = 0;
        }

        imageCoverTransform.value = {
          scale,
          offsetX,
          offsetY,
          wrapperWidth,
          wrapperHeight,
        };
        console.log('Image Cover Transform:', imageCoverTransform.value);
      }
    };

    // Pas de hotspots aan op basis van de werkelijke gerenderde afmetingen
    const processedHotspots = computed<ProcessedHotspot[]>(() => {
      const { scale, offsetX, offsetY, wrapperWidth, wrapperHeight } = imageCoverTransform.value;

      if (wrapperWidth === 0 || wrapperHeight === 0) {
        return []; // Wacht tot de afmetingen zijn berekend
      }

      return rawHotspots.map(hotspot => {
        // Converteer originele pixelcoördinaten naar geschaalde pixelcoördinaten
        const scaledX = hotspot.x * scale;
        const scaledY = hotspot.y * scale;

        // Voeg de offset toe en converteer naar percentage van de wrapper
        const finalX = scaledX + offsetX;
        const finalY = scaledY + offsetY;

        return {
          ...hotspot,
          x: (finalX / wrapperWidth) * 100,
          y: (finalY / wrapperHeight) * 100,
        };
      });
    });

    const activeHotspot = ref<ProcessedHotspot | null>(null);

    const showInfo = (hotspot: ProcessedHotspot) => {
      activeHotspot.value = hotspot;
    };

    const hideInfo = () => {
      activeHotspot.value = null;
    };

    onMounted(() => {
      window.addEventListener('resize', updateImageRenderedSize);
      // Update bij mount, en ook na een korte vertraging om zeker te zijn dat alles gerenderd is
      updateImageRenderedSize();
      setTimeout(updateImageRenderedSize, 100);
    });

    onBeforeUnmount(() => {
      window.removeEventListener('resize', updateImageRenderedSize);
    });

    return {
      imageWrapperRef,
      imageRef,
      processedHotspots,
      activeHotspot,
      showInfo,
      hideInfo,
    };
  },
});
</script>