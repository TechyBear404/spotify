<template>
  <main class="relative min-h-screen bg-red-950 page-transition">
    <div
      class="absolute inset-0 bg-gradient-to-br from-red-950/90 via-red-900/80 to-red-950/90"
    ></div>
    <div class="absolute inset-0 opacity-5"></div>
    <div class="relative p-8 max-w-[1400px] mx-auto">
      <MusicList />
    </div>
  </main>
</template>

<script setup>
import MusicList from "@/components/MusicList.vue";
</script>

<style scoped>
.page-transition {
  transform-origin: top;
}
</style>
