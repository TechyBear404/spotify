<template>
  <div
    class="fixed bottom-0 left-0 w-full p-4 text-red-100 border-t border-red-900 music-player bg-red-950/70 backdrop-blur-lg"
  >
    <div class="mx-auto max-w-7xl">
      <div class="flex justify-between">
        <div class="font-medium truncate">{{ currentMusic?.title }}</div>
        <div class="flex items-center gap-2 min-w-[150px]">
          <button
            @click="toggleMute"
            class="text-red-100 transition-colors hover:text-red-300"
          >
            <font-awesome-icon :icon="volumeIcon" class="w-4 h-4" />
          </button>
          <input
            type="range"
            min="0"
            max="100"
            v-model="volume"
            @input="updateVolume"
            class="w-full h-1 mx-2 rounded-lg appearance-none cursor-pointer hover:h-2 transition-all bg-gradient-to-r from-red-500 from-[length:var(--volume-progress)] to-red-100 to-[length:var(--volume-progress)]"
            :style="{ '--volume-progress': volumePercentage }"
          />
        </div>
      </div>
      <div class="flex items-center justify-center gap-4 my-2">
        <button
          @click="toggleLoop"
          class="flex items-center justify-center w-10 h-10 text-red-100 transition-all duration-300 rounded-full shadow-md bg-red-100/10 backdrop-blur-md nav-button-group hover:bg-red-100/20 active:bg-red-900/40 hover:shadow-lg hover:shadow-red-500/20"
          :class="{ 'bg-red-100/30': isLooping }"
        >
          <font-awesome-icon
            icon="fa-solid fa-repeat"
            class="w-5 h-5 transition-transform duration-300 nav-button-group-hover:scale-125"
          />
        </button>
        <button
          class="flex items-center justify-center w-10 h-10 text-red-100 transition-all duration-300 rounded-full shadow-md bg-red-100/10 backdrop-blur-md nav-button-group hover:bg-red-100/20 active:bg-red-900/40 hover:shadow-lg hover:shadow-red-500/20"
        >
          <font-awesome-icon
            icon="fa-solid fa-backward-step"
            class="w-5 h-5 transition-transform duration-300 nav-button-group-hover:scale-125"
          />
        </button>
        <button
          @click="togglePlay"
          class="flex items-center justify-center w-12 h-12 transition-all duration-300 bg-red-100 rounded-full shadow-md backdrop-blur-md play-button-group text-red-950 hover:bg-white active:bg-red-200 hover:shadow-lg hover:shadow-red-500/30"
        >
          <font-awesome-icon
            v-if="!isPlaying"
            icon="fa-solid fa-play"
            class="w-5 h-5 ml-0.5 transition-transform duration-300 play-button-group-hover:scale-125"
          />
          <font-awesome-icon
            v-else
            icon="fa-solid fa-pause"
            class="w-5 h-5 transition-transform duration-300 play-button-group-hover:scale-125"
          />
        </button>
        <button
          class="flex items-center justify-center w-10 h-10 text-red-100 transition-all duration-300 rounded-full shadow-md bg-red-100/10 backdrop-blur-md nav-button-group hover:bg-red-100/20 active:bg-red-900/40 hover:shadow-lg hover:shadow-red-500/20"
        >
          <font-awesome-icon
            icon="fa-solid fa-forward-step"
            class="w-5 h-5 transition-transform duration-300 nav-button-group-hover:scale-125"
          />
        </button>
      </div>
      <audio
        ref="audio"
        autoplay
        :loop="isLooping"
        :src="currentMusic?.src"
        @timeupdate="updateTime"
        @loadedmetadata="updateDuration"
      ></audio>
      <div class="flex items-center justify-between gap-4 mt-2">
        <div class="text-sm opacity-80">{{ formatTime(currentTime) }}</div>
        <input
          type="range"
          min="0"
          :max="duration"
          v-model="currentTime"
          @input="seek"
          class="w-full h-1 mx-2 rounded-lg appearance-none cursor-pointer hover:h-2 transition-all bg-gradient-to-r from-red-500 from-[length:var(--progress)] to-red-100 to-[length:var(--progress)]"
          :style="{ '--progress': progressPercentage }"
        />
        <div class="text-sm opacity-80">{{ formatTime(duration) }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, onMounted } from "vue";
import { gsap } from "gsap";

const VOLUME_STORAGE_KEY = "spotifry-volume";
const MUTE_STORAGE_KEY = "spotifry-muted";

const props = defineProps(["music"]);

