<template>
  <div class="grid grid-cols-6 gap-4 p-4 text-red-100 bg-red-900">
    <div
      v-for="music in musics"
      class="p-4 transition-transform duration-300 transform bg-red-800 rounded-lg shadow-md cursor-pointer hover:scale-105"
      @click="selectedMusic(music)"
    >
      <picture>
        <img
          :src="music.cover"
          alt=""
          class="transition-opacity duration-300 rounded-t-lg hover:opacity-75"
        />
      </picture>
      <div class="mt-2 font-semibold text-red-100">{{ music.title }}</div>
      <div class="text-red-200">{{ music.artist }}</div>
      <div class="text-red-200">{{ music.album }}</div>
      <div class="text-red-200">{{ music.year }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import musicsData from "@/database/db.json";

const musics = ref(musicsData);

const emit = defineEmits(["selectedMusic"]);

const selectedMusic = (music) => {
  emit("selectedMusic", music);
};
</script>
