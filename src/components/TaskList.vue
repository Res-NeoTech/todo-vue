<script setup lang="ts">
import TaskItem from './TaskItem.vue'
import type { Task } from '../assets/interfaces';

const props = defineProps<{ tasks: Task[] }>()

const emit = defineEmits<{
  (e: 'update-task', updatedTask: Task): void
  (e: 'delete-task', deletedTask: Task): void
}>()

const toggleCompleted = (task: Task) => {
  emit('update-task', { ...task, completed: !task.completed })
}

const handleDelete = (task: Task) => {
  emit('delete-task', { ...task })
}
</script>

<template>
  <div id="task-list">
    <TaskItem v-for="task in props.tasks" :key="task.id" :task="task" @update-task="() => toggleCompleted(task)"
      @delete-task="() => handleDelete(task)" />
  </div>
</template>

<style scoped>
#task-list {
  display: flex;
  justify-content: space-evenly;
  flex-wrap: wrap;
  gap: 50px;
}
</style>