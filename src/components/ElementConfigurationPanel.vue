<script setup lang="ts">
import TextField from './elements/TextField.vue';
import type { Field } from './FormBuilder.vue';

defineProps<{
    field: Field
}>()
</script>

<template>
  <div class="bg-gray-100 flex flex-col gap-3 p-4">
    <div class="text-lg font-bold mb-8 text-gray-800">Field Params</div>
    <TextField label="Field Label" v-model="field.label" placeholder="Field Label..."></TextField>
    <TextField label="Field Placeholder" v-model="field.placeholder" placeholder="Field Placeholder..."></TextField>
    <TextField label="Field Name" v-model="field.name" placeholder="Field Name..."></TextField>
  </div>
</template>

<style>

</style>