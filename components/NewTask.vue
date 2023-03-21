<script setup lang="ts">
import type { Task } from "@/types";
import { nanoid } from "nanoid";

const emit = defineEmits<{
    (e: "add", payload: Task): void
}>()

const focused = ref(false)
const title = ref("")

function createTask(e: Event) {
    if (title.value.trim()) {
        e.preventDefault();
        emit('add', {
            title: title.value.trim(),
            id: nanoid(),
            createdAt: new Date()
        } as Task)
    }
    title.value = ""
}

</script>

<template>
    <div>
        <textarea v-model="title" @keydown.tab="createTask" @keydown.enter="createTask"
            class="focus:bg-white focus:shadow resize-none rounded w-full border-r cursor-pointer pl-1" :class="{
                'h-7': !focused,
                'h-20': focused,
            }" style="outline: none !important;" @focus="focused = true" @blur="focused = false"
            :placeholder="!focused ? '+ Add a Card' : 'Enter a Title for this Card'"></textarea>
    </div>
</template>