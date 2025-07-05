<script setup lang="ts">
import { useTemplateRef } from 'vue';
import type { Task } from '../assets/interfaces';

const props = defineProps<{ newId: number }>()

const input = useTemplateRef<HTMLInputElement>('taskInput')

const emit = defineEmits<{
    (e: 'add-task', newTask: Task): void
}>();

const addTask = () => {
    if (input.value) {
        const taskText: string = input.value.value;
        if (taskText.trim() !== "") {
            input.value.value = "";
            const newTask: Task = { id: props.newId, text: taskText, completed: false };
            emit("add-task", newTask);
        }
    }
};
</script>

<template>
    <div id="inputDiv">
        <input type="text" name="taskInput" id="taskInput" ref="taskInput" autofocus @keyup.enter="addTask">
        <button type="button" @click="addTask">Add</button>
    </div>
</template>

<style scoped>
#inputDiv {
    margin-bottom: 75px;
}

#inputDiv>input {
    margin-right: 25px;
    width: 300px;
    cursor: text;
}

@media (max-width: 768px) {
    #inputDiv>input {
        width: 200px;
    }
}
</style>