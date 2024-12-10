<template>
  <div class="fixed bottom-0 left-0 w-full p-6 text-red-100 bg-red-950">
    <div>currentMusic: {{ currentMusic?.title }}</div>
    <div class="flex items-center justify-center gap-2">
      <font-awesome-icon icon="fa-solid fa-backward-step" />
      <div @click="togglePlay">
        <font-awesome-icon
          v-if="!isPlaying"
          icon="fa-solid fa-play"
          class="p-2 bg-red-100 rounded-full text-red-950 aspect-square"
        />
        <font-awesome-icon
          v-else
          icon="fa-solid fa-pause"
          class="p-2 bg-red-100 rounded-full text-red-950 aspect-square"
        />
      </div>
      <font-awesome-icon icon="fa-solid fa-forward-step" />
    </div>
    <audio
      ref="audio"
      autoplay
      :src="currentMusic?.src"
      @timeupdate="updateTime"
      @loadedmetadata="updateDuration"
    ></audio>
    <div class="flex items-center justify-between mt-4">
      <div>{{ formatTime(currentTime) }}</div>
      <input
        type="range"
        min="0"
        :max="duration"
        v-model="currentTime"
        @input="seek"
        class="w-full h-1 mx-4 bg-red-500 rounded-lg appearance-none cursor-pointer"
      />
      <div>{{ formatTime(duration) }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const props = defineProps(["music"]);

const audio = ref(null);
const currentMusic = ref(props.music || {});
const isPlaying = ref(true);
const currentTime = ref(0);
const duration = ref(0);

const togglePlay = () => {
  if (isPlaying.value) {
    audio.value.pause();
  } else {
    audio.value.play();
  }
  isPlaying.value = !isPlaying.value;
};

const updateTime = () => {
  currentTime.value = audio.value.currentTime;
};

const updateDuration = () => {
  duration.value = audio.value.duration;
};

const formatTime = (time) => {
  const minutes = Math.floor(time / 60);
  const seconds = Math.floor(time % 60)
    .toString()
    .padStart(2, "0");
  return `${minutes}:${seconds}`;
};

const seek = (event) => {
  audio.value.currentTime = event.target.value;
};

watch(
  () => props.music,
  (newMusic) => {
    console.log("newMusic", newMusic);
    currentMusic.value = newMusic || {};
    if (isPlaying.value) {
      audio.value.play();
    }
  }
);
</script>

<style scoped>
input[type="range"]::-webkit-slider-thumb {
  width: 15px;
  height: 15px;
  background: #ffffff;
  cursor: pointer;
  border-radius: 50%;
  -webkit-appearance: none;
}

input[type="range"]::-moz-range-thumb {
  width: 15px;
  height: 15px;
  background: #ffffff;
  cursor: pointer;
  border-radius: 50%;
}

input[type="range"]::-ms-thumb {
  width: 15px;
  height: 15px;
  background: #ffffff;
  cursor: pointer;
  border-radius: 50%;
}
</style>
