<script setup lang="ts">
import { RouterLink } from 'vue-router';

const props = defineProps<{
  imgClass?: string;
}>();

const imgClass = props.imgClass;

const items = [
  {
    to: '/rastatt',
    src: '../../assets/rastatt.jpeg',
    alt: 'Rastatt',
  },
  {
    to: '/kehl',
    src: '../../assets/kehl.jpeg',
    alt: 'Kehl',
  },
  {
    to: '/rheinstetten',
    src: '../../assets/rheinstetten.jpeg',
    alt: 'Rheinstetten',
  },
];

// Inline SVG fallback to avoid missing file issues
const fallback =
  "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='800' height='600'><rect fill='%23f2f4f7' width='100%' height='100%'/><text x='50%' y='50%' dominant-baseline='middle' text-anchor='middle' fill='%2390a4ae' font-family='arial' font-size='32'>Bild nicht verfügbar</text></svg>";
</script>
<template>
  <div class="image-row">
    <RouterLink v-for="(i, idx) in items" :key="idx" :to="i.to">
      <figure class="location-card">
        <img
          :src="i.src"
          :alt="i.alt"
          :class="imgClass || 'location-img'"
          loading="lazy"
          @error="(e: any) => (e.target.src = fallback)"
        />
        <figcaption class="location-name">{{ i.alt }}</figcaption>
      </figure>
    </RouterLink>
  </div>
</template>

<style>
.image-row {
  display: grid;
  grid-template-columns: repeat(3, minmax(300px, 1fr));
  gap: 20px;
  justify-items: center;
  margin-top: clamp(24px, 4vh, 48px);
}

.image-row a {
  display: block;
  width: 100%;
  max-width: 400px;
}

.location-img {
  width: 100%;
  height: 200px;
  aspect-ratio: 16 / 9;
  border-radius: 16px;
  object-fit: cover;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08);
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease,
    filter 0.2s ease;
}

.location-img:hover {
  transform: translateY(-3px) scale(1.03);
  box-shadow: 0 10px 24px rgba(0, 0, 0, 0.12);
  filter: brightness(1.05);
}

.location-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.location-name {
  margin-top: 8px;
  font-weight: 600;
  color: #17497f;
  font-size: 1rem;
}

/* Responsive breakpoints */
@media (max-width: 900px) {
  .image-row {
    grid-template-columns: repeat(2, minmax(220px, 1fr));
  }
}
@media (max-width: 600px) {
  .image-row {
    grid-template-columns: 1fr;
  }
}
</style>
