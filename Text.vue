<template>
  <div :class="['text-style gicons', customClass]" :title="title">
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  customClass?: string; // 自訂樣式
  title?: string; // 標題
  icon?: string; // 圖標
  text?: string; // 文字
  iconR?: string; // 右側圖標
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
  border: 1px solid var(--white);
  border-radius: var(--border-radius-1);
  color: var(--main-text);
  background-color: var(--white);
  > span {
    color: var(--icons);
  }
}
</style>
