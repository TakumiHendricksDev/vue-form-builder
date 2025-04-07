<script setup lang="ts">
import { ref } from 'vue';

// Define props
defineProps<{
  label: string;
  placeholder?: string; // Optional placeholder
  modelValue?: string; // For two-way binding
  name?: string;
}>();

// Emit an event for two-way binding
const emit = defineEmits(['update:modelValue']);
</script>

<template>
  <div class="flex flex-col gap-3">
    <label class="text-gray-800 font-bold">{{ label }}</label>
    <input
      class="border rounded border-gray-300 bg-white p-3"
      type="text"
      :placeholder="placeholder"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </div>
</template>