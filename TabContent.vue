<template>
  <div class="tab-content">
    <slot />
  </div>
</template>

<script setup lang="ts">
interface Props {}

withDefaults(defineProps<Props>(), {});
</script>

<style lang="scss" scoped>
.tab-content {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  padding: 12px;
  border: var(--border-1);
  border-top: none;
  border-radius: 0 0 var(--border-radius-1) var(--border-radius-1);
  background-color: var(--white);
}
</style>
