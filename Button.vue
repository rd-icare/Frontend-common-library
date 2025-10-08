<template>
  <button
    ref="buttonRef"
    :type="type"
    class="btn-style gicons"
    :class="typeStyle"
    :title="icon === 'arrow_back' ? '返回上頁' : title"
    @click="typeStyle === 'select-style' && clickFn()"
    v-debounce-click:[timeout]>
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="typeStyle === 'select-style'">{{ selectState ? 'arrow_drop_up' : 'arrow_drop_down' }}</span>
    <span v-else-if="iconR">{{ iconR }}</span>
  </button>
</template>

<!-- @click="onClick" -->

<script setup lang="ts">
interface Props {
  /** 類型 */
  type?: 'button' | 'submit' | 'reset';
  /** 類型風格 */
  typeStyle?: string;
  /** 標題提示文字 */
  title?: string;
  /** 左圖標 */
  icon?: string;
  /** 文字 */
  text?: string;
  /** 右圖標 */
  iconR?: string;
  /** 防止連點時間，單位毫秒 */
  timeout?: number;
}

withDefaults(defineProps<Props>(), {
  type: 'button',
  style: '',
  title: '',
  icon: '',
  text: '',
  iconR: '',
  timeout: 300,
});

const emit = defineEmits<{ (e: 'clickFn'): void }>();

/** 模板引用 */
const buttonRef = ref<HTMLButtonElement | null>(null);

/** 選擇狀態 */
const selectState = defineModel<boolean>('selectState', {
  type: Boolean,
  default: false,
});

/** 回傳點擊事件 */
function clickFn() {
  selectState.value = !selectState.value;
  emit('clickFn');
}
</script>

<style lang="scss" scoped>
button {
  // 預設
  &.btn-style {
    overflow: hidden;
    height: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 2px;
    padding: 0px 12px;
    border: var(--border-1);
    border-radius: var(--border-radius-1);
    background-color: var(--white);
    transition: var(--transition-fast);
    &:focus {
      outline: none;
    }
    // 主色
    &.c-primary {
      border: 1px solid var(--color-primary);
      color: var(--white);
      background-color: var(--color-primary);
      &:hover {
        filter: brightness(1.1);
      }
    }
    // 副色
    &.c-sub {
      border: 1px solid var(--color-sub);
      color: var(--white);
      background-color: var(--color-sub);
      &:hover {
        filter: brightness(1.1);
      }
    }
    // 無邊框
    &.no-border {
      height: auto;
      padding: 0px;
      border: 1px solid transparent;
      background-color: transparent;
    }
    // 圖標風格
    &.icon-style {
      flex: 0 0 32px;
      width: 32px;
      height: 32px;
      justify-content: center;
      padding: 0px;
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
    // 圖標風格 hover
    &.icon-style-hover {
      &:hover {
        @include hover-style;
      }
    }
    // 選擇風格
    &.select-style {
      padding: 0px 4px 0px 12px;
    }
    &.active {
      @include active-style;
    }
    &.disabled {
      pointer-events: none;
      cursor: not-allowed;
      opacity: 0.5;
    }
  }
}
</style>
