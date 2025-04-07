<script setup lang="ts">
defineProps<{
  label: string
}>()
</script>

<template>
  <div class="flex flex-col gap-3 items-start">
    <label class="text-gray-800 font-bold">{{label}}</label>
    <input class="border rounded border-gray-300 bg-white p-3" type="checkbox">
  </div>
</template>
