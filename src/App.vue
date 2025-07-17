<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.created_at - a.created_at
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    created_at: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input id="name" v-model="name" placeholder="Name" type="text"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          v-model="input_content" 
          placeholder="e.g. make a game" 
          type="text"
          id="content"
          name="content"/>

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input
              type="radio" 
              name="category"
              id="category1"
              value="business"
              v-model="input_category"/>
              <span class="bubble business"></span>
              <div>Business</div>
          </label>

          <label>
            <input
              type="radio" 
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add Todo"/>
      </form>
    </section>

    <section class="todo-list">
      <h3>YOUR TODOS</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${todo.category == 'business'
                                  ? 'business'
                                  : 'personal'}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content"/>
          </div>

          <div class="actions">
            <button class="delete" @click=removeTodo(todo)>Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>