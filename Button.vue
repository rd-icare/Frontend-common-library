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
  type?: 'button' | 'submit' | 'reset'; // 類型
  customClass?: string; // 自訂樣式
  title?: string; // 標題
  icon?: string; // 圖標
  text?: string; // 文字
  iconR?: string; // 右邊圖標
  timeout?: number; // 防止連點時間，單位毫秒，預設 1500
}

withDefaults(defineProps<Props>(), {
  type: 'button',
  customClass: 'font-small-3 font-normal',
  title: '',
  icon: '',
  text: '',
  iconR: '',
  timeout: 300,
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
  // 預設
  &.btn-style {
    height: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 4px;
    padding: 0px 12px;
    border: var(--border-1);
    border-radius: var(--border-radius-1);
    color: var(--main-text);
    background-color: var(--white);
    transition: var(--transition-fast);
    &:hover {
      opacity: 0.8;
    }
    > span {
      color: var(--icons);
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
      &:hover {
        opacity: 1;
        filter: brightness(1.1);
      }
    }
    // 副色
    &.c-sub {
      border: 1px solid var(--color-sub);
      color: var(--white);
      background-color: var(--color-sub);
      > span {
        color: var(--white);
      }
      &:hover {
        opacity: 1;
        filter: brightness(1.1);
      }
    }
    // 無邊框
    &.no-border {
      border: 1px solid transparent;
      background-color: transparent;
    }
    // 圖標
    &.icon-style {
      flex: 0 0 32px;
      width: 32px;
      height: 32px;
      justify-content: center;
      padding: 0px;
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
      &.active {
        // pointer-events: none;
        border: 1px solid var(--color-primary);
        background-color: var(--color-primary-back);
        > span {
          color: var(--color-primary);
        }
      }
    }
    // 圖標 hover
    &.icon-style-hover {
      &:hover {
        opacity: 1;
        background-color: var(--surface);
      }
    }
  }
}
</style>
