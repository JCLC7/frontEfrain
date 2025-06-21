<template>
  <!-- Transparent Navbar -->
  <nav class="absolute top-0 left-0 w-full z-10 bg-transparent">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between h-20">
        <div class="flex items-center">
          <a href="#" class="flex-shrink-0 flex items-center">
            <img class="h-10 w-auto" src="https://tailwindflex.com/images/logo.svg" alt="Logo" />
            <span class="ml-2 text-xl font-bold text-white">Name of Company</span>
          </a>
          <div class="hidden md:ml-45 md:flex md:space-x-10">
            <a href="#" class="text-white hover:text-gray-200 px-3 py-2 text-sm font-medium"
              >Inicio</a
            >
            <a href="#" class="text-white hover:text-gray-200 px-3 py-2 text-sm font-medium"
              >Sobre Nosotros</a
            >
            <a href="#" class="text-white hover:text-gray-200 px-3 py-2 text-sm font-medium"
              >Servicios</a
            >
            <a href="#" class="text-white hover:text-gray-200 px-3 py-2 text-sm font-medium"
              >Contacto</a
            >
          </div>
        </div>

        <div class="flex items-center">
          <!-- Mobile Menu Button -->
          <div class="flex items-center md:hidden">
            <button
              type="button"
              class="inline-flex items-center justify-center p-2 rounded-md text-white hover:text-gray-200 hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white"
              @click="toggleMenu"
              :aria-expanded="isMenuOpen.toString()"
            >
              <span class="sr-only">Open main menu</span>
              <svg
                class="h-6 w-6"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6h16M4 12h16M4 18h16"
                />
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Mobile Menu -->
    <div v-show="isMenuOpen" class="md:hidden">
      <div class="px-2 pt-2 pb-3 space-y-1 bg-gray-800 bg-opacity-90">
        <a href="#" class="text-white block px-3 py-2 rounded-md text-base font-medium">Home</a>
        <a
          href="#"
          class="text-gray-300 hover:text-white hover:bg-gray-700 block px-3 py-2 rounded-md text-base font-medium"
          >About</a
        >
        <a
          href="#"
          class="text-gray-300 hover:text-white hover:bg-gray-700 block px-3 py-2 rounded-md text-base font-medium"
          >Services</a
        >
        <a
          href="#"
          class="text-gray-300 hover:text-white hover:bg-gray-700 block px-3 py-2 rounded-md text-base font-medium"
          >Contact</a
        >
      </div>
    </div>
  </nav>
  <Hero />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Hero from './Hero.vue'

const isMenuOpen = ref(false)

function toggleMenu() {
  isMenuOpen.value = !isMenuOpen.value
}
</script>
