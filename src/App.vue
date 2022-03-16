<template>
<link rel="stylesheet" href="node_modules/todomvc-app-css/index.css">
<div id="app">
  <section class="todoapp">
    <header>
      <h1>TODOS</h1>
    </header>
    <input v-model="newTodo" 
           @keyup.enter="addTodo"
           type="text" 
           class="new-todo" autofocus 
           placeholder="需要做什麼？"  />
    
    <main class="main">
      <input id="toggle-all" type="checkbox" 
      class="toggle-all" />
      <label for="toggle-all" ></label>
      <ul class="todo-list">
        <li v-for="todo in todos" :key="todo.id" class="todo" :class="{completed: todo.completed}">
          <div class="view">
            <input class="toggle" type="checkbox" @click="finishedTodo(todo)" v-model="todo.completed">
            <label for="">{{todo.title}}</label>
            <button class="destroy" @click="removeTodo(todo)"/>
          </div>
          <input type="text" class="edit">
        </li>
      </ul>
    </main>
     <footer class="footer" v-show="todos.length">
      <span class="todo-count"> <strong>{{remaining}}</strong> {{remaining | pluralize}} left </span>
      <ul class="filters">
        <li>
          <a href="#/all">All</a>
        </li>
        <li>
          <a href="#/active">Active</a>
        </li>
        <li>
          <a href="#/completed">Completed</a>
        </li>
      </ul>
      <button class="clear-completed">
        Clear completed
      </button>
    </footer>
<!--     <div v-else>No footer</div> -->
  </section>
  <button type="button" @click="saveStorage" >Save </button>
</div>
</template>

<script>


export default {
  import 'todomvc-app-css';
  const { uuid } = require('uuidv4');


const STORAGE_KEY = "todomvc-app-vue";

const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter((todo) => !todo.completed),
  completed: (todos) => todos.filter((todo) => todo.completed)
};

new Vue({
  el: "#app",
  data: {
    newTodo: "",
    todos: [
      {
        id: uuidv4(),
        title: "學習 Vue Template 的使用",
        completed: true
      },
      {
        id: uuidv4(),
        title: "學習在 Vue Template 中進行條件判斷",
        completed: false
      },
      {
        id: uuidv4(),
        title: "學習在 Vue Template 中使用迴圈",
        completed: false
      }
    ]
  },
  methods: {
    addTodo() {
      // console.log("this.newTodo", this.newTodo);
      const title = this.newTodo && this.newTodo.trim();
      if (!title) {
        return;
      }
      this.todos.push({
        id: uuidv4(),
        title: this.newTodo,
        completed: false
      });
      this.newTodo = "";
    },
    removeTodo(todo) {
      console.log("remove todo", todo.id);
      this.todos = this.todos.filter((_todo) => _todo.id !== todo.id);
    },
    finishedTodo(e) {
      console.log(this.todos.filter((_todo) => _todo.id !== e.id).completed);
      this.todos.completed = this.todos.filter((_todo) => _todo.id !== e.id).completed;
    },
    saveStorage() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
    }
  },
  computed: {
    remaining() {
      // console.log('active', filters.active(this.todos))
      return filters.active(this.todos).length;
      // return this.todos.filter(todo => !todo.completed).length;
    }
  },
  filters: {
    pluralize(n) {
      return n === 1 ? "item" : "items";
    }
  },
  watch: {
    todos: {
      handler: function () {
        // console.log("todos changed"), 
        this.saveStorage()
      },
      deep: true
    }
  },

  created() {
    this.todos = JSON.parse(localStorage.getItem(STORAGE_KEY)) || []
  },
});

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
