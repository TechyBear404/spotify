<script setup>
import { RouterView } from "vue-router";
import Nav from "./components/Nav.vue";
import MusicPlayer from "./components/MusicPlayer.vue";
import { ref, provide } from "vue";
import { gsap } from "gsap";

const selectedMusic = ref(null);
const setSelectedMusic = (music) => {
  selectedMusic.value = music;
};

provide("selectedMusic", selectedMusic);
provide("setSelectedMusic", setSelectedMusic);
</script>

<template>
  <div class="relative">
    <Nav />
    <div class="pt-14">
      <RouterView v-slot="{ Component }">
        <transition
          mode="out-in"
          @enter="(el) => gsap.to(el, { opacity: 1, y: 0, duration: 0.4 })"
          @before-enter="(el) => gsap.set(el, { opacity: 0, y: 20 })"
        >
          <component :is="Component" :key="$route.path" />
        </transition>
      </RouterView>
    </div>
    <MusicPlayer :music="selectedMusic" v-if="selectedMusic" />
  </div>
</template>
