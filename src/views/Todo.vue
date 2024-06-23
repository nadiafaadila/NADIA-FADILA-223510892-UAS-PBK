<template>
  <q-page padding>
    <div class="todo-form">
      <div class="input-container">
        <q-input filled v-model="newTodo" label="Add To-Do" class="q-mb-md" />
        <q-btn round icon="add" class="add-btn" @click="addTodo" />
      </div>

      <div class="filter-buttons">
        <div class="filter-list">
          <q-btn
            class="check-btn"
            @click="toggleFilter('done')"
            label="Filter Checked"
            flat
          />
          <q-btn
            class="uncheck-btn"
            @click="toggleFilter('undone')"
            label="Filter Unchecked"
            flat
          />
          <q-btn class="clear-btn" @click="clearTodos" label="Clear Todos" flat />
        </div>
      </div>
    </div>

    <q-list bordered class="todo-list">
      <q-item
        v-for="todo in filteredTodos"
        :key="todo.id"
        v-bind:class="{ done: todo.done }"
        class="todo-item"
      >
        <q-item-section avatar>
          <q-checkbox
            v-model="todo.done"
            @change="toggleTodoStatus(todo)"
            color="primary"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label
            v-if="!todo.editing"
            v-bind:class="{ done: todo.done }"
            @click="editTodo(todo)"
          >
            <span :class="{ scribble: todo.done }">{{ todo.title }}</span>
          </q-item-label>
          <q-input
            v-else
            v-model="todo.title"
            dense
            hide-underline
            autofocus
            @keyup.enter="finishEditing(todo)"
            @blur="finishEditing(todo)"
            :disable="todo.done"
            class="edit-input"
          />
        </q-item-section>
        <q-item-section side>
          <q-btn
            v-if="!todo.editing && !todo.done"
            icon="edit"
            flat
            dense
            round
            class="edit-btn"
            @click="editTodo(todo)"
          />
          <q-btn
            v-if="todo.editing || todo.done"
            icon="check"
            flat
            dense
            round
            class="check-btn"
            @click="finishEditing(todo)"
          />
          <q-btn
            icon="delete"
            flat
            dense
            round
            class="delete-btn"
            @click="removeTodo(todo)"
          />
        </q-item-section>
      </q-item>
    </q-list>
  </q-page>
</template>

<script setup>
import { ref, computed } from "vue";
import { useTodoStore } from "../stores/todoStore";

const newTodo = ref("");
const filter = ref("undone"); // Default filter to show undone todos

const store = useTodoStore();

const addTodo = () => {
  if (newTodo.value.trim()) {
    const todo = {
      id: Date.now(),
      title: newTodo.value,
      done: false,
      editing: false,
    };
    store.addTodo(todo);
    newTodo.value = "";

    // Automatically switch filter to 'undone' if no filter is currently active
    if (!filter.value) {
      filter.value = 'undone';
    }
  }
};

const removeTodo = (todo) => {
  store.removeTodo(todo);
};

const editTodo = (todo) => {
  todo.editing = true;
};

const finishEditing = (todo) => {
  if (todo.title.trim()) {
    todo.editing = false;
  } else {
    store.removeTodo(todo);
  }
};

const toggleTodoStatus = (todo) => {
  store.toggleTodoStatus(todo);
};

const toggleFilter = (type) => {
  if (filter.value === type) {
    filter.value = null;
  } else {
    filter.value = type;
  }
};

const clearTodos = () => {
  store.clearTodos();
};

const filteredTodos = computed(() => store.filteredTodos(filter.value));
</script>

<style scoped>
.q-page {
  background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 100%);
  padding: 20px;
  border-radius: 10px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}

.todo-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  background: #ffffffcc;
  padding: 10px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  flex-wrap: wrap;
  width: 100%; /* Full width */
  max-width: 600px; /* Limit width */
}

.input-container {
  display: flex;
  align-items: center;
  width: 100%;
}

.q-input {
  flex: 1; /* Take remaining space */
}

.add-btn {
  background: linear-gradient(135deg, #ff758c 0%, #ff7eb3 100%);
}

.add-btn:hover {
  background: linear-gradient(135deg, #ff7eb3 0%, #ff758c 100%);
}

.filter-buttons {
  display: flex;
  gap: 10px;
  margin-top: 10px; /* Space between New Todo and filters */
  justify-content: center; /* Center align buttons */
  width: 100%; /* Full width */
}

.filter-list {
  display: flex;
  gap: 10px;
}

.q-btn {
  color: #fff !important;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.check-btn {
  background: linear-gradient(135deg, rgb(209, 160, 168) 0%, #eef2f3 100%);
}

.check-btn:hover {
  background: linear-gradient(135deg, #eef2f3 0%, #e6c0f0 100%);
}

.uncheck-btn {
  background: linear-gradient(135deg, #ff9966 0%, #ff5e62 100%);
}

.uncheck-btn:hover {
  background: linear-gradient(135deg, #ff5e62 0%, #ff9966 100%);
}

.clear-btn {
  background: linear-gradient(135deg, red 0%, #ad3f3f 100%);
  padding: 5px;
}

.clear-btn:hover {
  background: linear-gradient(135deg, red 0%, #f58985 100%);
  border-color: transparent; /* Remove border color change on hover */
}

.todo-list {
  background: #ffffffcc;
  padding: 10px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  width: 100%; /* Full width */
  max-width: 600px; /* Limit width */
}

.todo-item {
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 10px;
  background: #fff;
  transition: background 0.3s, transform 0.3s;
  width: 100%; /* Full width */
}

.todo-item:hover {
  background: #f0b6bd;
  transform: translateY(-2px);
}

.done {
  color: #6c757d;
}

.scribble {
  position: relative;
}

.scribble::after {
  content: "";
  position: absolute;
  left: 0;
  top: 50%;
  width: 100%;
  height: 2px;
  background: #6c757d;
  transform: rotate(-5deg);
}

.edit-input {
  width: 100%;
  background: #ffe6e9;
}
</style>
