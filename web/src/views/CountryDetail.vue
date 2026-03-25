<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const country = ref(null)
const loading = ref(true)
const error = ref(null)

async function fetchCountry() {
  loading.value = true
  error.value = null
  try {
    const code = route.params.code
    const res = await fetch(`https://restcountries.com/v3.1/alpha/${code}`)
    if (!res.ok) throw new Error('Country not found')
    const data = await res.json()
    country.value = data[0]
  } catch (e) {
    error.value = e instanceof Error ? e.message : 'Something went wrong'
  } finally {
    loading.value = false
  }
}

function formatPopulation(num) {
  return num.toLocaleString('en-US')
}

function goBack() {
  router.back()
}

watch(() => route.params.code, () => {
  fetchCountry()
})

onMounted(() => {
  fetchCountry()
})
</script>

<template>
  <div class="detail">
    <button class="back-btn" @click="goBack">
      <i class="fa-solid fa-arrow-left"></i>
      Back
    </button>

    <p v-if="loading" class="state">Loading…</p>
    <p v-else-if="error" class="state state--error">{{ error }}</p>

    <div v-else-if="country" class="country">
      <div class="country__flag">
        <img
          :src="country.flags?.svg || country.flags?.png"
          :alt="`${country.name?.common} flag`"
        />
      </div>

      <div class="country__info">
        <h1 class="country__name">{{ country.name?.common }}</h1>

        <div class="country__details">
          <div class="country__column">
            <p><strong>Native Name:</strong> {{ country.name?.nativeName?.[Object.keys(country.name?.nativeName || {})[0]]?.common || '—' }}</p>
            <p><strong>Population:</strong> {{ formatPopulation(country.population || 0) }}</p>
            <p><strong>Region:</strong> {{ country.region || '—' }}</p>
            <p><strong>Sub Region:</strong> {{ country.subregion || '—' }}</p>
            <p><strong>Capital:</strong> {{ country.capital?.[0] || '—' }}</p>
          </div>

          <div class="country__column">
            <p><strong>Top Level Domain:</strong> {{ country.tld?.[0] || '—' }}</p>
            <p>
              <strong>Currencies:</strong>
              {{ country.currencies ? Object.values(country.currencies).map(c => c.name).join(', ') : '—' }}
            </p>
            <p>
              <strong>Languages:</strong>
              {{ country.languages ? Object.values(country.languages).join(', ') : '—' }}
            </p>
          </div>
        </div>

        <div class="country__borders" v-if="country.borders?.length">
          <strong>Border Countries:</strong>
          <div class="border-list">
            <router-link
              v-for="border in country.borders"
              :key="border"
              :to="`/country/${border}`"
              class="border-tag"
            >
              {{ border }}
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.detail {
  max-width: 1320px;
  margin: 0 auto;
  padding: 2.25rem clamp(1rem, 4vw, 5rem) 3rem;
}

.back-btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.6rem 1.5rem;
  border: none;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  cursor: pointer;
  font-weight: var(--fw-semibold);
  color: var(--color-text);
  margin-bottom: 3rem;
}

.back-btn:hover {
  opacity: 0.8;
}

.state {
  margin: 2rem 0;
  text-align: center;
}

.state--error {
  color: #b00020;
}

.country {
  display: grid;
  gap: 3rem;
}

@media (min-width: 1024px) {
  .country {
    grid-template-columns: 1fr 1fr;
    align-items: center;
  }
}

.country__flag {
  width: 100%;
  max-width: 600px;
}

.country__flag img {
  width: 100%;
  height: auto;
  display: block;
  border-radius: 4px;
  box-shadow: var(--color-shadow);
}

.country__name {
  font-size: 2rem;
  font-weight: var(--fw-extra-bold);
  margin-bottom: 1.5rem;
}

.country__details {
  display: grid;
  gap: 2rem;
  margin-bottom: 2rem;
}

@media (min-width: 768px) {
  .country__details {
    grid-template-columns: 1fr 1fr;
  }
}

.country__column p {
  margin-bottom: 0.75rem;
  line-height: 1.8;
}

.country__column strong {
  font-weight: var(--fw-semibold);
}

.country__borders {
  margin-top: 2rem;
}

.country__borders strong {
  display: block;
  margin-bottom: 1rem;
}

.border-list {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
}

.border-tag {
  padding: 0.4rem 1.25rem;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  font-size: 0.9rem;
  text-decoration: none;
  color: var(--color-text);
}

.border-tag:hover {
  opacity: 0.8;
}
</style>