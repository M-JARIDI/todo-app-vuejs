<template>
  <div>
    <h1>My Todo List</h1>
    <div class="todo-form">
      <div>
        <form v-if="displayForm" @submit.prevent="OnSubmit">
          <input
            v-model="newTodo"
            name="todo"
            placeholder="todo-name"
            required
          />
          <button class="addButton">Add New Todo</button>
        </form>
      </div>
      <div>
        <button
          @click.prevent="openForm"
          style="padding: 0.5rem 1rem"
          :class="{ addButton: !displayForm, removeButton: displayForm }"
        >
          {{ displayForm ? "Close" : "Start adding tasks" }}
        </button>
      </div>
    </div>
    <div class="todo-list">
      <li v-if="Alltodos.length === 0" class="todo" style="margin-top: 1rem">
        <div class="content">
          <h3>No tasks</h3>
        </div>
      </li>
    </div>
    <div class="todo-list" v-if="Alltodos.length > 0">
      <ul>
        <li v-for="(todo, index) in Alltodos" :key="index" class="todo">
          <div class="content">
            <h3>{{ todo }}</h3>
          </div>
          <div class="content">
            <i class="fa fa-trash" @click="Completed(index)"></i>
          </div>
        </li>
        <button @click.prevent="removeAll" class="removeButton">
          Clear list
        </button>
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
    let displayForm = ref(false);
    const API_URL = "http://localhost:8000";
    // const API_URL = "https://todo-app-vuejs.herokuapp.com/";

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

    const openForm = () => {
      displayForm.value = !displayForm.value;
    };
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

    const removeAll = () => {
      if (confirm("are you sure!!")) {
        axios
          .delete(`${API_URL}/`)
          .then(() => {
            Alltodos.value.splice(0, Alltodos.value.length);
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
      removeAll,
      displayForm,
      openForm,
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
  display: flex;
  justify-items: center;
  flex-direction: row;
  justify-content: center;
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
.removeButton {
  background-color: red;
}
.addButton {
  background-color: green;
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
