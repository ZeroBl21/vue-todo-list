<script setup>
import { computed, onMounted, ref, watchEffect } from 'vue'
import TodoItem from './components/TodoItem.vue'
import TodosFilters from './components/TodosFilters.vue'
import TodosFooter from './components/TodosFooter.vue'

const STORAGE_KEY = '__VUE_TODOS__'
const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter((todo) => !todo.completed),
  completed: (todos) => todos.filter((todo) => todo.completed)
}

const todos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY)) || [])
const filter = ref('all')
const editingTodo = ref({})

const filteredTodos = computed(() => filters[filter.value](todos.value))

onMounted(() => {
  window.addEventListener('hashchange', onHashChange)
})

watchEffect(() => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(todos.value))
})

function addTodo(e) {
  const value = e.target.value.trim()

  if (value === '') return

  todos.value.push({
    id: todos.value.length + 1,
    task: value,
    completed: false
  })

  e.target.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}

let beforeEditCache = ''
function editTodo(todo) {
  beforeEditCache = todo.task
  editingTodo.value = todo
}

function doneEdit(todo) {
  if (editingTodo.value) {
    editingTodo.value = {}
    todo.task = todo.task.trim()
    if (!todo.task) removeTodo(todo)
  }
}

function cancelEdit(todo) {
  editingTodo.value = {}
  todo.task = beforeEditCache
}

function removeCompleted() {
  todos.value = filters.active(todos.value)
}

function onHashChange() {
  const route = window.location.hash.replace(/#\/?/, '')
  if (filters[route]) {
    filter.value = route
  } else {
    window.location.hash = ''
    filter.value = 'all'
  }
}
</script>

<template>
  <section class="todos">
    <header class="todos__header">
      <h1 class="todos__title">Todos</h1>
      <input
        class="todos__input"
        autofocus
        placeholder="What needs to be done?"
        @keyup.enter="addTodo"
      />
    </header>
    <main class="todos__main">
      <TodosFilters :todos="todos" :filter="filter" />
      <ul class="todos__list">
        <TodoItem
          v-for="todo in filteredTodos"
          :key="todo.id"
          :todo="todo"
          v-model:task="todo.task"
          :edit-todo="editTodo"
          :editing-todo="editingTodo"
          :done-edit="doneEdit"
          :cancel-edit="cancelEdit"
          :remove-todo="removeTodo"
          @toggle="(newValue) => (todo.completed = newValue)"
        />
      </ul>
    </main>
    <TodosFooter :todos="todos" :filters="filters" @clean="removeCompleted" />
  </section>
</template>

<style>
/* Todos Main Body */
.todos {
  display: grid;
  place-items: center;
  margin: 2rem auto;
  max-width: min(640px, 95%);
  min-height: 12rem;
  background-color: #334155;
  border-radius: 0.25rem;
  border: 1px solid #475569;
}

.todos__title {
  text-align: center;
  margin: 1rem;
}

.todos__main,
.todos__header,
.todo__edit {
  width: 100%;
}

.todos__input,
.todo__edit {
  background: transparent;
  color: white;
  font-size: 16px;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  border: 1px solid #475569;
  width: 100%;
}
</style>
