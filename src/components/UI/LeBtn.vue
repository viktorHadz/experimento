<script setup>
defineProps({
  buttonText: String,
  type: String,
  disabled: Boolean,
})
</script>
<template>
  <button
    :disabled="disabled"
    class="le-btn"
    :class="{ 'cursor-not-allowed opacity-70': disabled }"
  >
    {{ buttonText }}
    <slot></slot>
  </button>
</template>
