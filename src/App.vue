<template>
  <div id="app">
    <h4>The Todo List</h4>
    <div class="todo-input-container">
      <input placeholder="Add a todo" v-model="todo" />
      <button v-on:click="addPost()" :disabled="!todo.length">Add</button>
    </div>
    <div class="todo-container">
      <Todo v-for="todo in todoList" :key="todo.key" :todo="todo" @modified-todo="fetchTodos()" />
    </div>
  </div>
</template>

<script>
import Todo from "./components/Todo.vue";

export default {
  name: "App",
  components: {
    Todo
  },
  data() {
    return {
      todo: "",
      todoList: [],
      errorAddingPost: false
    };
  },
  created() {
    this.fetchTodos();
  },
  methods: {
    async fetchTodos() {
      const response = await fetch("http://localhost:3000/todos");
      this.todoList = await response.json();
    },
    async addPost() {
      try {
        await fetch("http://localhost:3000/todos", {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            id: Math.max(...this.todoList.map(t => t.id)) + 1,
            todo: this.todo
          })
        });
        this.fetchTodos();
        this.todo = "";
      } catch {
        this.errorAddingPost = true;
      }
    }
  }
};
</script>

<style>
.todo-input-container {
  display: flex;
}
.todo-container {
  display: flex;
  flex-direction: column;
}
</style>
