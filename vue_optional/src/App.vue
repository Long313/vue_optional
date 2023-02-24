<script>
import axios from 'axios';
let id = 0;
export default {
  mounted() {
    this.$refs.p.textContent = "mounted",
    this.fetchData()
  },
  data() {
    return {
      message: "Hello World",
      titleClass: "title",
      newTodo: "",
      edit: false,
      hideCompleted: false,
      todos: []
    };
  },
  computed: {
    handleHideCompleted() {
      return this.hideCompleted ? this.todos.filter( t => t.done !== true) : this.todos
    },
  },
  methods: {
    onInput(e) {
      this.newTodo = e.target.value;
    },
    async handleAddTodo() {
      let obj = {
        title: this.newTodo,
        completed: false
      }
      const res = await axios.post("https://jsonplaceholder.typicode.com/todos",obj)
      this.todos.push(res.data);
    },
    async handleDelete(todo) {
      await axios.delete(`https://jsonplaceholder.typicode.com/todos/${todo.id}`)
      this.todos = this.todos.filter( t => t.id !== todo.id);
    },
    async fetchData() {
     let res = await axios.get("https://jsonplaceholder.typicode.com/todos?_limit=5")
     this.todos = res.data
    },
    // async handleEdit(todo) {
    //   console.log(todo.id - 1);
    //   this.edit = true;
    //   if(this.edit) {
    //     this.$refs.inputTitle.focus();
    //     let obj = {
    //       title: this.newTodo,
    //       completed: false
    //     }
    //    const res = await axios.put("https://jsonplaceholder.typicode.com/todos",obj)
    //    this.todos[todo.id - 1] = res.data;
    //   }
    // }
  },
}
</script>

<template>
  <h1 :id="titleClass">{{ message }}</h1>
  <p ref="p">Hello</p>

  <form @submit.prevent="handleAddTodo">
    <input v-model="newTodo" ref="inputTitle"/>
    <button>{{edit? "Finish" : "Add"}}</button>
  </form>
  <ul>
    <li
      v-for="todo in handleHideCompleted"
      key="todo.id"
      :class="todo.done ? 'completed' : ''"
    >
      <input type="checkbox" v-model="todo.done"/>
      {{ todo.title }}
      <!-- <button @click="handleEdit(todo)">Edit</button> -->
      <button @click="handleDelete(todo)">Delete</button>
    </li>
  </ul>
  <button @click="hideCompleted = !hideCompleted">
    {{ hideCompleted ? "Show all" : "Hide completed" }}
  </button>
</template>

<style scoped>
#title {
  color: red;
}
.completed {
  text-decoration: line-through;
}
</style>
