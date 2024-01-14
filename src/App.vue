<template>
  <div>
    <h1>Todos</h1>
    <h2>Create a new item in {{ taskRepo.metadata.caption }}</h2>
    <main>
      <form @submit.prevent="addTask()">
        <input v-model="newTaskTitle" placeholder="What needs to be done?" />
        <button>Add</button>
      </form>
      <div v-for="task in tasks">
        <input type="checkbox" v-model="task.completed" />
        {{ task.title }}
      </div>
    </main>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref } from "vue";
import { remult } from "remult";
import { Task } from "./shared/Task";

// Remult Repository object use to fetch and create
// Task entity object
const taskRepo = remult.repo(Task);
//  Created a reactive task
const tasks = ref<Task[]>([]);
//  populated tasks
//  find date of type taskRepo  then tasks.value =eaach item in task repo
onMounted(() =>
  taskRepo
    .find({
      limit: 20,
      orderBy: { createdAt: "asc" },
      // server side filtering
      // where: { completed: true },
    })
    .then((items) => (tasks.value = items))
);

//Adding new tasks
const newTaskTitle = ref("");

async function addTask() {
  try {
    const newTask = await taskRepo.insert({
      title: newTaskTitle.value,
    });
    tasks.value.push(newTask);
    newTaskTitle.value = "";
  } catch (error) {
    alert((error as { message: string }).message);
  }
}
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
