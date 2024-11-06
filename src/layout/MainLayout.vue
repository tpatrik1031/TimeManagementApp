<template>
  <nav class="bg-gradient-to-r from-purple-700 to-indigo-900 p-4 shadow-lg relative">
    <div class="max-w-screen-xxl mx-6 flex items-center justify-between">
      <a href="#" class="flex items-center space-x-3 rtl:space-x-reverse group">
        <CalendarIcon :height="6" class="text-white transition-transform transform group-hover:scale-110" />
        <span class="text-2xl font-semibold text-white tracking-wide transition-opacity group-hover:opacity-80">
          Időnyilvántartó
        </span>
      </a>
      <button
        @click="isMenuOpen = !isMenuOpen"
        class="md:hidden flex items-center p-2 w-10 h-10 text-white rounded-lg focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 hover:bg-purple-800 transition-transform transform hover:scale-105"
        aria-controls="navbar-default"
        aria-expanded="false"
      >
        <HamburgerIcon />
      </button>
      <div
        class="hidden md:flex md:w-auto md:items-center space-x-6 text-lg"
        id="navbar-default"
      >
        <ul class="flex space-x-6">
          <li>
            <a href="#" class="text-white hover:text-blue-200 transition duration-200">
              Főoldal
            </a>
          </li>
          <li>
            <a href="#" class="text-white hover:text-blue-200 transition duration-200">
              Naptár
            </a>
          </li>
        </ul>
      </div>
    </div>
    <div
      v-show="isMenuOpen"
      class="absolute top-full left-0 w-full bg-indigo-800 p-4 shadow-lg md:hidden z-10"
      id="navbar-default"
    >
      <ul class="flex flex-col space-y-4 text-lg">
        <li>
          <a href="#" class="text-white hover:text-blue-200 transition duration-200">
            Főoldal
          </a>
        </li>
        <li>
          <a href="#" class="text-white hover:text-blue-200 transition duration-200">
            Naptár
          </a>
        </li>
      </ul>
    </div>
  </nav>
  <slot />
</template>

<script setup>
  import { ref } from 'vue';
  import { onMounted } from 'vue';
  import { initFlowbite } from 'flowbite';
  import HamburgerIcon from '../components/icons/HamburgerIcon.vue';
  import CalendarIcon from '../components/icons/CalendarIcon.vue';

  const isMenuOpen = ref(false);

  onMounted(() => {
    initFlowbite();
  });
</script>