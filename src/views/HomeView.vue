<template>
  <main class="min-h-screen p-8 bg-red-900">
    <h1 class="mb-8 text-5xl font-bold text-red-100">Welcome to SpotiFry</h1>
    <MusicList @selectedMusic="setSelectedMusic" />
    <MusicPlayer :music="selectedMusic" v-if="selectedMusic" />
  </main>
</template>

<script setup>
import MusicList from "@/components/MusicList.vue";
import MusicPlayer from "@/components/MusicPlayer.vue";
import { ref } from "vue";

const selectedMusic = ref(null);

const setSelectedMusic = (music) => {
  selectedMusic.value = music;
};
</script>
