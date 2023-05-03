<script setup>
defineProps({
  todos: Array,
  filter: String
})
</script>

<template>
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
</template>

<style scoped>
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
</style>
