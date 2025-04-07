<script setup lang="ts">
import TextField from './elements/TextField.vue';

defineProps<{
    field: object
}>()
</script>

<template>
  <div class="bg-white flex flex-col gap-3 p-4">
    <TextField label="Field Name" v-model="field.label" placeholder="Field Name..."></TextField>
    <TextField label="Field Placeholder" v-model="field.placeholder" placeholder="Field Placeholder..."></TextField>
  </div>
</template>

<style>

</style>