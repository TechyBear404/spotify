<template>
  <main class="relative min-h-screen page-transition">
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

.bg-image {
  background-image: url("@/assets/bg-red.svg");
}
</style>
