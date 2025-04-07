<script setup lang="ts">
import { onMounted, reactive, ref, type Component } from 'vue';
import ElementsPanel from './ElementsPanel.vue';
import ElementDisplay from './elements/ElementDisplay.vue';
import TextField from './elements/TextField.vue';
import CheckboxField from './elements/CheckboxField.vue'
import { Sortable } from '@shopify/draggable';

const drag = ref(false);

type FieldType = 'text' | 'checkbox';
interface Field {
    id: number;
    type: FieldType; // Ensure type matches keys of componentMap
    label: string;
}

const fields = reactive<Field[]>([
  { id: 1, label: "Text Field", type: "text" },
  { id: 2, label: "Other Text Field", type: "text" },
]); // Fields dropped into the Form Viewer

const componentMap: Record<FieldType, Component> = {
  text: TextField,
  checkbox: CheckboxField,
};

const draggableContainerRef = ref<HTMLElement | null>(null); // Reference to the draggable container

function addNewField(fieldType: FieldType, label: string) {
  const newId = fields.length > 0 ? Math.max(...fields.map(f => f.id)) + 1 : 1;
  fields.push({
    id: newId,
    type: fieldType,
    label: label
  });
}

onMounted(() => {
  // Initialize Sortable after the DOM has been rendered
  if (draggableContainerRef.value) {
    const sortable = new Sortable(draggableContainerRef.value, {
      draggable: '.sortable-item'
    });

    sortable.on('sortable:start', () => {
      console.log('Sorting started');
    });

    sortable.on('sortable:sort', () => {
      console.log('Sorting in progress');
    });

    sortable.on('sortable:sorted', (event) => {
      console.log('Sorted:', event);
      const oldIndex = event.oldIndex;
      const newIndex = event.newIndex;

      // Adjust the fields array accordingly
      const movedItem = fields.splice(oldIndex, 1)[0];
      fields.splice(newIndex, 0, movedItem);
    });
  }
});
</script>

<template>
  <div class="grid grid-cols-6 h-[100vh]">
    <!-- First Column -->
    <ElementsPanel 
      :draggable-container="draggableContainerRef" 
      class="col-span-1"
      @field-dropped="addNewField"
    ></ElementsPanel>

    <!-- Second Column -->
    <div class="col-span-4 p-4">
      <h3 class="text-lg font-bold">Form Viewer</h3>

      <div ref="draggableContainerRef" class="droppable-dropzone w-full flex flex-col gap-3">
        <component 
          v-for="field in fields" 
          :key="field.id" 
          :is="componentMap[field.type]" 
          :label="field.label"
          class="sortable-item"
        ></component>
      </div>      
    </div>
  </div>
</template>

<style scoped>
.sortable-item {
  cursor: grab;
}

.sortable-item--is-dragging {
  opacity: 0.5;
}

.droppable-dropzone--active {
  background-color: rgba(0, 0, 0, 0.05);
}
</style>
