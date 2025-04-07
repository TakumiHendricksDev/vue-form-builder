<script setup lang="ts">
import { nextTick, onMounted, reactive, ref, onBeforeUnmount } from 'vue';
import ElementDisplay from './elements/ElementDisplay.vue';
import SearchField from './elements/SearchField.vue';
import { Droppable } from '@shopify/draggable';

const fields = reactive([
    { id: 1, label: "Text Field", type: "text" },
    { id: 2, label: "Checkbox Field", type: "checkbox" },
]);

const props = defineProps<{
  draggableContainer: HTMLElement | null;
}>();

const emit = defineEmits(['field-dropped']);
const panelContainerRef = ref<HTMLElement | null>(null);
let droppableInstance: any = null;

// Initialize after component is fully mounted
onMounted(() => {
  // Give enough time for all DOM elements to be ready
  setTimeout(() => {
    initializeDroppable();
  }, 100);
});

// Clean up when component is destroyed
onBeforeUnmount(() => {
  if (droppableInstance) {
    droppableInstance.destroy();
    droppableInstance = null;
  }
});

function initializeDroppable() {
  if (!panelContainerRef.value) {
    console.error('Panel container ref is not available');
    return;
  }
  
  // Use a direct reference instead of querySelector
  const container = panelContainerRef.value;
  const dropzone = document.querySelector('.droppable-dropzone');
  
  console.log('Container:', container);
  console.log('Dropzone:', dropzone);
  
  if (!container || !dropzone) {
    console.error('Required elements not found');
    return;
  }
  
  try {
    // Create the droppable instance with minimal configuration
    droppableInstance = new Droppable([container], {
      draggable: '.draggable-item',
      dropzone: '.droppable-dropzone',
    });
    
    console.log('Droppable initialized');
    
    // Add event listeners
    droppableInstance.on('droppable:dropped', (event) => {
      console.log('Item dropped:', event);
      const source = event.dragEvent.source;
      const fieldType = source.getAttribute('data-type');
      const fieldLabel = source.getAttribute('data-label');
      
      if (fieldType && fieldLabel) {
        emit('field-dropped', fieldType, fieldLabel);
      }
    });
    
    // Debug events
    droppableInstance.on('drag:start', () => console.log('Drag started'));
    droppableInstance.on('drag:stop', () => console.log('Drag stopped'));
  } catch (error) {
    console.error('Error initializing Droppable:', error);
  }
}
</script>

<template>
  <div class="bg-white flex flex-col gap-3 p-4">
    <SearchField placeholder="Type element name here"></SearchField>

    <div ref="panelContainerRef" class="w-full flex flex-col gap-3 elements-panel-container">
      <div 
        v-for="element in fields" 
        :key="element.id" 
        class="draggable-item"
        :data-type="element.type"
        :data-label="element.label"
      >
        <ElementDisplay :label="element.label"></ElementDisplay>
      </div>
    </div>
  </div>
</template>

<style>
/* Non-scoped styles to ensure they apply globally */
.draggable-item {
  cursor: grab;
  user-select: none;
}

.draggable-item:active {
  cursor: grabbing;
}

.draggable-mirror {
  opacity: 0.7;
  box-shadow: 0 5px 15px rgba(0,0,0,0.15);
  z-index: 100;
}
</style>