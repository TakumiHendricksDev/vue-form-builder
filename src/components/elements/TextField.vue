<script setup lang="ts">
defineProps<{
  label: string
  placeholder: string | undefined
}>()
</script>

<template>
  <div class="flex flex-col gap-3">
    <label class="text-gray-800 font-bold">{{label}}</label>
    <input class="border rounded border-gray-300 bg-white p-3" type="text" :placeholder="placeholder">
  </div>
</template>
