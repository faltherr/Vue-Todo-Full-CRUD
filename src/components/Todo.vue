<template>
  <div class="todo-item-container">
    <div class="error-container" v-show="isError">
      <strong>An error occured</strong>
    </div>
    <h4 v-if="!isEditing">{{todo.todo}}</h4>
    <input v-else v-model="updatedTodo" />
    <div>
      <button v-if="!isEditing" @click="isEditing=true">Edit</button>
      <button v-else @click="editTodo(todo.id)">Submit</button>
      <button v-if="!isEditing" @click="deleteTodo(todo.id)">Delete</button>
      <button v-else @click="cancelEdit()">Cancel</button>
    </div>
  </div>
</template>

<script>
const event = "modified-todo";
export default {
  name: "Todo",

  props: {
    todo: {
      type: Object
    }
  },

  data() {
    return {
      updatedTodo: this.todo.todo,
      isEditing: false,
      isError: false
    };
  },

  methods: {
    cancelEdit() {
      this.isError = false;
      this.isEditing = false;
    },
    async deleteTodo(id) {
      console.log("deleting");
      try {
        await fetch(`http://localhost:3000/todos/${id}`, {
          method: "DELETE"
        });
        this.$emit(event);
      } catch {
        this.isError = true;
      }
    },
    async editTodo(id) {
      try {
        await fetch(`http://localhost:3000/todos/${id}`, {
          method: "PATCH",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            todo: this.updatedTodo
          })
        });
        this.isEditing = false;
        this.$emit(event);
      } catch {
        this.isError = true;
      }
    }
  }
};
</script>

<style scoped>
.todo-item-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
}
</style>