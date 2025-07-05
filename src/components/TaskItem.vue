<script setup lang="ts">
import type { Task } from '../assets/interfaces'

const props = defineProps<{ task: Task }>()

const emit = defineEmits<{
  (e: 'update-task', updatedTask: Task): void
  (e: 'delete-task', deletedTask: Task): void
}>()

const toggleCompleted = () => {
  emit('update-task', { ...props.task, completed: !props.task.completed })
}

const handleDelete = () => {
  emit('delete-task', { ...props.task })
}

</script>

<template>
  <div :id="`task-${props.task.id}`" :class="`completed-${task.completed}`" >
    <h1>Task #{{ task.id }}</h1>
    <p>{{ task.text }}</p>
    <button type="button" @click="toggleCompleted">
      {{ task.completed ? "✅ Completed" : "❌ Not Completed" }}
    </button>
    <button type="button" @click="handleDelete">🗑️ Delete</button>
  </div>
</template>

<style scoped>
div {
  background-color: #1a1a1a;
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 8px;
  width: 350px;
}

.completed-true {
  border: 1px solid #42b883;
  transition: 500ms all;
  filter: drop-shadow(0 0 15px #42b883);
}

.completed-false {
  border: 1px solid #646cff;
  transition: 500ms all;
  filter: drop-shadow(0 0 15px #646cff);
}

button {
  width: 100%;
}

h1 {
  font-size: xx-large;
}
</style>