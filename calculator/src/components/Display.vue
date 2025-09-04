<template>
  <div id="display">{{ 0.00 || result }}</div>
</template>
<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  result: {
    type: String,
    required: true,
  },
})
</script>