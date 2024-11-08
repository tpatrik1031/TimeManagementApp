<template>
  <form @submit.prevent="submitTask">
    <label for="date" class="block mb-2 font-medium text-white">Dátum:</label>
    <div class="flex my-2 gap-2 items-center">
      <VueDatePicker id="date" type="datetime" placeholder="Válassza ki az időkeretet..." v-model="date" range required/>
    </div>
    <div class="flex-col items-center">
      <label for="tag" class="block mb-2 font-medium text-white">Címke:</label>
      <select id="tag" class="bg-purple-50 border border-purple-300 text-purple-900 text-sm rounded-lg focus:ring-purple-500 focus:border-purple-500 block w-full p-2.5" v-model="taskTag" required>
        <option value="">Válasszon címkét</option>
        <option value="project">Projekt</option>
        <option value="customer">Ügyfél</option>
        <option value="other">Egyéb</option>
      </select>
    </div>
    <div class="flex-col gap-2 my-2">
      <label for="description" class="block mb-2 font-medium text-white">Leírás:</label>
      <textarea id="description" class="block p-2.5 w-full text-sm text-purple-900 bg-purple-50 rounded-lg border border-purple-300 focus:ring-purple-500 focus:border-purple-500" v-model="taskDescription" placeholder="Leírás..."></textarea>
    </div>
    <div class="flex flex-col md:flex-row lg:flex-row justify-between mt-4 mb-2">
      <p class="text-red-600">{{ errorMessage }}</p>
      <button class="btn-primary" type="submit">
        {{ isEditing ? 'Szerkesztés' : 'Hozzáadás' }}
      </button>
    </div>
  </form>
</template>
  
<script setup>
  import { ref, watch } from 'vue';

  const props = defineProps({
    taskToEdit: Object,
  });

  const emit = defineEmits(['add-task', 'edit-task', 'close-modal']);

  const date = ref([]);
  const taskDescription = ref('');
  const taskTag = ref('');
  const customTag = ref('');
  const isEditing = ref(false);
  const errorMessage = ref('');

  watch(
    () => props.taskToEdit,
    (newTask) => {
      if (newTask) {
        date.value = [
          new Date(`${newTask.date} ${newTask.startTime}`),
          new Date(`${newTask.date} ${newTask.endTime}`)
        ];
        taskDescription.value = newTask.description;
        taskTag.value = newTask.tag || '';
        isEditing.value = true;
      }
    },
    { immediate: true }
  );

  const resetForm = () => {
    date.value = [];
    taskDescription.value = '';
    taskTag.value = '';
    customTag.value = '';
    isEditing.value = false;
    errorMessage.value = '';
  };

  const validateDates = () => {
    if (date.value.length === 2 && date.value[1] <= date.value[0]) {
      errorMessage.value = "Befejezési dátumnak a kezdési dátum után kell lennie.";
      return false;
    }
    errorMessage.value = '';
    return true;
  };

  const submitTask = () => {
    if (!validateDates()) return;

    const task = {
      id: isEditing.value ? props.taskToEdit.id : Date.now(),
      date: date.value[0].toLocaleDateString(),
      startTime: date.value[0].toLocaleTimeString(),
      endTime: date.value[1].toLocaleTimeString(),
      description: taskDescription.value,
      tag: taskTag.value === 'other' ? customTag.value : taskTag.value,
    };
    emit(isEditing.value ? 'edit-task' : 'add-task', task);
    emit('close-modal');
    resetForm();
  };
</script>