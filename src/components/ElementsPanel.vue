<script setup lang="ts">
import { reactive, ref } from 'vue';
import ElementDisplay from './elements/ElementDisplay.vue';
import SearchField from './elements/SearchField.vue';

const fields = reactive([
    { id: 1, label: "Text Field", type: "text", name: "text field"},
    { id: 2, label: "Checkbox Field", type: "checkbox", name: "checkbox"},
]);

const emit = defineEmits(['field-dropped']);

// Handle drag start
function onDragStart(event: DragEvent, field: any) {
  console.log('Drag started', field);
  
  // Set data to be transferred
  if (event.dataTransfer) {
    event.dataTransfer.setData('application/json', JSON.stringify({
      type: field.type,
      label: field.label
    }));
    
    // Set drag effect and image
    event.dataTransfer.effectAllowed = 'copy';
    
    // You can also set a custom drag image if needed
    const dragImg = document.createElement('div');
    dragImg.textContent = field.label;
    dragImg.style.padding = '10px';
    dragImg.style.background = 'white';
    dragImg.style.width = '500px';
    dragImg.style.border = '1px solid #ccc';
    document.body.appendChild(dragImg);
    event.dataTransfer.setDragImage(dragImg, 0, 0);
    setTimeout(() => document.body.removeChild(dragImg), 0);
  }
}
</script>

<template>
  <div class="bg-gray-100 flex flex-col gap-3 p-4">
    <SearchField placeholder="Type element name here"></SearchField>

    <div class="w-full flex flex-col gap-3">
      <div 
        v-for="element in fields" 
        :key="element.id" 
        class="draggable-item"
        draggable="true"
        @dragstart="(e) => onDragStart(e, element)"
      >
        <ElementDisplay :label="element.label"></ElementDisplay>
      </div>
    </div>
  </div>
</template>

<style>
.draggable-item {
  cursor: grab;
  user-select: none;
}

.draggable-item:active {
  cursor: grabbing;
}
</style>