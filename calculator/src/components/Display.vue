<template>
  <div v-bind:class="dynamicClass" id="display">{{ 0.00 || resultRef }}</div>
</template>

<script setup>
import { toRef, ref, defineProps, computed } from 'vue'
const props = defineProps({
  result: {
    type: String,
    required: true,
  },
});
// reactive reference to a specific prop that updates when the prop changes
const resultRef = toRef(props,'result');// Now resultRef.value will update if props.result changes
const dynamicClass = computed(() => {
  if ((resultRef.value?.length ?? 0) > 10) {
    return 'small-text';
  } else {
    return'large-text';
  }
});
</script>