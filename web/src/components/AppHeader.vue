<script setup>
defineProps({
  dark: { type: Boolean, default: false },
})

const emit = defineEmits(['toggle-theme'])
</script>

<template>
  <header class="header">
    <div class="header__inner">
      <h1 class="header__title"><router-link to="/">Where in the world?</router-link></h1>
      <button
        type="button"
        class="header__theme"
        :aria-pressed="dark"
        aria-label="Toggle dark mode"
        @click="emit('toggle-theme')"
      >
        <span class="header__theme-icon" aria-hidden="true">
          <i v-if="!dark" class="fa-regular fa-moon"></i>
          <i v-else class="fa-solid fa-sun"></i>
        </span>
        <span>{{ dark ? 'Light Mode' : 'Dark Mode' }}</span>
      </button>
    </div>
  </header>
</template>

<style scoped>
.header {
  background: var(--color-surface);
  box-shadow: var(--header-shadow);
}

.header__inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  max-width: 1320px;
  margin: 0 auto;
  padding: 1.35rem clamp(1rem, 4vw, 5rem);
}

.header__title {
  margin: 0;
  font-size: clamp(16px, 2vw, 22px);
  font-weight: var(--fw-extrabold);
}

.header__title a {
  text-decoration: none;
  color: inherit;
}

.header__theme {
  display: inline-flex;
  align-items: center;
  gap: 0.45rem;
  padding: 0.35rem 0.5rem;
  border: none;
  background: transparent;
  cursor: pointer;
  font-weight: var(--fw-semibold);
}

.header__theme:hover {
  opacity: 0.85;
}

.header__theme-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 1em;
  font-size: 1em; /* 跟按鈕文字使用相同字級 */
  line-height: 1;
}

.header__theme-icon i {
  font-size: inherit;
  line-height: 1;
}
</style>
