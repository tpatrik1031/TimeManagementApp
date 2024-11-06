<template>
    <div>
      <div class="flex my-2">
        <button class="btn-view" @click="currentView = 'daily'">
          Napi beosztás
        </button>
        <button class="btn-view" @click="currentView = 'weekly'">
          Heti beosztás
        </button>
        <button class="btn-view" @click="currentView = 'monthly'">
          Havi beosztás
        </button>
      </div>
      <div class="w-full">
        <Datepicker 
          v-if="currentView === 'daily'" 
          v-model="selectedDate" 
          placeholder="Válassza ki a napot" 
          type="date" 
        />
        <Datepicker 
          v-if="currentView === 'weekly'" 
          v-model="selectedWeek" 
          placeholder="Válassza ki a hetet" 
          type="date" 
        />
        <Datepicker 
          v-if="currentView === 'monthly'" 
          v-model="selectedMonth" 
          placeholder="Válassza ki a hónapot" 
          type="date" 
        />
      </div>
      <div v-if="currentView === 'daily'">
        <div class="flex justify-between my-4">
          <h2 class="font-bold">Napi beosztás {{ selectedDate ? selectedDate.toLocaleDateString() : ' ' }}</h2>
          <button class="btn-delete" @click="deleteAllTasks('daily')">
            Összes törlése
          </button>
        </div>
        <ul class="task-list">
          <TaskItem 
            v-for="task in filteredDailyTasks" 
            :key="task.id" 
            :task="task" 
            @edit="setTaskToEdit(task)" 
            @delete="deleteTask(task.id)"
          />
        </ul>
      </div>
      <div v-if="currentView === 'weekly'">
        <div class="flex justify-between my-4">
          <h2 class="font-bold">Heti beosztás {{ selectedWeek ? selectedWeek.toLocaleDateString() : ' ' }}</h2>
          <button class="btn-delete" @click="deleteAllTasks('weekly')">
            Összes törlése
          </button>
        </div>
        <ul class="task-list">
          <TaskItem 
            v-for="task in filteredWeeklyTasks" 
            :key="task.id" 
            :task="task" 
            @edit="setTaskToEdit(task)" 
            @delete="deleteTask(task.id)"
          />
        </ul>
      </div>
      <div v-if="currentView === 'monthly'">
        <div class="flex justify-between my-4">
          <h2 class="font-bold">Havi beosztás {{ selectedMonth ? selectedMonth.toLocaleDateString() : ' ' }}</h2>
          <button class="btn-delete" @click="deleteAllTasks('monthly')">
            Összes törlése
          </button>
        </div>
        <ul class="task-list">
          <TaskItem 
            v-for="task in filteredMonthlyTasks" 
            :key="task.id" 
            :task="task" 
            @edit="setTaskToEdit(task)" 
            @delete="deleteTask(task.id)"
          />
        </ul>
      </div>
    </div>
</template>
  
<script setup>
  import { ref, computed } from 'vue';
  import Datepicker from '@vuepic/vue-datepicker';
  import '@vuepic/vue-datepicker/dist/main.css';
  import TaskItem from './TaskItem.vue';
  
  const props = defineProps(['taskToEdit', 'tasks']);
  const emit = defineEmits(['add-task', 'edit-task', 'delete-task', 'delete-all-tasks']);
  
  const currentView = ref('daily');
  
  const selectedDate = ref(new Date());
  const selectedWeek = ref(new Date());
  const selectedMonth = ref(new Date());
  
  const filteredDailyTasks = computed(() => {
    if (!selectedDate.value) return [];
    const selectedDay = selectedDate.value.toLocaleDateString();
    return props.tasks.filter(task => new Date(task.date).toLocaleDateString() === selectedDay);
  });
  
  const filteredWeeklyTasks = computed(() => {
    if (!selectedWeek.value) return [];
    const startOfWeek = new Date(selectedWeek.value);
    startOfWeek.setDate(startOfWeek.getDate() - startOfWeek.getDay());
    const endOfWeek = new Date(startOfWeek);
    endOfWeek.setDate(startOfWeek.getDate() + 6);
  
    return props.tasks.filter(task => {
      const taskDate = new Date(task.date);
      return taskDate >= startOfWeek && taskDate <= endOfWeek;
    });
  });
  
  const filteredMonthlyTasks = computed(() => {
    if (!selectedMonth.value) return [];
    const startOfMonth = new Date(selectedMonth.value.getFullYear(), selectedMonth.value.getMonth(), 1);
    const endOfMonth = new Date(selectedMonth.value.getFullYear(), selectedMonth.value.getMonth() + 1, 0);
  
    return props.tasks.filter(task => {
      const taskDate = new Date(task.date);
      return taskDate >= startOfMonth && taskDate <= endOfMonth;
    });
  });
  
  const setTaskToEdit = (task) => {
    emit('edit-task', task);
  };
  
  const deleteTask = (id) => {
    emit('delete-task', id);
  };
  
  const deleteAllTasks = (view) => {
    let tasksToDelete;
    if (view === 'daily') {
      tasksToDelete = filteredDailyTasks.value;
    } else if (view === 'weekly') {
      tasksToDelete = filteredWeeklyTasks.value;
    } else if (view === 'monthly') {
      tasksToDelete = filteredMonthlyTasks.value;
    }
    emit('delete-all-tasks', tasksToDelete.map(task => task.id));
  };
</script>