<script setup>
defineProps({
  todo: Object,
  task: String,
  editingTodo: Object,
  editTodo: Function,
  doneEdit: Function,
  cancelEdit: Function,
  removeTodo: Function
})
defineEmits(['update:task', 'toggle'])
</script>

<template>
  <li
    class="todo__item"
    :key="todo.id"
    @click="$emit('toggle', !todo.completed)"
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
      :value="task"
      @input="$emit('update:task', $event.target.value)"
      @vue:mounted="({ el }) => el.focus()"
      @blur="doneEdit(todo)"
      @keyup.enter="doneEdit(todo)"
      @keyup.escape="cancelEdit(todo)"
    />
  </li>
</template>

<style scoped>
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
</style>
