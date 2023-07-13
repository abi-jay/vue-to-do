<!-- Import javascripts, things you can use with composition api in vue js-->
<!-- ref- handles state. mounted - render the component. computed-ordered by created date. watch - watch for changes -->
<!-- ref - input state. watch - changes to local storage. onmounted - pull from local storage-->
<!-- Check local storage under console - applications in browser-->
<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

const addToDo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  //console.log("addTodo");
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    // this will work once todos is initially set, will not work when we push. Watch works only
    // for changes. To make this work set deep to true. Look through individual item, if anything changes deep catches
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  // json.parse because we stringified originally
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<!-- Markup -->
<template>
  <main class="app">
    <section class="greeting">
      <!-- v- model is  reference in line 8, what we typed reflects here but won't save so do watch function-->
      <h2 class="title">
        Welcome, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addToDo">
        <h4>What's on your to-do list?</h4>
        <!-- As we enter input content changed - 2 way bounded-->
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"
        />
        <!-- {{ input_content }} -->
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <!-- {{ input_category }} -->
        </div>
        <input type="submit" value="Add Todo Item" />
      </form>
    </section>
    <!-- {{ todos }}-->
    <!-- List Todos-->
    <section class="todo-list">
      <h3>TO-DO LIST</h3>
      <div class="list">
        <!--Class bind with template literal-->
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <!-- click event-->
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
