<template>
  <div class="text gicons" :class="typeStyle" :title="`${title}`" v-debounce-click:[timeout]>
    <span v-if="icon">{{ icon }}</span>
    <div v-if="!useHtml && text" class="value">
      {{ text }}
    </div>
    <div v-if="useHtml && text" class="v-html">
      <div v-html="text" class="value"></div>
      <slot />
    </div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 使用 v-html 渲染 */
  useHtml?: boolean;
  /** 類型風格 */
  typeStyle?: string;
  /** 標題提示文字 */
  title?: string | number;
  /** 左圖標 */
  icon?: string;
  /** 文字 */
  text?: string | number | object;
  /** 右圖標 */
  iconR?: string;
  /** 防止連點時間，單位毫秒 */
  timeout?: number;
}

withDefaults(defineProps<Props>(), {
  title: '',
  icon: '',
  text: '',
  iconR: '',
  timeout: 300,
});
</script>

<style lang="scss" scoped>
.text {
  overflow: hidden;
  display: grid; // 填滿
  /* display: inline-grid; // 不填滿 */
  grid-auto-flow: column; // 子元素強制橫向排
  align-items: center;
  gap: 4px;
  line-height: 1;
  .value {
    overflow: hidden;
    text-overflow: ellipsis;
    word-break: break-all;
  }
  &:has(.v-html, .value),
  & + :deep(.icon) {
    span {
      position: relative;
      top: 1px;
    }
  }
  // 標題風格
  &.title-style {
    font-size: 18px;
    font-weight: var(--font-bold);
    line-height: 1;
  }
  // 圖標風格
  &.icon-style {
    justify-content: flex-start;
  }
  // 連結風格
  &.link-style {
    cursor: pointer;
    color: var(--color-secondary);
    :hover {
      text-decoration: underline;
    }
  }
  // 圓角風格 & 輸入框風格
  &.round-style,
  &.input-style {
    min-height: var(--box-height);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 2px;
    padding: 0px 12px;
    border-radius: var(--border-radius-1);
    .value {
      word-break: break-word;
    }
    // 副色模式
    &.color-secondary-mode {
      @include blue-style;
    }
    // 紅色模式
    &.color-red-mode {
      @include red-style;
    }
    // 橘色模式
    &.color-orange-mode {
      @include orange-style;
    }
    // 棕色模式
    &.color-brown-mode {
      @include brown-style;
    }
    // 綠色模式
    &.color-green-mode {
      @include green-style;
    }
    // 藍色模式
    &.color-blue-mode {
      @include blue-style;
    }
    // 灰色模式
    &.color-gray-mode {
      @include gray-style;
    }
  }
  // 輸入框風格
  &.input-style {
    width: 100%;
    justify-content: flex-start;
    line-height: 1.3;
  }
}
</style>
