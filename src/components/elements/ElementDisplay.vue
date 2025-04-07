<script setup lang="ts">
defineProps<{
  label: string;
}>();
</script>

<template>
  <div class="border border-gray-300 rounded p-3 bg-white">
    {{ label }}
  </div>
</template>
