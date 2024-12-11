<template>
  <div
    class="fixed bottom-0 left-0 w-full p-4 text-red-100 border-t border-red-900 music-player bg-red-950/80 backdrop-blur-lg"
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
            class="w-full h-1 mx-2 transition-all rounded-lg appearance-none cursor-pointer hover:h-2"
            :class="volumeBarClass"
          />
        </div>
      </div>
      <div class="flex items-center justify-center gap-4 my-2">
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

const progressPercentage = computed(() => {
  return ((currentTime.value / duration.value) * 100 || 0) + "%";
});

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

const volumeBarClass = computed(() => {
  return {
    "volume-slider": true,
    [`volume-${Math.round(volume.value / 10) * 10}`]: true,
  };
});

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

input[type="range"]::-webkit-slider-runnable-track {
  @apply h-1 rounded-lg bg-transparent transition-all;
}

input[type="range"]:hover::-webkit-slider-runnable-track {
  @apply h-2;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  @apply w-3 h-3 bg-white rounded-full cursor-pointer mt-[-4px] hover:scale-125 transition-transform;
}

input[type="range"]::-moz-range-track {
  @apply h-1 rounded-lg bg-red-100 transition-all;
}

input[type="range"]:hover::-moz-range-track {
  @apply h-2;
}

input[type="range"]::-moz-range-progress {
  @apply h-1 rounded-lg bg-red-500 transition-all;
}

input[type="range"]:hover::-moz-range-progress {
  @apply h-2;
}

input[type="range"]::-moz-range-thumb {
  @apply w-3 h-3 bg-white rounded-full cursor-pointer border-none mt-[-4px] hover:scale-125 transition-transform;
}

input[type="range"]::-ms-thumb {
  @apply w-3 h-3 bg-white rounded-full cursor-pointer mt-[-4px] hover:scale-125 transition-transform;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: #fee2e2;
  cursor: pointer;
  transition: all 0.15s ease;
}

input[type="range"]::-webkit-slider-thumb:hover {
  background: #fecaca;
  transform: scale(1.2);
}

input[type="range"]::-webkit-slider-runnable-track {
  background: rgb(153 27 27 / 0.5);
  border-radius: 0.5rem;
  height: 4px;
}

.nav-button-group:hover .nav-button-group-hover\:scale-125 {
  transform: scale(1.25);
}

.play-button-group:hover .play-button-group-hover\:scale-125 {
  transform: scale(1.25);
}

.volume-slider {
  background: linear-gradient(
    to right,
    rgb(239 68 68) 0%,
    rgb(239 68 68) var(--volume),
    rgb(254 226 226) var(--volume),
    rgb(254 226 226) 100%
  );
}

.volume-0 {
  --volume: 0%;
}
.volume-10 {
  --volume: 10%;
}
.volume-20 {
  --volume: 20%;
}
.volume-30 {
  --volume: 30%;
}
.volume-40 {
  --volume: 40%;
}
.volume-50 {
  --volume: 50%;
}
.volume-60 {
  --volume: 60%;
}
.volume-70 {
  --volume: 70%;
}
.volume-80 {
  --volume: 80%;
}
.volume-90 {
  --volume: 90%;
}
.volume-100 {
  --volume: 100%;
}
</style>
