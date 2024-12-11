<template>
  <div
    class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-6 gap-4 sm:gap-5 lg:gap-6 p-3 sm:p-4 lg:p-6"
  >
    <div
      v-for="music in musics"
      class="relative p-3 sm:p-4 transition-all duration-500 border shadow-xl music-card rounded-xl backdrop-blur-md bg-gradient-to-br from-red-800/40 to-red-900/40 border-red-500/20 card-group hover:bg-gradient-to-br hover:from-red-700/50 hover:to-red-800/50 hover:shadow-2xl hover:shadow-red-950/50 hover:-translate-y-1 hover:border-red-400/30"
      @click="viewDetails(music)"
    >
      <div class="relative overflow-hidden rounded-lg shadow-md">
        <img
          :src="music.cover"
          alt=""
          class="w-full transition-transform duration-500 card-group-hover:scale-110 card-group-hover:opacity-90"
        />
        <div
          class="absolute inset-0 flex items-center justify-center transition-opacity duration-300 opacity-0 bg-red-950/40 card-group-hover:opacity-100"
        >
          <button
            @click.stop="selectedMusic(music)"
            class="flex items-center justify-center w-12 h-12 transition-colors duration-300 rounded-full shadow-md bg-red-500/80 backdrop-blur-md play-group hover:bg-red-400 active:bg-red-600 ring-1 ring-red-300/30 hover:ring-red-300/50 hover:shadow-lg hover:shadow-red-500/30"
          >
            <font-awesome-icon
              icon="fa-solid fa-play"
              class="w-5 h-5 ml-0.5 transition-transform duration-300 text-red-50 drop-shadow play-group-hover:scale-125"
            />
          </button>
        </div>
      </div>
      <div class="relative z-10 mt-3 sm:mt-4">
        <h3
          class="mb-1.5 sm:mb-2 text-base sm:text-lg font-bold tracking-tight transition-colors duration-300 text-red-50 drop-shadow-md card-group-hover:text-red-100"
        >
          {{ music.title }}
        </h3>
        <div class="flex items-center gap-1.5 sm:gap-2 mb-1">
          <font-awesome-icon
            icon="fa-solid fa-user"
            class="w-4 text-red-200 drop-shadow"
          />
          <p class="text-sm font-medium text-red-200 drop-shadow">
            {{ music.artist }}
          </p>
        </div>
        <div class="flex items-center gap-1.5 sm:gap-2 mb-1">
          <font-awesome-icon
            icon="fa-solid fa-compact-disc"
            class="w-4 text-red-300 drop-shadow"
          />
          <p class="text-sm text-red-300 drop-shadow">{{ music.album }}</p>
        </div>
        <div class="flex items-center gap-1.5 sm:gap-2">
          <font-awesome-icon
            icon="fa-solid fa-calendar"
            class="w-4 text-red-400 drop-shadow"
          />
          <p class="text-sm text-red-400 drop-shadow">{{ music.year }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, inject, onMounted } from "vue";
import { gsap } from "gsap";
import musicsData from "@/database/db.json";

const musics = ref(musicsData);
const setSelectedMusic = inject("setSelectedMusic");

const selectedMusic = (music) => {
  setSelectedMusic(music);
};

const viewDetails = (music) => {
  // Handle view details if needed
  console.log("View details:", music.title);
};

onMounted(() => {
  const cards = gsap.utils.toArray(".music-card");
  const batchSize = 6; // Number of cards per row

  // Animate in batches
  for (let i = 0; i < cards.length; i += batchSize) {
    const batch = cards.slice(i, i + batchSize);
    gsap.fromTo(
      batch,
      {
        opacity: 0,
        y: 20,
      },
      {
        opacity: 1,
        y: 0,
        duration: 0.3,
        stagger: {
          amount: 0.2,
          from: "start",
        },
        ease: "power1.out",
        clearProps: "all",
        onComplete: () => gsap.set(batch, { clearProps: "all" }),
      }
    );
  }
});
</script>

<style scoped>
.card-group:hover .card-group-hover\:scale-110 {
  transform: scale(1.1);
}

.card-group:hover .card-group-hover\:opacity-90 {
  opacity: 0.9;
}

.card-group:hover .card-group-hover\:opacity-100 {
  opacity: 1;
}

.play-group:hover .play-group-hover\:scale-125 {
  transform: scale(1.25);
}
</style>
