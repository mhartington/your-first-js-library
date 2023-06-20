<script setup lang="ts">
import { computed } from 'vue';
const props = defineProps({
  background: {
    default: '',
  },
});
</script>

<template>
  <div class="slidev-layout default">
    <slot />
  </div>
</template>
