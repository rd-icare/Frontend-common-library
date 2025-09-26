<template>
  <div :class="['text-style gicons', customClass]" :title="title">
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 自定義樣式 */
  customClass?: string;
  /** 標題提示文字 */
  title?: string;
  /** 左圖標 */
  icon?: string;
  /** 文字 */
  text?: string;
  /** 右圖標 */
  iconR?: string;
}

withDefaults(defineProps<Props>(), {
  customClass: 'font-small-3 font-normal',
  title: '',
  icon: '',
  text: '',
  iconR: '',
});
</script>

<style lang="scss" scoped>
.text-style {
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 0px;
  color: var(--main-text);
  > span {
    color: var(--icons);
  }
}
</style>
