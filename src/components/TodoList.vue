<script setup lang="ts">
import { ref, Teleport } from "vue";
import style from "./TodoList.module.scss";

import Modal from "./Modal/Modal.vue";

interface Todos {
  id: number;
  text: string;
  done: boolean;
}

const todos = ref<Todos[]>([]);
const inputValue = ref<string>("");
const id = ref<number>(0);
const isModalOpen = ref<boolean>(false);
const selectedTodo = ref<Todos | null>(null);

const addTodoItem = () => {
  if (inputValue.value?.length !== 0 && inputValue.value.trim()) {
    todos.value.push({ id: id.value++, text: inputValue.value, done: false });
  }

  inputValue.value = "";
};

const removeTodoItem = (todo: Todos) => {
  todos.value = todos.value.filter((t) => t.id !== todo.id);
};

const updateTodoItem = (todo: Todos) => {
  selectedTodo.value = todo;
  isModalOpen.value = true;
};

const saveUpdatedTask = (newText: string) => {
  if (selectedTodo.value) {
    const todo = todos.value.find((t) => t.id === selectedTodo.value?.id);
    if (todo) {
      todo.text = newText;
    }
  }

  isModalOpen.value = false;
};
</script>

<template>
  <header :class="style.header">
    <h3 :class="style.framework1">Vue3</h3>
    <h3 :class="style.framework2">Vue3</h3>
    <h1 :class="style.header_title">Todo List</h1>
  </header>
  <section :class="style.section_addItem">
    <input placeholder="Впишите задачу" v-model="inputValue" required />
    <button @click="addTodoItem">Добавить задачу</button>
  </section>
  <section :class="style.section_todoItems">
    <h2>Список задач</h2>
    <ul :class="style.listItems">
      <div v-for="todo in todos" :key="todo.id" :class="style.section_item">
        <li :class="style.listItem">
          <p :class="style.todo_text">{{ todo.id + 1 }}) {{ todo.text }}</p>
          <div :class="style.item_buttons">
            <button @click="updateTodoItem(todo)">Изменить</button>
            <button @click="removeTodoItem(todo)">Удалить</button>
          </div>
        </li>
      </div>
    </ul>
  </section>
  <Teleport to="body">
    <Modal
      v-if="isModalOpen"
      :inputValue="selectedTodo?.text"
      @save="saveUpdatedTask"
      @close="isModalOpen = false"
    ></Modal>
  </Teleport>
</template>
