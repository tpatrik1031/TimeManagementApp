<template>
  <MainLayout>
    <main class="min-h-screen flex items-center justify-center bg-gradient-to-r from-gray-800 to-gray-900 text-white p-4">
      <ConfirmModal
        :show="showConfirmModal"
        @confirm="confirmDeleteAllTasks"
        @cancel="showConfirmModal = false"
      />
      <div v-if="showModal" class="absolute w-full h-full bg-black bg-opacity-50 z-20 flex items-center justify-center">
        <div class="w-11/12 md:w-2/3 lg:w-1/2 bg-gray-800 border border-gray-700 p-6 rounded-lg shadow-lg relative">
          <div class="flex justify-between items-center mb-4">
            <h2 class="text-xl font-bold text-white">Töltse ki!</h2>
            <button @click="closeModal" class="text-gray-400 hover:text-gray-200">
              <CloseIcon />
            </button>
          </div>
          <TaskForm 
            :taskToEdit="taskToEdit" 
            @add-task="addTask" 
            @edit-task="updateTask" 
            @close-modal="closeModal" 
          />
        </div>
      </div>
      <div class="max-w-screen-lg w-full p-6 md:p-12 m-auto bg-gray-800 bg-opacity-90 rounded-lg shadow-lg text-center">
        <header class="flex flex-col gap-4 md:flex-row lg:flex-row justify-between items-center mb-6">
          <h1 class="text-3xl md:text-4xl font-extrabold text-white tracking-tight">Vágjon bele!</h1>
          <button @click="openCreateModal" class="btn-primary">
            Létrehozás
          </button>
        </header>
        <p v-if="message" :class="messageType === 'success' ? 'text-green-400' : 'text-red-500'" class="mb-4">
          {{ message }}
        </p>
        <div class="mt-4">
          <TaskList 
            :tasks="tasks" 
            @delete-task="deleteTask" 
            @edit-task="setTaskToEdit"
            @delete-all-tasks="openConfirmModal"
          />
        </div>
      </div>
    </main>
  </MainLayout>
  <Footer />
</template>

<script setup>
  import { ref, watch, onMounted } from 'vue';
  import TaskForm from '../src/components/TaskForm.vue';
  import TaskList from '../src/components/TaskList.vue';
  import CloseIcon from '../src/components/icons/CloseIcon.vue';
  import MainLayout from '../src/layout/MainLayout.vue';
  import ConfirmModal from '../src/components/ConfirmModal.vue';
  import Footer from './components/Footer.vue';

  const tasks = ref([]);
  const taskToEdit = ref(null);
  const message = ref('');
  const messageType = ref('success');
  const showModal = ref(false);
  const showConfirmModal = ref(false);
  const tasksToDelete = ref([]);

  onMounted(() => {
    const savedTasks = JSON.parse(localStorage.getItem('tasks')) || [];
    tasks.value = savedTasks;
  });

  watch(tasks, (newTasks) => {
    localStorage.setItem('tasks', JSON.stringify(newTasks));
  }, { deep: true });

  const showMessage = (msg, type = 'success') => {
    message.value = msg;
    messageType.value = type;
    setTimeout(() => {
      message.value = '';
    }, 3000);
  };

  const generateId = () => Date.now().toString();

  const addTask = (task) => {
    task.id = generateId();
    tasks.value.push(task);
    showMessage('Sikeres létrehozás');
  };

  const deleteTask = (id) => {
    tasks.value = tasks.value.filter(task => task.id !== id);
    showMessage('Sikeres törlés');
  };

  const updateTask = (updatedTask) => {
    const index = tasks.value.findIndex(task => task.id === updatedTask.id);
    if (index !== -1) {
      tasks.value[index] = updatedTask;
      taskToEdit.value = null;
      showMessage('Sikeres szerkesztés');
    } else {
      showMessage('Nem található bejegyzés', 'hiba');
    }
  };

  const setTaskToEdit = (task) => {
    taskToEdit.value = task;
    showModal.value = true;
  };

  const openCreateModal = () => {
    taskToEdit.value = null;
    showModal.value = true;
  };

  const closeModal = () => {
    showModal.value = false;
    taskToEdit.value = null;
  };

  const openConfirmModal = (taskIds) => {
    tasksToDelete.value = taskIds;
    showConfirmModal.value = true;
  };

  const confirmDeleteAllTasks = () => {
    tasks.value = tasks.value.filter(task => !tasksToDelete.value.includes(task.id));
    showConfirmModal.value = false;
    showMessage('Sikeresen törölte az összeset');
  };
</script>