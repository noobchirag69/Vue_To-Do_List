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
