<template>
  <div class="text-style gicons" :title="title">
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 標題提示文字 */
  title?: string;
  /** 左圖標 */
  icon?: string;
  /** 文字 */
  text?: string | object;
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
.text-style {
  overflow: hidden;
  height: 32px;
  display: grid;
  align-items: center;
  padding: 0px;
  > div {
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
</style>
