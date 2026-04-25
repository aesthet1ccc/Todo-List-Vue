<script setup lang="ts">
import { computed, ref, Teleport, TransitionGroup } from "vue";
import { useLocalStorage } from "@vueuse/core";
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

const sortedTodos = computed(() => {
  return [...todos.value].sort((a, b) => Number(a.done) - Number(b.done));
});

const countDoneTodo = computed(() => {
  return todos.value.filter((todo) => todo.done).length;
});

const countTodos = computed(() => {
  return todos.value.filter((todo) => !todo.done).length;
});

const storageRef = useLocalStorage("todos", todos);
</script>

<template>
  <header :class="style.header">
    <h3 :class="style.framework1">Vue3</h3>
    <h3 :class="style.framework2">Vue3</h3>
    <h1 :class="style.header_title">Todo List</h1>
  </header>
  <section :class="style.section_addItem">
    <input
      placeholder="Впишите задачу"
      v-model="inputValue"
      required
      @keyup.enter="addTodoItem"
    />
    <button @click="addTodoItem">Добавить задачу</button>
  </section>
  <section :class="style.section_todoItems">
    <section :class="style.counter_todos_block">
      <h2>Задач в работе: {{ countTodos }}</h2>
      <h2>Выполненные задачи: {{ countDoneTodo }}</h2>
    </section>
    <h2>Список задач</h2>
    <TransitionGroup tag="ul" name="list" :class="style.listItems">
      <div
        v-for="todo in sortedTodos"
        :key="todo.id"
        :class="style.section_item"
      >
        <li :class="style.listItem">
          <p :class="style.todo_text">
            <input
              type="checkbox"
              :class="style.todo_checkbox"
              v-model="todo.done"
            />
            <span :class="{ [style.todo_isDone]: todo.done }">
              {{ todo.text }}
            </span>
          </p>
          <div :class="style.item_buttons">
            <button @click="updateTodoItem(todo)" :disabled="todo.done">
              Изменить
            </button>
            <button @click="removeTodoItem(todo)">Удалить</button>
          </div>
        </li>
      </div>
    </TransitionGroup>
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
