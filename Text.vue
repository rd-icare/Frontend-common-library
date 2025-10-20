<template>
  <div class="text gicons" :class="typeStyle" :title="`${title}`">
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">
      {{ text }}
      <slot />
    </div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
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
  > div {
    overflow: hidden;
    text-overflow: ellipsis;
    word-break: break-all;
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
}
</style>
