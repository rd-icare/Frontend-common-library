<template>
  <div class="noData">{{ text }}</div>
</template>

<script setup lang="ts">
interface Props {
  /** 文字 */
  text?: string;
}

withDefaults(defineProps<Props>(), {
  text: '未有資料',
});
</script>

<style lang="scss" scoped>
.noData {
  width: 100%;
  display: flex;
  justify-content: center;
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 1px;
  line-height: 1.5;
  margin-top: 50px;
  color: var(--placeholder-text);
}
</style>