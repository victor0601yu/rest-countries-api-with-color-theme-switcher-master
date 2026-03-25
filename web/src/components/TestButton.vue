<script setup>
const API = 'https://restcountries.com/v3.1/all?fields=name,population,region,capital,flags,cca2'

async function fetchAllCountries() {
  try {
    const res = await fetch(API)
    if (!res.ok) throw new Error('Failed to load countries')
    const data = await res.json()
    console.log('All Countries Data:', data)
  } catch (e) {
    console.error('Error fetching countries:', e)
  }
}
</script>

<template>
  <button class="test-btn" @click="fetchAllCountries">
    Fetch All Countries
  </button>
</template>

<style scoped>
.test-btn {
  padding: 1rem 1.5rem;
  border: none;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  cursor: pointer;
  font-weight: var(--fw-semibold);
  color: var(--color-text);
}

.test-btn:hover {
  opacity: 0.8;
}
</style>