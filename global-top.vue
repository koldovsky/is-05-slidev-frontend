<script setup lang="ts">
import { computed } from 'vue'
import { useNav } from '@slidev/client'

const { slides, currentSlideNo } = useNav()

const showSlideNumber = computed(() => currentSlideNo.value > 1)
const totalSlides = computed(() => slides.value.length)
</script>

<template>
  <div
    v-if="showSlideNumber"
    class="slide-hud"
    aria-hidden="true"
  >
    <div class="slide-hud__grid">
      <span class="slide-hud__cur">{{ currentSlideNo }}</span>
      <span class="slide-hud__slash">/</span>
      <span class="slide-hud__tot">{{ totalSlides }}</span>
    </div>
  </div>
</template>

<style>
.slide-hud {
  position: fixed;
  top: 10px;
  right: 16px;
  z-index: 100;
  padding: 5px 8px 6px;
  border-radius: 3px;
  font-family:
    ui-monospace,
    'Cascadia Code',
    'Segoe UI Mono',
    Consolas,
    monospace;
  font-size: 10px;
  font-weight: 600;
  line-height: 1.2;
  letter-spacing: 0.02em;
  font-variant-numeric: tabular-nums;
  color: #9fe79a;
  text-shadow:
    0 0 6px rgba(80, 255, 120, 0.45),
    0 0 1px rgba(0, 0, 0, 0.9);
  background: linear-gradient(
    165deg,
    rgba(0, 14, 6, 0.82) 0%,
    rgba(0, 22, 10, 0.72) 100%
  );
  border: 1px solid rgba(60, 200, 90, 0.28);
  box-shadow:
    0 0 0 1px rgba(0, 0, 0, 0.35) inset,
    0 1px 8px rgba(0, 0, 0, 0.35);
  pointer-events: none;
  user-select: none;
  transform: scale(0.5);
  transform-origin: top right;
}

.slide-hud__grid {
  display: grid;
  grid-template-columns: 3.5ch 1ch 4ch;
  column-gap: 4px;
  align-items: center;
}

.slide-hud__cur {
  text-align: right;
  color: #c8ffc4;
}

.slide-hud__slash {
  text-align: center;
  color: #8fdf88;
}

.slide-hud__tot {
  text-align: left;
  color: #c8ffc4;
}
</style>
