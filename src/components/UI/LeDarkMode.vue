<script setup>
import { SunIcon, MoonIcon } from '@heroicons/vue/24/outline'
import { useColorMode } from '@vueuse/core'

const mode = useColorMode({
  attribute: 'data-theme',
  modes: {
    light: 'light',
    dark: 'dark',
  },
  disableTransition: false,
})
</script>
<template>
  <div v-if="mode === 'light'">
    <button
      title="light mode"
      class="flex cursor-pointer flex-col items-center"
      @click="((mode = 'dark'), console.log(mode))"
    >
      <SunIcon class="hover:text-acc size-8 stroke-1 transition-all duration-75"></SunIcon>
    </button>
  </div>
  <div v-if="mode === 'dark'">
    <button
      title="dark mode"
      class="flex cursor-pointer flex-col items-center align-text-top"
      @click="((mode = 'light'), console.log(mode))"
    >
      <MoonIcon class="hover:text-acc size-7.5 stroke-1"></MoonIcon>
    </button>
  </div>
</template>

<style scoped></style>
