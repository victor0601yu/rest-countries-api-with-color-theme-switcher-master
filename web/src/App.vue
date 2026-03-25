<script setup>
import { onBeforeMount, ref, watch } from 'vue'
import AppHeader from './components/AppHeader.vue'

const dark = ref(
  typeof localStorage !== 'undefined' && localStorage.getItem('theme') === 'dark',
)

function applyThemeClass() {
  if (typeof document === 'undefined') return
  document.documentElement.classList.toggle('theme-dark', dark.value)
}

onBeforeMount(applyThemeClass)

watch(dark, (v) => {
  applyThemeClass()
  if (typeof localStorage !== 'undefined') {
    localStorage.setItem('theme', v ? 'dark' : 'light')
  }
})

function toggleTheme() {
  dark.value = !dark.value
}
</script>

<template>
  <div class="page">
    <AppHeader :dark="dark" @toggle-theme="toggleTheme" />
    <router-view />
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
}
</style>