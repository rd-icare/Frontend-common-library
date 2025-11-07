<template>
  <div class="tab-content" :style="{ 'min-height': `${minHeight}px` }">
    <slot />
  </div>
</template>

<script setup lang="ts">
interface Props {
  minHeight?: number;
}

withDefaults(defineProps<Props>(), {
  minHeight: 400,
});
</script>

<style lang="scss" scoped>
.tab-content {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  border: var(--border-1);
  border-top: none;
  border-radius: 0 0 var(--border-radius-1) var(--border-radius-1);
  background-color: var(--white);
}
</style>
