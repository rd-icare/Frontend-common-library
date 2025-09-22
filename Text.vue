<template>
  <div :class="['text-style gicons', customClass]" :title="title">
    <span v-if="icon">{{ icon }}</span>
    <div v-if="text">{{ text }}</div>
    <span v-if="iconR">{{ iconR }}</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  customClass?: string;
  title?: string;
  icon?: string;
  text?: string;
  iconR?: string;
}

withDefaults(defineProps<Props>(), {
  customClass: 'font-body-3 font-normal',
  title: '',
  icon: '',
  text: '',
  iconR: '',
});
</script>

<style lang="scss" scoped>
.text-style {
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 0px;
  border: 1px solid var(--white);
  border-radius: var(--border-radius-2);
  color: var(--main-text);
  background-color: var(--white);
  > span {
    color: var(--icons);
  }
}
</style>
