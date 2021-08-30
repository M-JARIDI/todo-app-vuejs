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
    const getTodos = () => {
      axios
        .get(`http://localhost:8000`)
        .then((response) => {
          console.log("response get", response.data);
          Alltodos.value = response.data.slice();
          console.log("taaaaaayb", Alltodos);
        })
        .catch((error) => {
          console.log("error", error);
        });
    };
    getTodos();

    const OnSubmit = () => {
      axios
        .post(`http://localhost:8000`, { todo: newTodo.value })
        .then((response) => {
          console.log("response added", response.data);
          Alltodos.value.push(response.data);
          newTodo.value = "";
        })
        .catch((error) => {
          console.log("error", error);
        });
    };
    const Completed = (index) => {
      if (confirm("are you sure")) {
        axios
          .delete(`http://localhost:8000/${index}`, { todo: newTodo.value })
          .then((response) => {
            console.log("response delete", response.data);
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
