<template>
  <div>
    <h1>My Todo List</h1>
    <div class="todo-form">
      <form @submit.prevent="OnSubmit">
        <input v-model="newTodo" name="todo" placeholder="todo-name" />
        <button>Add New Todo</button>
      </form>
    </div>
    <div class="todo-list">
      <ul>
        <li v-for="(todo, index) in Alltodos" :key="index" class="todo">
          <div class="content">
            <h3>{{ todo }}</h3>
          </div>
          <div class="content">
            <i class="far fa-times-circle fa-2x" @click="Completed(index)"></i>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
  name: "Todo",
  components: {},

  setup() {
    const newTodo = ref("");
    let Alltodos = ref([]);

    const API_URL = "http://localhost:8000";

    const getTodos = () => {
      axios
        .get(API_URL)
        .then((response) => {
          Alltodos.value = response.data.slice();
        })
        .catch((error) => {
          console.log("error", error);
        });
    };
    //api call to retrieve all todos
    getTodos();

    const OnSubmit = () => {
      axios
        .post(API_URL, { todo: newTodo.value })
        .then((response) => {
          Alltodos.value.push(response.data);
          newTodo.value = "";
        })
        .catch((error) => {
          console.log("error", error);
        });
    };
    const Completed = (index) => {
      if (confirm("are you sure!!")) {
        axios
          .delete(`${API_URL}/${index}`, { todo: newTodo.value })
          .then(() => {
            Alltodos.value.splice(index, 1);
          })
          .catch((error) => {
            console.log("error", error);
          });
      }
    };
    return {
      newTodo,
      OnSubmit,
      Alltodos,
      Completed,
    };
  },
};
</script>

<style scoped>
body {
  padding-top: 1em;
  padding-bottom: 1em;
}
input {
  display: block;
  margin: auto;
  border-top-style: hidden;
  border-right-style: hidden;
  border-left-style: hidden;
  border-bottom-style: groove;
  outline: none;
  font-size: 1.3em;
  padding: 15px 0;
  text-align: center;
}
.todo-form {
  margin-top: 30px;
}
.todo {
  margin-bottom: 20px;
  display: flex;
  flex-direction: row;
  align-items: center;
}
button {
  margin-top: 10px;
  padding: 10px;
  background-color: rgb(180, 199, 211);
  border: none;
  cursor: pointer;
  border-radius: 5px;
  font-weight: bold;
  outline: none;
}

h3 {
  margin: 0px;
  padding: 0px;
}
li {
  list-style-type: none;
  width: 50%;
  margin: auto;
  padding: 15px 0;
  background-color: rgb(216, 216, 216);
}
.content {
  flex: 1;
}
i {
  cursor: pointer;
  color: red;
}
</style>
