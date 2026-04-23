<script setup lang="ts">
import { nextTick, onMounted, ref, watch } from "vue";
import style from "./Modal.module.scss";

const props = defineProps({
  inputValue: {
    type: String,
    default: "",
  },
  isModalOpen: {
    type: Boolean,
  },
});

const emit = defineEmits<{
  (event: "close"): void;
  (event: "save", newValue: string): void;
}>();

const handleAccept = () => {
  emit("save", inputModalValue.value);
};

const handleClose = () => {
  emit("close");
};

const inputModalValue = ref<string>(props.inputValue);
const inputRef = ref<HTMLInputElement | null>(null);

onMounted(() => {
  inputRef.value?.focus();
});
</script>
<template>
  <div :class="style.modal_overlay">
    <div :class="style.modal_content">
      <h3>Редактировать задачу</h3>
      <input
        v-model="inputModalValue"
        :class="style.modal_input"
        placeholder="Введите новое название..."
        @keyup.enter="handleAccept"
        @keyup.esc="handleClose"
        ref="inputRef"
      />

      <div :class="style.modal_actions">
        <button :class="style.btn_accept" @click="handleAccept">Принять</button>
        <button :class="style.btn_cancel" @click="handleClose">
          Отклонить
        </button>
      </div>
    </div>
  </div>
</template>
