<template>
  <nav
    class="fixed top-0 z-50 w-full border-b shadow-lg bg-red-950/70 backdrop-blur-lg border-red-900/30 shadow-red-950/50"
  >
    <div class="max-w-[1400px] mx-auto flex justify-between items-center p-4">
      <div
        class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-red-50 via-red-200 to-red-100"
      >
        SpotiFry
      </div>
      <div class="flex gap-8">
        <RouterLink
          v-for="link in ['Home', 'About']"
          :key="link"
          :to="link === 'Home' ? '/' : `/${link.toLowerCase()}`"
          class="relative py-2 font-medium transition-colors duration-300 text-red-50 hover:text-red-200 group"
        >
          {{ link }}
          <span
            class="absolute bottom-0 left-0 w-0 h-0.5 bg-gradient-to-r from-red-200 to-red-50 transition-all duration-300 group-hover:w-full opacity-70"
          ></span>
        </RouterLink>
      </div>
      <div></div>
    </div>
  </nav>
</template>
