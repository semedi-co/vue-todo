<script setup>
import { computed, reactive, ref, watchEffect } from "vue";
// page1, page2, page3
const todo = reactive({
  data: JSON.parse(localStorage.getItem("todos") || "[]"),
  form: null,
  selected: null,
  mode: "add",
  search: null,
  page: "page1",
});

const selectForm = ref([]);

// input form Search
// carikan title ini di data

function createTodo() {
  if (todo.mode === "add") {
    if (todo.form.trim()) {
      todo.data.unshift({
        id: Math.random(),
        title: todo.form,
        completed: false,
      });
      todo.form = null;
    }
  } else {
    if (todo.form.trim()) {
      // Carikan index dari todo.data yang akan diedit
      const i = todo.data.findIndex((o) => o.id === todo.selected.id);
      // Ubah data pada index tersebut
      todo.data[i].title = todo.form;
      // Reset
      todo.selected = {};
      todo.form = null;
      todo.mode = "add";
    }
  }
}

function delTodo(idx) {
  // alert confirm
  if (confirm("Are you sure?")) {
    todo.data.splice(idx, 1);
  }
}

function checkList(id) {
  todo.data[id].completed = !todo.data[id].completed;
}

// string function
// array Function

// Filter Datanya
const filteredTodos = computed(() => {
  if (todo.search) {
    return todo.data.filter((data) => {
      return data.title.toLowerCase().includes(todo.search.toLowerCase());
    });
  } else {
    return todo.data;
  }
});

// persist state
watchEffect(() => {
  localStorage.setItem("todos", JSON.stringify(todo.data));
  console.log(todo.data);
});
</script>

<template>
  {{ selectForm }}
  <select v-model="selectForm">
    <option value="page1">Page 1</option>
    <option value="page2">Page 2</option>
    <option value="page3">Page 3</option>
  </select>
  <input type="checkbox" name="res" value="oke" v-model="selectForm" />
  <input type="checkbox" name="res1" value="no" v-model="selectForm" />

  <!-- Todo -->
  <div class="container">
    <div class="wrapper">
      <button
        @click="todo.page = 'page1'"
        :class="todo.page == 'page1' ? 'active' : ''"
      >
        All Todos
      </button>
      <button
        @click="todo.page = 'page2'"
        :class="todo.page == 'page2' ? 'active' : ''"
      >
        Completed Todos
      </button>
      <button
        @click="todo.page = 'page3'"
        :class="todo.page == 'page3' ? 'active' : ''"
      >
        Uncompleted Todos
      </button>
    </div>
    <hr />
    <!-- v-model -->
    <div class="wrapper">
      <form @submit.prevent="createTodo">
        <input type="text" v-model="todo.form" placeholder="Todo..." />
        <button type="submit">
          💾 {{ todo.mode === "add" ? "Simpan" : "Edit" }}
        </button>
      </form>
    </div>
    <hr />
    <input type="text" placeholder="🔍 Cari...." v-model="todo.search" />
    <hr />
    <!-- All Todos -->
    <div v-if="todo.page === 'page1'" class="wrapper">
      <h2>📋 All Todos</h2>
      <div v-if="filteredTodos.length">
        <ul v-for="(item, index) in filteredTodos" :key="item.id">
          <!-- item.completed = !item.completed -->
          <div class="listItem">
            <li
              @dblclick="checkList(index)"
              :class="item.completed ? 'completed' : ''"
            >
              {{ item.title }}
            </li>
            <div>
              <button
                @dblclick="
                  todo.mode = 'edit';
                  todo.form = item.title;
                  todo.selected = item;
                "
              >
                📝
              </button>
              <button @click="delTodo(index)">❌</button>
            </div>
          </div>
        </ul>
      </div>
      <div v-else>Data Kosong</div>
    </div>
    <!-- Completed Todos -->
    <div v-if="todo.page === 'page2'" class="wrapper">
      <h2>✅ Completed Todos</h2>
      <ul v-for="item in filteredTodos" :key="item.id">
        <li v-if="item.completed">
          {{ item.title }}
        </li>
      </ul>
    </div>
    <!-- Uncompleted Todos -->
    <div v-if="todo.page === 'page3'" class="wrapper">
      <h2>Uncompleted Todos</h2>
      <ul v-for="item in filteredTodos" :key="item.id">
        <li v-if="!item.completed">
          {{ item.title }}
        </li>
      </ul>
    </div>
  </div>
</template>
<style>
body {
  background-color: #f5f5f5;
  margin: 0;
}
.container {
  max-width: 380px;
  margin-left: auto;
  margin-right: auto;
  background-color: white;
  min-height: 100%;
  margin-top: 0;
  margin-bottom: 0;
  padding: 1rem;
}
.active {
  background-color: aqua;
}
button {
  border: none;
  margin-right: 12px;
  cursor: pointer;
}
.wrapper {
  margin: 12px 0;
}
.completed {
  text-decoration: line-through;
  color: rgb(172, 172, 172);
}
form {
  display: flex;
}
.listItem {
  display: flex;
  justify-content: space-between;
}
</style>
