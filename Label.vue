<template>
  <div
    class="input"
    :class="[item.class]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      ...item.style,
    }">
    <label v-if="!item.useHtmlLabel && item.label" :class="[item.labelClass]">
      {{ item.label }}
    </label>
    <label v-if="item.useHtmlLabel && item.label" :class="['flex items-center', item.labelClass]" v-html="item.label" />
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
.input {
  gap: 0px !important;
  margin-right: calc(var(--form-content-gap) / 2);
  > .element {
    > .input-element {
      height: calc(var(--box-height) - var(--form-content-gap));
    }
  }
}
</style>
