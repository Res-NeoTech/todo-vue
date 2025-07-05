<script setup lang="ts">
import { ref, computed, onMounted, watch, type ComputedRef, useTemplateRef } from 'vue';
import type { Task } from './assets/interfaces';
import TaskList from './components/TaskList.vue';
import TaskInput from './components/TaskInput.vue';

// Refs
const tasks = ref<Task[]>([]);
const currentFilter = ref<"all" | "completed" | "uncompleted">("all");

// Template Refs
const allButton = useTemplateRef<HTMLButtonElement>("filterAll");
const compButton = useTemplateRef<HTMLButtonElement>("filterCompleted");
const uncompButton = useTemplateRef<HTMLButtonElement>("filterUncompleted");

// Task Management
const onUpdateTask = (updatedTask: Task) => {
  const index = tasks.value.findIndex(t => t.id === updatedTask.id)
  if (index !== -1) {
    tasks.value[index] = updatedTask
  }
}

const deleteCompletedTasks = () => {
  tasks.value = tasks.value.filter((tasks) => !tasks.completed);
}

const onDeleteTask = (taskToDelete: Task) => {
  const deleteIndex = tasks.value.findIndex(t => t.id === taskToDelete.id)
  tasks.value.splice(deleteIndex, 1);
}

const onNewTask = (newTask: Task) => {
  tasks.value.push(newTask);
}

// Computed values
const nextId: ComputedRef<number> = computed(() => {
  if (tasks.value.length === 0) return 1
  return tasks.value[tasks.value.length - 1].id + 1
})

const remainingTasks: ComputedRef<number> = computed(() => {
  if (tasks.value.length === 0) return 0
  const completed: number = tasks.value.filter((task) => task.completed).length;
  return tasks.value.length - completed;
})

const filteredTasks: ComputedRef<Task[]> = computed(() => {
  switch (currentFilter.value) {
    case "all":
      return tasks.value;
    case "completed":
      return tasks.value.filter((task) => task.completed);
    case "uncompleted":
      return tasks.value.filter((task) => !task.completed);
  }
})

// Miscellaneous
onMounted(() => {
  const savedTasks: string | null = localStorage.getItem('tasks');
  if (savedTasks) tasks.value = JSON.parse(savedTasks);
  const savedFilter: string | null = localStorage.getItem('filter');
  if (savedFilter) currentFilter.value = JSON.parse(savedFilter);
})

watch(tasks, (newVal) => {
  localStorage.setItem('tasks', JSON.stringify(newVal))
}, { deep: true })

watch(currentFilter, (newVal) => {
  switch (newVal) {
    case "all":
      allButton.value?.classList.add("chosenFilter");
      compButton.value?.classList.remove("chosenFilter");
      uncompButton.value?.classList.remove("chosenFilter");
      break;
    case "completed":
      allButton.value?.classList.remove("chosenFilter");
      compButton.value?.classList.add("chosenFilter");
      uncompButton.value?.classList.remove("chosenFilter");
      break;
    case "uncompleted":
      allButton.value?.classList.remove("chosenFilter");
      compButton.value?.classList.remove("chosenFilter");
      uncompButton.value?.classList.add("chosenFilter");
      break;
  }

  localStorage.setItem('filter', JSON.stringify(newVal));
})
</script>

<template>
  <div>
    <h1>To-Do App</h1>
    <div id="sort">
      <button class="chosenFilter" ref="filterAll" @click="currentFilter = 'all'">All</button>
      <button ref="filterCompleted" @click="currentFilter = 'completed'">Completed</button>
      <button ref="filterUncompleted" @click="currentFilter = 'uncompleted'">Uncompleted</button>
    </div>
    <div id="summary" v-if="tasks.length > 0">
      <h2>{{ remainingTasks > 0 ? `Remaining Tasks : ${remainingTasks}` : "No Remaining Tasks" }}</h2>
      <button @click="deleteCompletedTasks">Delete Completed Tasks</button>
    </div>
    <TaskInput :new-id="nextId" @add-task="onNewTask" />
    <TaskList v-if="filteredTasks.length > 0" :tasks="filteredTasks" @update-task="onUpdateTask" @delete-task="onDeleteTask" />
    <h2 v-else>
      No tasks matching this filter.
    </h2>
  </div>
</template>

<style scoped>
h1, #sort, #summary {
  margin-bottom: 75px;
}

#sort, #summary {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 25px;
}

.chosenFilter {
  border: 1px solid #646cff;
  transition: 500ms all;
  filter: drop-shadow(0 0 10px #646cff);
}

@media (max-width: 768px) {
  #summary {
    flex-direction: column;
  }
}
</style>
