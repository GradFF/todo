<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])

const name = ref('')

const input_content = ref('')

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  })
)

const addTodo = () => {
  if (input_content.value.trim() === '') {
    return
  }

  todos.value.push({
    content: input_content.value,
    createdAt: new Date().getTime(),
    done: false
  })

  input_content.value = ''
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(
  todos,
  newValue => {
    localStorage.setItem('todos', JSON.stringify(newValue))
  },
  { deep: true }
)

watch(name, newValue => {
  localStorage.setItem('name', newValue)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || null
  todos.value = JSON.parse(localStorage.getItem('todos') || [])
})
</script>
<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list</h4>
        <input
          type="text"
          placeholder="create new todo here"
          v-model="input_content"
        />
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>

      <div class="todo-items" v-for="(todo, index) in todos" :key="index">
        <label>
          <input type="checkbox" v-model="todo.done" />
        </label>
        <div class="todo-content">
          <input type="text" v-model="todo.content" />
        </div>
        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </section>
  </main>
</template>
