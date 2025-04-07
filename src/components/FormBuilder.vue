<script setup lang="ts">
import { onMounted, reactive, ref, type Component } from 'vue';
import ElementsPanel from './ElementsPanel.vue';
import TextField from './elements/TextField.vue';
import CheckboxField from './elements/CheckboxField.vue';
import ElementConfigurationPanel from './ElementConfigurationPanel.vue';

type FieldType = 'text' | 'checkbox';
interface Field {
    id: number;
    type: FieldType;
    label: string;
    placeholder?: string;
}

const fields = reactive<Field[]>([]); // Fields dropped into the Form Viewer
const dragOverIndex = ref(-1);
const selectedField = ref(null)

const componentMap: Record<FieldType, Component> = {
  text: TextField,
  checkbox: CheckboxField,
};

const formContainerRef = ref<HTMLElement | null>(null);

// Add a new field at the end
function addNewField(fieldType: FieldType, label: string) {
  const newId = fields.length > 0 ? Math.max(...fields.map(f => f.id)) + 1 : 1;
  fields.push({
    id: newId,
    type: fieldType,
    label: label,
    placeholder: label
  });
}

// Add a field at a specific position
function addNewFieldAt(fieldType: FieldType, label: string, index: number) {
  const newId = fields.length > 0 ? Math.max(...fields.map(f => f.id)) + 1 : 1;
  const newField = {
    id: newId,
    type: fieldType,
    label: label,
    placeholder: label
  };
  
  fields.splice(index, 0, newField);
}

// Reordering functionality
function reorderField(fromIndex: number, toIndex: number) {
  if (fromIndex === toIndex) return;
  
  // Clone to avoid reactivity issues
  const fieldsCopy = [...fields];
  const itemToMove = fieldsCopy[fromIndex];
  
  // Remove item
  fieldsCopy.splice(fromIndex, 1);
  
  // Calculate correct insertion point
  const adjustedIndex = fromIndex < toIndex ? toIndex - 1 : toIndex;
  
  // Insert at new position
  fieldsCopy.splice(adjustedIndex, 0, itemToMove);
  
  // Update fields - do in batch for better reactivity
  for (let i = 0; i < fieldsCopy.length; i++) {
    fields[i] = fieldsCopy[i];
  }
}

// Start dragging an existing field (reordering)
function onReorderDragStart(event: DragEvent, field: Field, index: number) {
  if (event.dataTransfer) {
    event.dataTransfer.setData('application/json', JSON.stringify({
      type: field.type,
      label: field.label,
      id: field.id,
      isReordering: true,
      originalIndex: index
    }));
    
    event.dataTransfer.effectAllowed = 'move';
  }
}

// Handle dragging over the form area
function onDragOver(event: DragEvent, index?: number) {
  event.preventDefault();
  
  // Add visual styling to container
  if (formContainerRef.value) {
    formContainerRef.value.classList.add('dragover');
  }
  
  // Update position indicator
  if (index !== undefined) {
    dragOverIndex.value = index;
  } else {
    // If dragging over empty space, place at end
    dragOverIndex.value = fields.length;
  }
}

// Handle drag leaving the form area
function onDragLeave(event: DragEvent) {
  // Only handle if actually leaving the container, not moving between children
  if (event.target === formContainerRef.value) {
    formContainerRef.value?.classList.remove('dragover');
    dragOverIndex.value = -1;
  }
}

// Handle dropping an item
function onDrop(event: DragEvent) {
  event.preventDefault();
  
  // Remove styling
  formContainerRef.value?.classList.remove('dragover');
  
  if (event.dataTransfer) {
    try {
      const data = JSON.parse(event.dataTransfer.getData('application/json'));
      
      if (data.isReordering) {
        // Handle reordering
        reorderField(data.originalIndex, dragOverIndex.value);
      } else if (data.type && data.label) {
        // Handle new item
        if (dragOverIndex.value >= 0) {
          addNewFieldAt(data.type, data.label, dragOverIndex.value);
        } else {
          addNewField(data.type, data.label);
        }
      }
    } catch (error) {
      console.error('Error parsing drop data:', error);
    }
  }
  
  // Reset drag indicator
  dragOverIndex.value = -1;
}
</script>

<template>
  <div class="grid grid-cols-6 h-[100vh]">
    <!-- First Column -->
    <ElementsPanel class="col-span-1"></ElementsPanel>

    <!-- Second Column -->
    <div class="col-span-3 p-4">
      <div class="text-lg font-bold mb-8">Form Viewer</div>

      <div 
        ref="formContainerRef" 
        class="droppable-dropzone w-full flex flex-col gap-3 p-4 border-2 border-dashed border-gray-300 rounded-md min-h-[100px]"
        @dragover="onDragOver($event)"
        @dragleave="onDragLeave"
        @drop="onDrop"
      >
        <!-- First position indicator -->
        <div 
          v-if="dragOverIndex === 0" 
          class="h-2 bg-blue-500 w-full rounded transition-all"
        ></div>
        
        <template v-for="(field, index) in fields" :key="field.id">
          <div 
            class="sortable-item-wrapper"
            :class="selectedField != null && selectedField.id === field.id ? 'border border-3 p-2 rounded border-indigo-300' : ''"
            draggable="true"
            @dragstart="(e) => onReorderDragStart(e, field, index)"
            @dragover.stop="onDragOver($event, index + 1)"
            @click="selectedField = field"
          >
            <component 
              :is="componentMap[field.type]" 
              :label="field.label"
              :placeholder="field.placeholder"
              class="sortable-item"
            />
          </div>
          
          <!-- Position indicator after this item -->
          <div 
            v-if="dragOverIndex === index + 1" 
            class="h-2 bg-blue-500 w-full rounded transition-all my-1"
          ></div>
        </template>
        
        <!-- Empty state message -->
        <div v-if="fields.length === 0" class="text-center text-gray-500 py-8">
          Drag elements here to build your form
        </div>
      </div>
    </div>

    <!-- Third Column -->
    <ElementConfigurationPanel v-if="selectedField" :field="selectedField"></ElementConfigurationPanel>
  </div>
</template>

<style>
.droppable-dropzone {
  transition: all 0.2s ease;
}

.droppable-dropzone.dragover {
  background-color: rgba(59, 130, 246, 0.05);
  border-color: rgba(59, 130, 246, 0.5);
}

.sortable-item-wrapper {
  cursor: grab;
  position: relative;
  transition: transform 0.1s, box-shadow 0.1s;
  border-radius: 4px;
}

.sortable-item-wrapper:hover {
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.sortable-item-wrapper:active {
  cursor: grabbing;
  transform: scale(1.01);
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
</style>