<script setup>
import { computed, ref, watchEffect } from 'vue'

const STORAGE_KEY = '__VUE_TODOS__'
const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter((todo) => !todo.completed),
  completed: (todos) => todos.filter((todo) => todo.completed)
}

const todos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY)) || [])
const filter = ref('all')
const editingTodo = ref('')

const filteredTodos = computed(() => filters[filter.value](todos.value))
const remaningTodos = computed(() => filters.active(todos.value).length)

window.addEventListener('hashchange', onHashChange)

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
    editingTodo.value = ''
    todo.task = todo.task.trim()
    console.log(!todo.task)
    if (!todo.task) removeTodo(todo)
  }
}

function cancelEdit(todo) {
  editingTodo.value = ''
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
      <div v-if="todos.length > 0">
        <ul class="todos__filters">
          <li class="filters__item">
            <a
              href="#/all"
              class="filters__link"
              :class="{ 'filters__link--selected': filter === 'all' }"
              >All</a
            >
          </li>
          <li class="filters__item">
            <a
              href="#/active"
              class="filters__link"
              :class="{ 'filters__link--selected': filter === 'active' }"
              >Active</a
            >
          </li>
          <li class="filters__item">
            <a
              href="#/completed"
              class="filters__link"
              :class="{ 'filters__link--selected': filter === 'completed' }"
              >Completed</a
            >
          </li>
        </ul>
      </div>
      <ul class="todos__list">
        <li
          class="todo__item"
          v-for="todo in filteredTodos"
          key="todo.id"
          @click="todo.completed = !todo.completed"
          @dblclick="editTodo(todo)"
          :class="{
            'todo__item--completed': todo.completed,
            'todo__item--edit': todo === editingTodo
          }"
        >
          <div class="todo__content">
            <span class="todo__task">{{ todo.task }}</span>
            <button class="todo__remove" @click="removeTodo(todo)">×</button>
          </div>
          <input
            class="todo__edit"
            v-if="todo == editingTodo"
            v-model="todo.task"
            @vnode-mounted="({ el }) => el.focus()"
            @keyup.blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @keyup.escape="cancelEdit(todo)"
          />
        </li>
      </ul>
    </main>
    <footer class="todos__footer" v-if="todos.length > 0">
      <span
        >{{ remaningTodos }}
        {{ remaningTodos > 1 ? 'Items' : 'Item' }} left</span
      >
      <button
        class="todos__remove"
        v-show="todos.length > remaningTodos"
        @click="removeCompleted"
      >
        Clear Completed
      </button>
    </footer>
  </section>
</template>

<style>
html {
  background-color: #1e293b;
  font-family: 'mononoki NF', 'Courier New', Courier, monospace;
  color: white;
}

input {
  font-family: 'mononoki NF', 'Courier New', Courier, monospace;
}

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
.todos__footer,
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

/* Filters */
.todos__filters {
  list-style: none;
  text-align: center;
  padding: 1rem;
}

.filters__item {
  display: inline;
}

.filters__link {
  color: inherit;
  margin: 3px;
  padding: 3px 7px;
  text-decoration: none;
  border: 1px solid transparent;
  border-radius: 3px;
}

.filters__link:hover {
  border-color: #db7676;
}

.filters__link--selected {
  border-color: #ce4646;
}

.todo__item {
  display: flex;
  justify-content: space-between;
  position: relative;
  word-wrap: anywhere;
}

.todo__item:nth-child(odd) {
  background-color: #4b5563;
}

.todo__content,
.todo__edit {
  padding: 1rem 2rem;
}

.todo__item--edit .todo__content {
  display: none;
}

.todo__item--completed {
  text-decoration: line-through;
  opacity: 0.8;
}

.todo__remove {
  position: absolute;
  top: 0;
  right: 10px;
  bottom: 0;
  width: 40px;
  height: 40px;
  margin: auto 0;
  font-size: 30px;
  color: #949494;
  transition: color 0.2s ease-out;
  border: none;
  background: transparent;
}

.todo__remove:hover {
  color: #c18585;
  opacity: 0.8;
}

.todos__footer {
  display: flex;
  justify-content: space-between;
  padding: 1rem;
  border: 1px solid #475569;
}

.todos__remove {
  font-size: 16px;
  color: white;
  border: 1px solid transparent;
  background: transparent;
}

.todos__remove:hover {
  border-color: #db7676;
  border-radius: 0.25rem;
}
</style>
