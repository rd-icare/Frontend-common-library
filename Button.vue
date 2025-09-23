<template>
  <button
    ref="buttonRef"
    :type="type"
    :class="['btn-style gicons', customClass]"
    :title="icon === 'arrow_back' ? '返回上頁' : title"
    v-debounce-click:[timeout]>
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="iconR">{{ iconR }}</span>
  </button>
</template>

<!-- @click="onClick" -->

<script setup lang="ts">
interface Props {
  type?: 'button' | 'submit' | 'reset';
  customClass?: string;
  title?: string;
  icon?: string;
  text?: string;
  iconR?: string;
  timeout?: number; // 防止連點時間，單位毫秒，預設 1500
}

withDefaults(defineProps<Props>(), {
  type: 'button',
  customClass: 'font-body-3 font-normal',
  title: '',
  icon: '',
  text: '',
  iconR: '',
  timeout: 1000,
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
