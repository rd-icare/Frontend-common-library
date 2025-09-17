<template>
  <button
    ref="buttonRef"
    :type="type"
    :class="['btn-style gicons', customClass]"
    :title="icon === 'arrow_back' ? '返回上頁' : title"
    v-debounce-click:[timeout]>
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
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
  timeout?: number; // 防止連點時間，單位毫秒，預設 1500
}

withDefaults(defineProps<Props>(), {
  type: 'button',
  customClass: 'font-body-3 font-normal',
  title: '',
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

<style lang="scss" scoped>
button {
  &.btn-style {
    height: 36px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 4px;
    padding: 0px 16px;
    border: 1px solid var(--secondary-text);
    border-radius: var(--border-radius-2);
    color: var(--secondary-text);
    background-color: var(--white);
    transition: var(--transition-fast);
    &:hover {
      filter: brightness(1.1);
      // filter: contrast(1.3);
    }
    > span {
      color: var(--secondary-text);
    }
    &:focus {
      outline: none;
    }
    // 主色
    &.c-primary {
      border: 1px solid var(--color-primary);
      color: var(--white);
      background-color: var(--color-primary);
      > span {
        color: var(--white);
      }
    }
    // 次要色
    &.c-sub {
      border: 1px solid var(--color-sub);
      color: var(--white);
      background-color: var(--color-sub);
      > span {
        color: var(--white);
      }
    }
    &.no-border {
      border: 1px solid transparent;
      background-color: transparent;
    }
    &.icon-style {
      width: 36px;
      height: 36px;      
      justify-content: center;
      > span {
        font-size: 24px;
      }
      &.left {
        position: absolute;
        left: 0;
        margin-left: 4px;
        padding: 0;
        height: 100%;
      }
      &.right {
        position: absolute;
        right: 0;
        margin-right: 4px;
        padding: 0;
        height: 100%;
      }
    }
  }
}
</style>
