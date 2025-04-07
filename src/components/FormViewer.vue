<script setup lang="ts">
import TextField from './elements/TextField.vue';

defineProps<{
}>()
</script>

<template>
  <div class="bg-gray-100 p-4">
    <TextField label="Test" placeholder="This is a test..." name="text field"></TextField>
  </div>
</template>
