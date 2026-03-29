<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  name: { type: String, required: true },
  population: { type: Number, required: true },
  region: { type: String, required: true },
  capital: { type: String, default: '—' },
  flagUrl: { type: String, required: true },
  flagFallback: { type: String, default: '' },
  flagAlt: { type: String, default: '' },
  eager: { type: Boolean, default: false },
  cca2: { type: String, required: true },
})

const imgSrc = ref(props.flagUrl)

watch(
  () => props.flagUrl,
  (url) => {
    imgSrc.value = url
  },
)

function onFlagError() {
  if (props.flagFallback && imgSrc.value !== props.flagFallback) {
    imgSrc.value = props.flagFallback
  }
}

const formatPop = (n) => new Intl.NumberFormat('en-US').format(n)
</script>

<template>
  <router-link :to="`/country/${cca2}`" class="card">
    <div class="card__flag-wrap">
      <img class="card__flag" :src="imgSrc" :alt="flagAlt || name" width="320" height="192"
        :loading="eager ? 'eager' : 'lazy'" referrerpolicy="no-referrer" decoding="async" @error="onFlagError" />
    </div>
    <div class="card__body">
      <h2 class="card__name">{{ name }}</h2>
      <dl class="card__dl">
        <div class="card__row">
          <dt class="card__label">Population:</dt>
          <dd class="card__value">{{ formatPop(population) }}</dd>
        </div>
        <div class="card__row">
          <dt class="card__label">Region:</dt>
          <dd class="card__value">{{ region }}</dd>
        </div>
        <div class="card__row">
          <dt class="card__label">Capital:</dt>
          <dd class="card__value">{{ capital }}</dd>
        </div>
      </dl>
    </div>
  </router-link>
</template>

<style scoped>
.card {
  display: block;
  background: var(--color-surface);
  border-radius: 6px;
  overflow: hidden;
  box-shadow: var(--color-shadow);
  text-decoration: none;
  color: inherit;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
}

.card__flag-wrap {
  aspect-ratio: 320 / 192;
  overflow: hidden;
}

.card__flag {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.card__body {
  padding: 1.35rem 1.5rem 2.25rem;
}

.card__name {
  margin: 0 0 1rem;
  font-size: 1.05rem;
  font-weight: var(--fw-extrabold);
}

.card__dl {
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
}

.card__row {
  display: flex;
  flex-wrap: wrap;
  gap: 0.25rem 0.35rem;
  margin: 0;
}

.card__label {
  margin: 0;
  font-weight: var(--fw-semibold);
}

.card__value {
  margin: 0;
  font-weight: var(--fw-normal);
}
</style>