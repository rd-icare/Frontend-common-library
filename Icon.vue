<template>
  <div v-if="icon" class="icon gicons" :title="title">
    <span>{{ icon }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 標題提示文字 */
  title?: string;
  /** 圖標 */
  icon?: string;
}

withDefaults(defineProps<Props>(), {
  title: '',
  icon: '',
});
</script>

<style lang="scss" scoped>
.icon {
  user-select: none;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
