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
    <div class="element">
      <div class="input-element"></div>
    </div>
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

<style lang="scss" scoped>
.input-element {
  height: calc(var(--box-height) - var(--form-content-gap));
}
</style>
