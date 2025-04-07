<script setup lang="ts">
// Define props
defineProps<{
  label: string; // Label for the checkbox
  modelValue?: boolean; // For two-way binding
  name: string;
}>();

// Emit an event for two-way binding
const emit = defineEmits(['update:modelValue']);
</script>

<template>
  <div class="flex flex-col gap-3 items-start">
    <label class="text-gray-800 font-bold">
      <input
        class="border rounded border-gray-300 bg-white p-3 mr-2"
        type="checkbox"
        :checked="modelValue"
        :name=name
        @change="$emit('update:modelValue', $event.target.checked)"
      />
      {{ label }}
    </label>
  </div>
</template>