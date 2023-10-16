<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button class="todoapp__toggle-all" :class="{ active: activeTodos.length === 0 }"></button>

        <form @submit.prevent="handleSubmit">
          <input 
            type="text" 
            class="todoapp__new-todo" placeholder="What needs to be done?" v-model="title" 
          />
        </form>
      </header>

      <TransitionGroup name="list" 
     tag="section" class="todoapp__main">
        <TodoItem 
          v-for="todo of visibleTodos" 
          :key="todo.id" 
          :todo="todo" 
          @update="Object.assign(todo, $event)"
          @delete="todos.splice(todos.indexOf(todo), 1)"
        />
      </TransitionGroup>

      <footer class="todoapp__footer">
        <span class="todo-count">
          {{ activeTodos.length }} items left
        </span>
        <Filter v-model="status"/>
        <button v-if="activeTodos.length > 0" class="todoapp__clear-completed">
          Clear completed
        </button>
      </footer>
    </div>

    <Message 
      class="is-warning" 
      :active="errorMessage !== ''"
      @hide="errorMessage = ''"
    > 
      <template #default="{x}">
        <p>{{ errorMessage }} {{x}}</p>
      </template>

      <template #header>
        <p>Server error</p>
      </template>
    </Message>

  </div>
</template>


<script>
import Message from './components/Message.vue'
import Filter from './components/Filter.vue';
import TodoItem from './components/TodoItem.vue';

export default {
  components: {
    Message,
    TodoItem,
    Filter,
  },
  data() {
    let todos = [];
    const jsonData = localStorage.getItem('todos') || '';
    let errorMessage = '';

    try {
      todos = JSON.parse(jsonData);
    } catch (err) {
      errorMessage = 'Unable to load todos';
    }

    return {
      todos,
      title: '',
      status: 'all',
      errorMessage,
    }
  },
  mounted() {
    console.log(this.todos);
  },
  computed: {
    activeTodos() {
      return this.todos.filter(todo => !todo.completed)
    },
    completedTodos() {
      return this.todos.filter(todo => todo.completed)
    },
    visibleTodos() {
      switch(this.status){
        case 'active':
          return this.activeTodos;

        case 'completed':
          return this.completedTodos;

        default:
          return this.todos;
      }
    }
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos));
      }
    }
  },
  methods: {
    handleSubmit() {
      this.todos.push({
        id: Date.now(),
        title: this.title,
        completed: false,
      })

      this.title = '';
    }
  }
}
</script>

<style>
.list-enter-active,
.list-leave-active {
  max-height: 60px;
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  max-height: 0;
  transform: scaleY(0);
}
</style>