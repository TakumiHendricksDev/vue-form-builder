<script setup lang="ts">
import { faSearch } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

defineProps<{
  placeholder: string | undefined
}>()
</script>

<template>
  <div class="flex flex-col gap-3 relative">
    <input class="border rounded border-gray-300 bg-white p-3" type="text" :placeholder="placeholder">
    <FontAwesomeIcon :icon="faSearch" class="absolute right-4 top-4" />
  </div>
</template>
