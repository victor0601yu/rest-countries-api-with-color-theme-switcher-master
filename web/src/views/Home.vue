<script setup>
import { computed, onMounted, ref, watch } from 'vue'
import CountryCard from '../components/CountryCard.vue'
import TestButton from '../components/TestButton.vue'

const API = 'https://restcountries.com/v3.1/all?fields=name,population,region,capital,flags,cca2'

const REGIONS = [
  { label: 'Africa', value: 'Africa' },
  { label: 'America', value: 'Americas' },
  { label: 'Asia', value: 'Asia' },
  { label: 'Europe', value: 'Europe' },
  { label: 'Oceania', value: 'Oceania' },
]

const REGION_FILTER_OPTIONS = [{ label: 'None', value: '' }, ...REGIONS]

const countries = ref([])
const loading = ref(true)
const error = ref(null)
const query = ref('')
const regionFilter = ref('')
const filterOpen = ref(false)

function normaliseCountry(raw) {
  const cap = raw.capital?.[0]
  const png = raw.flags?.png || ''
  const svg = raw.flags?.svg || ''
  return {
    cca2: raw.cca2 || raw.name?.common || '',
    name: raw.name?.common ?? '—',
    population: raw.population ?? 0,
    region: raw.region ?? '—',
    capital: cap && cap.length ? cap : '—',
    flagUrl: png || svg,
    flagSvg: svg,
    flagAlt: raw.flags?.alt || `${raw.name?.common ?? 'Country'} flag`,
  }
}

const filteredCountries = computed(() => {
  let list = countries.value
  const q = query.value.trim().toLowerCase()
  if (q) {
    list = list.filter((c) => c.name.toLowerCase().includes(q))
  }
  if (regionFilter.value) {
    list = list.filter((c) => c.region === regionFilter.value)
  }
  return list
})

const regionButtonLabel = computed(() => {
  if (!regionFilter.value) return 'Filter by Region'
  const found = REGIONS.find((r) => r.value === regionFilter.value)
  return found ? found.label : 'Filter by Region'
})

function selectRegion(value) {
  regionFilter.value = value
  filterOpen.value = false
}

function onDocClick(e) {
  const target = e.target
  if (!(target instanceof Element)) return
  if (!target.closest?.('[data-filter-root]')) {
    filterOpen.value = false
  }
}

onMounted(async () => {
  document.addEventListener('click', onDocClick)
  try {
    const res = await fetch(API)
    if (!res.ok) throw new Error('Failed to load countries')
    const data = await res.json()
    countries.value = data.map(normaliseCountry).sort((a, b) => a.name.localeCompare(b.name, 'en'))
  } catch (e) {
    error.value = e instanceof Error ? e.message : 'Something went wrong'
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <main class="main">
    <div class="toolbar">
      <label class="search">
        <span class="visually-hidden">Search for a country</span>
        <span class="search__icon" aria-hidden="true">
          <i class="fa-solid fa-magnifying-glass"></i>
        </span>
        <input
          v-model="query"
          class="search__input"
          type="search"
          placeholder="Search for a country..."
          autocomplete="off"
        />
      </label>

      <div class="filter" data-filter-root>
        <button
          type="button"
          class="filter__btn"
          :aria-expanded="filterOpen"
          aria-haspopup="listbox"
          @click.stop="filterOpen = !filterOpen"
        >
          {{ regionButtonLabel }}
          <span class="filter__chev" aria-hidden="true">
            <i class="fa-solid fa-chevron-down"></i>
          </span>
        </button>
        <ul v-show="filterOpen" class="filter__menu" role="listbox">
          <li v-for="r in REGION_FILTER_OPTIONS" :key="r.value || 'none'" role="option">
            <button type="button" class="filter__option" @click="selectRegion(r.value)">
              {{ r.label }}
            </button>
          </li>
        </ul>
      </div>

      <TestButton />
    </div>

    <p v-if="loading" class="state">Loading…</p>
    <p v-else-if="error" class="state state--error">{{ error }}</p>
    <p v-else-if="!filteredCountries.length" class="state">No countries match your filters.</p>

    <div v-else class="grid">
      <CountryCard
        v-for="(c, index) in filteredCountries"
        :key="c.cca2"
        :name="c.name"
        :population="c.population"
        :region="c.region"
        :capital="c.capital"
        :flag-url="c.flagUrl"
        :flag-fallback="c.flagSvg !== c.flagUrl ? c.flagSvg : ''"
        :flag-alt="c.flagAlt"
        :eager="index < 8"
        :cca2="c.cca2"
      />
    </div>
  </main>
</template>

<style scoped>
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

.main {
  max-width: 1320px;
  margin: 0 auto;
  padding: 2.25rem clamp(1rem, 4vw, 5rem) 3rem;
}

.toolbar {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
  margin-bottom: 2.5rem;
}

@media (min-width: 768px) {
  .toolbar {
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }
}

.search {
  position: relative;
  display: block;
  flex: 1;
  max-width: 480px;
}

.search__icon {
  position: absolute;
  left: 1.35rem;
  top: 50%;
  translate: 0 -50%;
  color: var(--color-input);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.95rem;
  line-height: 1;
  opacity: 0.55;
  pointer-events: none;
}

.search__input {
  width: 100%;
  padding: 1rem 1rem 1rem 3.25rem;
  border: none;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  color: var(--color-text);
}

.search__input::placeholder {
  color: var(--color-input);
}

.search__input:focus {
  outline: 2px solid var(--color-input);
  outline-offset: 1px;
}

.filter {
  position: relative;
  align-self: flex-start;
}

.filter__btn {
  display: inline-flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
  min-width: 200px;
  padding: 1rem 1.15rem;
  border: none;
  border-radius: 4px;
  background: var(--color-surface);
  box-shadow: var(--color-shadow);
  cursor: pointer;
  font-weight: var(--fw-semibold);
  text-align: left;
}

.filter__chev {
  display: flex;
  align-items: center;
  line-height: 1;
  font-size: 0.7rem;
  opacity: 0.7;
}

.filter__menu {
  position: absolute;
  right: 0;
  top: calc(100% + 0.35rem);
  min-width: 200px;
  margin: 0;
  padding: 0.5rem 0;
  list-style: none;
  background: var(--color-surface);
  border-radius: 4px;
  box-shadow: var(--color-shadow);
  z-index: 10;
}

.filter__option {
  display: block;
  width: 100%;
  padding: 0.45rem 1.15rem;
  border: none;
  background: transparent;
  text-align: left;
  cursor: pointer;
  font-weight: var(--fw-semibold);
}

.filter__option:hover {
  opacity: 0.8;
}

.grid {
  display: grid;
  gap: 2.75rem;
  grid-template-columns: 1fr;
}

@media (min-width: 640px) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (min-width: 1280px) {
  .grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

.state {
  margin: 2rem 0;
  text-align: center;
}

.state--error {
  color: #b00020;
}
</style>