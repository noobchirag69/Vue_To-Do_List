<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import { library } from '@fortawesome/fontawesome-svg-core'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faTrash, faPlus } from '@fortawesome/free-solid-svg-icons'

const tasks = ref([])
const name = ref('')

const input_content = ref('')

const tasks_asc = computed(() => tasks.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(tasks, (newVal) => {
  localStorage.setItem('tasks', JSON.stringify(newVal))
}, {
  deep: true
})

const addTask = () => {
  tasks.value.push({
    content: input_content.value,
    createdAt: new Date().getTime()
  })

  input_content.value = ""
}

const removeTask = (task) => {
  tasks.value = tasks.value.filter((t) => t !== task)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
})

library.add(faTrash, faPlus)
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Welcome, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TASK</h3>

      <form id="new-todo-form" @submit.prevent="addTask">
        <h4>What's on your To-Do List?</h4>
        <input type="text" name="content" id="content" placeholder="e.g. Finish Homework" v-model="input_content"
          required />

        <button type="submit">
          <font-awesome-icon icon="fa-solid fa-plus" />
        </button>
      </form>
    </section>

    <section class="todo-list">
      <h3>TO-DO LIST</h3>
      <div class="list" id="todo-list">

        <div v-for="task in tasks_asc" class="todo-item">

          <div class="bubble"></div>

          <div class="todo-content">
            <input type="text" v-model="task.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTask(task)">
              <font-awesome-icon icon="fa-solid fa-trash" />
            </button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>

<style scoped>
section {
  margin-top: 2rem;
  margin-bottom: 2rem;
  padding-left: 1.5rem;
  padding-right: 1.5em;
}

h3 {
  color: var(--dark);
  font-size: 1rem;
  margin-bottom: 0.5rem;
}

h4 {
  color: var(--grey);
  font-size: 0.875rem;
  font-weight: 700;
  margin-bottom: 0.8rem;
}

.greeting .title {
  display: flex;
}

.greeting .title input {
  margin-left: 0.5rem;
  flex: 1 1 0%;
  min-width: 0;
}

.greeting .title,
.greeting .title input {
  color: var(--dark);
  font-size: 1.5rem;
  font-weight: 700;
}

.create-todo input[type="text"] {
  display: block;
  width: 100%;
  font-size: 1.125rem;
  padding: 1rem 1.5rem;
  color: var(--dark);
  background-color: #FFF;
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  margin-bottom: 1rem;
}

.bubble {
  width: 10px;
  height: 10px;
  margin: 0 1rem;
  border-radius: 50%;
  background-color: var(--business);
  box-shadow: var(--business-glow);
}

.create-todo button {
  width: 100%;
  font-size: 1.5rem;
  padding: 0.5rem;
  color: #FFF;
  background-color: var(--danger);
  border-radius: 0.5rem;
  box-shadow: var(--personal-glow);
  cursor: pointer;
  transition: 0.2s ease-in-out;
}

.create-todo button:hover {
  opacity: 0.75;
}

.todo-list .list {
  margin: 1rem 0;
}

.todo-list .todo-item {
  display: flex;
  align-items: center;
  background-color: #FFF;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  margin-bottom: 1rem;
}

.todo-item .todo-content {
  flex: 1 1 0%;
}

.todo-item .todo-content input {
  color: var(--dark);
  font-size: 1.125rem;
}

.todo-item .actions {
  display: flex;
  align-items: center;
}

.todo-item .actions button {
  display: block;
  color: var(--danger);
  font-size: 1.2rem;
  cursor: pointer;
  transition: 0.2s ease-in-out;
  margin-right: 0.5rem;
}

.todo-item .actions button:hover {
  opacity: 0.75;
}
</style>