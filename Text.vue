<template>
  <div class="text gicons" :class="typeStyle" :title="`${title}`">
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
  /** 使用 v-html */
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
}

withDefaults(defineProps<Props>(), {
  title: '',
  icon: '',
  text: '',
  iconR: '',
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
  .value {
    overflow: hidden;
    text-overflow: ellipsis;
    word-break: break-all;
  }
  &.title-style {
    font-size: 18px;
    font-weight: bold;
    line-height: 1;
  }
  &.icon-style {
    justify-content: flex-start;
  }
  &.link-style {
    cursor: pointer;
    color: var(--color-secondary);
    :hover {
      text-decoration: underline;
    }
  }
  &.round-style {
    min-height: var(--box-height);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 2px;
    padding: 4px 12px;
    border-radius: var(--border-radius-1);
    .value {
      word-break: break-word;
    }
    // 副色主題
    &.color-secondary {
      @include blue-style;
    }
    // 紅色主題
    &.color-red {
      @include red-style;
    }
    // 綠色主題
    &.color-green {
      @include green-style;
    }
    // 藍色主題
    &.color-blue {
      @include blue-style;
    }
  }
}
</style>
