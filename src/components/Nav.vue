<template>
  <nav
    class="flex justify-between p-4 bg-red-800 text-red-100 font-bold shadow-lg"
  >
    <div class="text-red-100 text-2xl">SpotiFry</div>
    <div class="flex gap-6">
      <RouterLink
        to="/"
        class="text-red-100 hover:text-red-500 transition-colors duration-300"
        >Home</RouterLink
      >
      <RouterLink
        to="/about"
        class="text-red-100 hover:text-red-500 transition-colors duration-300"
        >About</RouterLink
      >
    </div>
    <div></div>
  </nav>
</template>
