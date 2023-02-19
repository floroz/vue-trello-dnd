<script setup lang="ts">
import type { Column } from "@/types";
import { ref } from "vue";
import TrelloBoardTask from "./TrelloBoardTask.vue";
import { onClickOutside } from "@vueuse/core";

defineProps<{ column: Column }>();

const emit = defineEmits<{
  (e: "task:created", title: string, id: Column["id"]): void;
}>();

const inputRef = ref<HTMLInputElement | null>(null);
const newTaskTitle = ref("");
const isEditing = ref(false);

onClickOutside(inputRef, () => {
  disableEditing();
});

const onAddTask = () => {
  enableEditing();
};

const onAdded = (id: Column["id"]) => {
  emit("task:created", newTaskTitle.value, id);
  disableEditing();
  resetInput();
};

const disableEditing = () => (isEditing.value = false);
const enableEditing = () => (isEditing.value = true);
const resetInput = () => (newTaskTitle.value = "");
</script>

<template>
  <div class="bg-gray-200 p-5 rounded min-w-[250px]">
    <header>
      {{ column.title }}
    </header>

    <TrelloBoardTask v-for="task in column.tasks" :key="task.id" :task="task" />
    <footer>
      <input
        ref="inputRef"
        v-show="isEditing"
        v-model="newTaskTitle"
        @keyup.enter="onAdded(column.id)"
      />
      <button v-show="!isEditing" @click="onAddTask">+ Add Task</button>
    </footer>
  </div>
</template>