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
    <div class="fixed inset-0 -z-10 bg-red-950">
      <picture class="absolute w-full h-screen">
        <img
          src="@/assets/bg-red.svg"
          alt=""
          class="bottom-0 object-cover object-right-bottom w-full h-screen contrast-125 brightness-50"
        />
      </picture>
      <!-- <div
        class="absolute inset-0 bg-radial-at-center from-transparent via-transparent to-red-950/50 backdrop-blur-md"
        style="
          mask-image: radial-gradient(
            circle at center,
            transparent 90%,
            black 100%
          );
        "
      ></div> -->
      <!-- <div
        class="absolute inset-0 bg-radial-at-center from-red-900/20 via-red-900/40 to-red-950/80"
      ></div> -->
    </div>
    <Nav />
    <RouterView v-slot="{ Component }">
      <transition
        mode="out-in"
        @enter="(el) => gsap.to(el, { opacity: 1, y: 0, duration: 0.4 })"
        @before-enter="(el) => gsap.set(el, { opacity: 0, y: 20 })"
      >
        <component :is="Component" :key="$route.path" class="pt-14" />
      </transition>
    </RouterView>
    <MusicPlayer :music="selectedMusic" v-if="selectedMusic" />
  </div>
</template>
