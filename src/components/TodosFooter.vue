<script setup>
import { computed, ref } from 'vue'

const p = defineProps({ filters: Object, todos: Array })
defineEmits(['clean'])

const refTodos = ref(p.todos)

const remaningTodos = computed(() => p.filters.active(refTodos.value).length)
</script>

<template>
  <footer class="todos__footer" v-if="todos.length > 0">
    <span
      >{{ remaningTodos }} {{ remaningTodos > 1 ? 'Items' : 'Item' }} left</span
    >
    <button
      class="todos__remove"
      v-show="todos.length > remaningTodos"
      @click="$emit('clean')"
    >
      Clear Completed
    </button>
  </footer>
</template>

<style scoped>
.todos__footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
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
