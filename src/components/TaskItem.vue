<template>
  <div class="max-w-sm p-6 bg-gradient-to-r from-gray-800 to-gray-700 border border-gray-700 rounded-lg shadow-lg text-white">
    <span>
      <h5 class="text-lg font-bold text-white">{{ task.startTime }} - {{ task.endTime }}</h5>
    </span>
    <p class="my-2 text-sm tracking-tight text-gray-400">{{ task.date }}</p>
    <p class="mb-2 font-semibold text-blue-400">{{ tagName }}</p>
    <p class="mb-4 font-normal text-gray-300">Leírás: {{ task.description }}</p>
    <div class="flex justify-between items-center mt-4">
      <button @click="$emit('edit')" class="p-2 bg-blue-500 rounded-full hover:bg-blue-600 transition-transform transform hover:scale-105">
        <EditIcon class="text-white" />
      </button>
      <button @click="$emit('delete')" class="p-2 bg-red-500 rounded-full hover:bg-red-600 transition-transform transform hover:scale-105">
        <DeleteIcon class="text-white" />
      </button>
    </div>
  </div>
</template>
 
<script setup>
  import DeleteIcon from './icons/DeleteIcon.vue';
  import EditIcon from './icons/EditIcon.vue';
  import { computed } from 'vue';
  
  const props = defineProps({
    task: {
      type: Object,
      required: true,
    },
  });
  
  const tagName = computed(() => {
    const tagMapping = {
      project: 'Projekt',
      customer: 'Ügyfél',
      other: 'Egyéb',
    };
    return tagMapping[props.task.tag] || props.task.tag;
  });
</script>