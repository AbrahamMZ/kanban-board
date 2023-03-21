<script setup lang="ts">
import type { Column, Task } from "~~/types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";

const columns = useLocalStorage<Column[]>("kanbasStorage", [
  {
    id: nanoid(),
    title: "Backlog",
    tasks: [
      { id: nanoid(), title: "Crear una Tarea 1", createdAt: new Date() },
      { id: nanoid(), title: "Crear una Tarea 2", createdAt: new Date() },
      { id: nanoid(), title: "Crear una Tarea 3", createdAt: new Date() },
    ],
  },
  {
    id: nanoid(),
    title: "In Progress",
    tasks: [],
  },
  {
    id: nanoid(),
    title: "Q/A",
    tasks: [],
  },
]);
watch(
  columns,
  () => {
    // TODO::Ajax Request
  },
  {
    deep: true,
  }
);
const alt = useKeyModifier("Alt");
function createColumn() {
  const column: Column = {
    id: nanoid(),
    title: "",
    tasks: [],
  };

  columns.value.push(column);
  nextTick(() => {
    (
      document.querySelector(
        ".column:last-of-type .title-input"
      ) as HTMLInputElement
    ).focus();
  });
}
</script>

<template>
  <div class="flex gap-4 overflow-x-auto items-start">
    <draggable
      v-model="columns"
      group="columns"
      item-key="id"
      :animation="150"
      handle=".drag-handle"
      class="flex gap-4 overflow-x-auto items-start"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-200 p-5 min-w-[250px] rounded">
          <header class="font-bold mb-4">
            <DraggHandle />
            <input
              v-model="column.title"
              type="text"
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id !== column.id))
                  : null
              "
            />
          </header>
          <draggable
            v-model="column.tasks"
            :group="{ name: 'task', pull: alt ? 'clone' : true }"
            item-key="id"
            :animation="150"
            handle=".drag-handle"
          >
            <template #item="{ element: task }: { element: Task }">
              <div>
                <KanbasBoardTask
                  :task="task"
                  @delete="
                    column.tasks = column.tasks.filter((t) => t.id !== $event)
                  "
                />
              </div>
            </template>
          </draggable>
          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add new Column
    </button>
  </div>
</template>
