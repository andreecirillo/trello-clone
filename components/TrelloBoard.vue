<script setup lang="ts">

import type { Column, Task } from "~~/types"
import draggable from "vuedraggable"
import { nanoid } from "nanoid"

const columns = ref<Column[]>([
    {
        id: nanoid(),
        title: "Backlog",
        tasks: [
            {
                id: nanoid(),
                title: "Create marketing landing page",
                createdAt: new Date()
            },
            {
                id: nanoid(),
                title: "Develop cool new feature",
                createdAt: new Date()
            },
            {
                id: nanoid(),
                title: "Fix page nav bug",
                createdAt: new Date()
            }
        ]
    },
    { title: "Selected for Dev", id: nanoid(), tasks: [] },
    { title: "In Progress", id: nanoid(), tasks: [] },
    { title: "QA", id: nanoid(), tasks: [] },
    { title: "Complete", id: nanoid(), tasks: [] }
]);

const alt = useKeyModifier("Alt")

function createColumn() {
    const column: Column = {
        id: nanoid(),
        title: "",
        tasks: []
    };

    columns.value.push(column);

    nextTick(() => {
        (document.querySelector(".column:last-of-type .title-input") as HTMLInputElement).focus()
    });
}
</script>
<template>
    <div class="flex items-start overflow-x-auto gap-4">
        <draggable v-model="columns" group="columns" :animation="150" handle=".drag-handle" item-key="id"
            class="flex gap-4 items-start">
            <template #item="{ element: column }: { element: Column }">
                <div class="column bg-gray-200 p-5 rounded min-w-[250px] mb-20">
                    <header class="font-bold mb-4">
                        <DragHandle />
                        <input type="text"
                            class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5 focus:outline-none"
                            @keyup.enter="($event.target as HTMLInputElement).blur()" />
                        <button class="ml-2 text-red-600" title="Delete Column" @click="columns = columns.filter((c) => c.id !== column.id)">x</button>
                    </header>
                    <draggable v-model="column.tasks" :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
                        :animation="150" handle=".drag-handle" item-key="id">
                        <template #item="{ element: task }: { element: Task }">
                            <div>
                                <TrelloBoardTask :task="task"
                                    @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)" />
                            </div>
                        </template>
                    </draggable>
                    <footer>
                        <NewTask @add="column.tasks.push($event)" />
                    </footer>
                </div>
            </template>
        </draggable>
        <button @click="createColumn" class="bg-gray-200 whitespace-nowrap rounded opacity-50">
            + Add another Column
        </button>
    </div>
</template>
