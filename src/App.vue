<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const tasks = ref([])
  const name = ref('')

  const input_content = ref('')
  const input_category = ref(null)

  const tasks_asc = computed(() => tasks.value.sort((a, b) => {
    return a.createdAt - b.createdAt
  }))

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })

  watch(tasks, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, {
    deep: true
  })

  const addTask = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
      return
    }

    tasks.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      editable: false,
      createdAt: new Date().getTime()
    })
  }

  const removeTask = (task) => {
    tasks.value = tasks.value.filter((t) => t !== task)
  }

  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
  })
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
        <input type="text" name="content" id="content" placeholder="e.g. Finish Homework" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input type="radio" name="category" id="category1" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

        </div>

        <input type="submit" value="Add +" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TO-DO LIST</h3>
      <div class="list" id="todo-list">

        <div v-for="task in tasks_asc" :class="`todo-item ${task.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="task.done" />
            <span :class="`bubble ${task.category == 'business'
              ? 'business'
              : 'personal'
            }`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="task.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTask(task)">Delete</button>
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
    margin-bottom: 0.5rem;
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
    margin-bottom: 1.5rem;
  }

  .create-todo .options {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-gap: 1rem;
    margin-bottom: 1.5rem;
  }

  .create-todo .options label {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 1.5rem;
    background-color: #FFF;
    border-radius: 0.5rem;
    box-shadow: var(--shadow);
    cursor: pointer;
  }

  input[type="radio"],
  input[type="checkbox"] {
    display: none;
  }

  .bubble {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 2px solid var(--business);
    box-shadow: var(--business-glow);
  }

  .bubble.personal {
    border-color: var(--personal);
    box-shadow: var(--personal-glow);
  }

  .bubble::after {
    content: "";
    display: block;
    opacity: 0;
    width: 0px;
    height: 0px;
    background-color: var(--business);
    box-shadow: var(--business-glow);
    border-radius: 50%;
    transition: 0.2s ease-in-out;
  }

  .bubble.personal::after {
    background-color: var(--personal);
    box-shadow: var(--personal-glow);
  }

  input:checked ~ .bubble::after {
    width: 10px;
    height: 10px;
    opacity: 1;
  }

  .create-todo .options label div {
    color: var(--dark);
    font-size: 1.125rem;
    margin-top: 1rem;
  }

  .create-todo input[type="submit"] {
    display: block;
    width: 100%;
    font-size: 1.2rem;
    padding: 1rem 1.5rem;
    color: #FFF;
    background-color: var(--danger);
    border-radius: 0.5rem;
    box-shadow: var(--personal-glow);
    cursor: pointer;
    transition: 0.2s ease-in-out;
  }

  .create-todo input[type="submit"]:hover {
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

  .todo-item label {
    display: block;
    margin-right: 1rem;
    cursor: pointer;
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
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
    color: #FFF;
    font-size: 0.9rem;
    cursor: pointer;
    transition: 0.2s ease-in-out;
  }

  .todo-item .actions button:hover {
    opacity: 0.75;
  }

  .todo-item .actions .edit {
    margin-right: 0.5rem;
    background-color: var(--primary);
  }

  .todo-item .actions .delete {
    background-color: var(--danger);
  }

  .todo-item.done .todo-content input {
    text-decoration: line-through;
    color: var(--grey);
  }
</style>