<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import Navbar from './components/Navbar.vue'
import About from './views/AboutView.vue'
import Services from './views/ServicesVue.vue'
import Contact from './views/ContactView.vue'
</script>

<template>
  <header>
    <Navbar />
  </header>
  <About />
  <Services />
  <Contact />

  <RouterView />
</template>
