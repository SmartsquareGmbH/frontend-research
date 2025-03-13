<script setup lang="ts">
import { useMouse } from '@vueuse/core'
const { x, y } = useMouse()
</script>

<template>
  <div>
    <span>X: {{ x }}</span>
    <span>Y: {{ y }}</span>
  </div>
</template>
