<template>
  <div class="table-box gicons">
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
.table-style {
}
</style>