const audio = ref(null);
const currentMusic = ref(props.music || {});
const isPlaying = ref(true);
const currentTime = ref(0);
const duration = ref(0);
const isLooping = ref(false);

const togglePlay = () => {
  if (isPlaying.value) {
    audio.value.pause();
  } else {
    audio.value.play();
  }
  isPlaying.value = !isPlaying.value;
};

const updateTime = () => {
  if (audio.value) {
    currentTime.value = audio.value.currentTime;
  }
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

const progressPercentage = computed(() => {
  return ((currentTime.value / duration.value) * 100 || 0) + "%";
});

watch(
  () => props.music,
  (newMusic) => {
    console.log("newMusic", newMusic);
    currentMusic.value = newMusic || {};
    if (audio.value) {
      audio.value.src = ""; // Force reload
      audio.value.src = currentMusic.value.src;
      restartMusic();
    }
  }
);

const restartMusic = () => {
  if (audio.value) {
    console.log("Restarting music");
    audio.value.currentTime = 0;
    if (isPlaying.value) {
      audio.value.play();
    }
  }
};

const volume = ref(Number(localStorage.getItem(VOLUME_STORAGE_KEY)) || 100);
const prevVolume = ref(Number(localStorage.getItem(VOLUME_STORAGE_KEY)) || 100);
const isMuted = ref(localStorage.getItem(MUTE_STORAGE_KEY) === "true");

const volumeIcon = computed(() => {
  if (isMuted.value || volume.value === 0) return "fa-solid fa-volume-xmark";
  if (volume.value < 50) return "fa-solid fa-volume-low";
  return "fa-solid fa-volume-high";
});

const toggleMute = () => {
  isMuted.value = !isMuted.value;
  localStorage.setItem(MUTE_STORAGE_KEY, isMuted.value.toString());

  if (isMuted.value) {
    prevVolume.value = volume.value;
    volume.value = 0;
  } else {
    volume.value = prevVolume.value;
  }
  updateVolume();
};

const updateVolume = () => {
  if (audio.value) {
    audio.value.volume = volume.value / 100;
    localStorage.setItem(VOLUME_STORAGE_KEY, volume.value.toString());
    if (volume.value > 0) {
      isMuted.value = false;
      localStorage.setItem(MUTE_STORAGE_KEY, "false");
    }
  }
};

// Add volume watcher
watch(volume, (newVolume) => {
  localStorage.setItem(VOLUME_STORAGE_KEY, newVolume.toString());
});

const volumePercentage = computed(() => {
  return `${volume.value}%`;
});

const toggleLoop = () => {
  isLooping.value = !isLooping.value;
  if (audio.value) {
    audio.value.loop = isLooping.value;
  }
};

onMounted(() => {
  if (audio.value) {
    const savedVolume = Number(localStorage.getItem(VOLUME_STORAGE_KEY)) || 100;
    const savedMuted = localStorage.getItem(MUTE_STORAGE_KEY) === "true";

    volume.value = savedMuted ? 0 : savedVolume;
    prevVolume.value = savedVolume;
    isMuted.value = savedMuted;

    audio.value.volume = volume.value / 100;
  }
  gsap.fromTo(
    ".music-player",
    {
      y: 50,
      opacity: 0,
    },
    {
      y: 0,
      opacity: 1,
      duration: 0.4,
      ease: "power2.out",
      clearProps: "transform",
    }
  );
});
</script>

<style scoped>
input[type="range"] {
  -webkit-appearance: none;
}

input[type="range"]::-webkit-slider-thumb {
  @apply appearance-none w-3 h-3 bg-red-50 hover:bg-red-100 rounded-full cursor-pointer transition-transform hover:scale-125;
  margin-top: -4px;
}

input[type="range"]::-webkit-slider-runnable-track {
  @apply h-1 rounded-lg bg-transparent transition-all hover:h-2;
}

.slider-input::-moz-range-thumb {
  @apply w-3 h-3 bg-red-50 hover:bg-red-100 rounded-full cursor-pointer border-0 transition-transform hover:scale-125;
  margin-top: -4px;
}

.slider-input::-moz-range-track {
  @apply h-1 rounded-lg bg-red-950/50 transition-all hover:h-2;
}

.slider-input::-moz-range-progress {
  @apply h-1 rounded-lg bg-red-500 transition-all hover:h-2;
}

/* Keep the hover transform utilities */
.nav-button-group:hover .nav-button-group-hover\:scale-125 {
  @apply scale-125;
}

.play-button-group:hover .play-button-group-hover\:scale-125 {
  @apply scale-125;
}
</style>
