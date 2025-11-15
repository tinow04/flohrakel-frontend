<script setup lang="ts">
import { ref, onMounted } from 'vue';

type Announcement = {
  id?: number;
  titel: string;
  text: string;
  datum: string;
  link?: string;
};

const fallbackAnnouncements: Announcement[] = [
  {
    id: 1,
    titel: 'Testankündigung',
    text: 'Dies ist eine Beispiel-Ankündigung, um das Kartenlayout zu zeigen.',
    datum: '2025-01-15',
  },
];

const announcements = ref<Announcement[]>([]);
const isLoading = ref(true);

const ANNOUNCEMENTS_URL = '/api/announcements';

const df = new Intl.DateTimeFormat('de-DE', {
  day: '2-digit',
  month: 'long',
  year: 'numeric',
});

function formatDate(iso: string): string {
  const [y, m, d] = iso.split('-').map(Number);
  return df.format(new Date(y, m - 1, d));
}

async function loadAnnouncements() {
  isLoading.value = true;
  try {
    const res = await fetch(ANNOUNCEMENTS_URL);
    if (!res.ok) {
      console.error('Fehler beim Laden der Ankündigungen:', res.statusText);
      // Fallback, wenn der Server einen Fehlerstatus liefert
      announcements.value = fallbackAnnouncements;
      return;
    }
    const data = await res.json();
    if (Array.isArray(data) && data.length) {
      announcements.value = data;
    } else {
      // Fallback, damit immer wenigstens eine Karte angezeigt wird
      announcements.value = fallbackAnnouncements;
    }
  } catch (err) {
    console.error('Netzwerkfehler beim Laden der Ankündigungen:', err);
    // Fallback auch bei Netzwerkfehlern
    announcements.value = fallbackAnnouncements;
  } finally {
    isLoading.value = false;
  }
}

onMounted(() => {
  loadAnnouncements();
});
</script>

<template>
  <section
    class="announcement-section"
    aria-labelledby="ank-heading"
  >
    <h2 class="ank-heading-visible">Aktuelle Ankündigungen</h2>
    <h2 id="ank-heading" class="visually-hidden">Aktuelle Ankündigungen</h2>
    <p v-if="isLoading" class="announcement-info">
      Lade aktuelle Ankündigungen ...
    </p>
    <article
      v-else
      class="announcement-card"
      v-for="(a, i) in announcements"
      :key="i"
    >
      <header class="announcement-header">
        <div class="announcement-meta">
          <span class="badge">Neu</span>
          <time class="announcement-date">{{ formatDate(a.datum) }}</time>
        </div>
        <h3 class="announcement-title">{{ a.titel }}</h3>
      </header>
      <p class="announcement-body">
        {{ a.text }}
      </p>
    </article>
  </section>
</template>

<style scoped>
.announcement-section {
  margin-top: 2.5rem;
  max-width: 900px;
  padding: 0 1rem;
  margin-left: auto;
  margin-right: auto;
}
.announcement-section > * + * {
  margin-top: 0.75rem;
}

.ank-heading-visible {
  font-size: 1.6rem;
  font-weight: 600;
  margin-bottom: 1rem;
}

.announcement-info {
  font-size: 0.95rem;
  color: #555;
  margin-bottom: 0.75rem;
}

.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}

.announcement-card {
  background: #ffffff;
  border-radius: 16px;
  padding: 1.25rem 1.5rem;
  margin-bottom: 1.25rem;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.12);
  border: 1px solid #e2e8f0;
  position: relative;
  overflow: hidden;
}
.announcement-card::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  border-left: 4px solid #17497f;
  pointer-events: none;
}

.announcement-header {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  margin-bottom: 0.5rem;
}
.announcement-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.85rem;
}

.badge {
  background: #17497f;
  color: #ffffff;
  padding: 0.25rem 0.65rem;
  border-radius: 999px;
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.02em;
  text-transform: uppercase;
}

.announcement-date {
  font-size: 0.85rem;
  color: #64748b;
}

.announcement-title {
  font-weight: 700;
  font-size: 1.2rem;
  margin: 0;
  color: #0f172a;
}

.announcement-body {
  margin: 0;
  margin-top: 0.5rem;
  color: #334155;
  line-height: 1.6;
  font-size: 0.98rem;
}

@media (max-width: 600px) {
  .announcement-card {
    padding: 1rem 1.1rem;
  }
  .announcement-title {
    font-size: 1.05rem;
  }
}
</style>