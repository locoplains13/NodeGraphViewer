<template>
  <svg height="200" width="300" xmlns="http://www.w3.org/2000/svg">
    <line
      id="line"
      x1="0"
      y1="0"
      x2="300"
      y2="200"
      style="stroke: white; stroke-width: 2"
    />
  </svg>
</template>

<script setup>
import { ref } from "vue";

const { coordinates } = ref({
  defineProps: { x1: 0, y1: 0, x2: 300, y2: 200 },
});
</script>
