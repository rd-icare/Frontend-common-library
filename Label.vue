<template>
  <div
    class="input"
    :class="[item.class]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      ...item.style,
    }">
    <label :class="[item.labelClass]">
      {{ item.label }}
    </label>
  </div>
</template>

<script setup lang="ts">
const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    name: '',
    type: 'text',
    label: '',
  }),
});
</script>

<style lang="scss" scoped></style>
