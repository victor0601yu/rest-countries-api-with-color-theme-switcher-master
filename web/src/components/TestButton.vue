<script setup>
const props = defineProps({
  countryCode: { type: String, default: null },
  size: { type: String, default: 'medium' } // 'medium' or 'small'
})

async function fetchCountryData() {
  try {
    const res = await fetch('/data.json')
    if (!res.ok) throw new Error('Failed to fetch data')
    const data = await res.json()
    if (props.countryCode) {
      const code = props.countryCode.toUpperCase()
      const country = data.find(c => c.cca2?.toUpperCase() === code || c.cca3?.toUpperCase() === code)
      console.log(`Data for ${props.countryCode}:`, country)
    } else {
      console.log('All Countries Data:', data)
    }
  } catch (e) {
    console.error('Error fetching data:', e)
  }
}
</script>

<template>
  <button :class="['test-btn', `test-btn--${props.size}`]" @click="fetchCountryData">
    {{ countryCode ? 'Fetch Current Country' : 'Fetch All Countries' }}
  </button>
</template>

<style scoped>
.test-btn {
  border: none;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  cursor: pointer;
  font-weight: var(--fw-semibold);
  color: var(--color-text);
  transition: opacity 0.2s ease-in-out;
}

.test-btn:hover {
  opacity: 0.8;
}

.test-btn--medium {
  padding: 1rem 1.5rem;
  font-size: 1rem;
}

.test-btn--small {
  padding: 0.6rem 1.5rem;
  font-size: 0.875rem;
}
</style>
