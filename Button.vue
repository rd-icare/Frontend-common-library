<template>
  <button ref="buttonRef" :type="type" :class="['btn-style gicons', customClass]" v-debounce-click:[timeout]>
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
  </button>
</template>

<!-- @click="onClick" -->

<script setup lang="ts">
interface Props {
  type?: 'button' | 'submit' | 'reset';
  customClass?: string;
  icon?: string;
  text?: string;
  timeout?: number; // 防止連點時間，單位毫秒，預設 1500
}

withDefaults(defineProps<Props>(), {
  type: 'button',
  customClass: 'font-body-3 font-normal',
  icon: '',
  text: '',
  timeout: 1500,
});

// 定義事件
// const emit = defineEmits<{
//   (e: 'click', event: MouseEvent): void;
// }>();

// ref 持有 button
const buttonRef = ref<HTMLButtonElement | null>(null);

// function onClick(event: MouseEvent) {
//   emit('click', event);
// }
</script>

<style lang="scss" scoped></style>
