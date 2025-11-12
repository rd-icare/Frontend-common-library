<template>
  <div
    class="input"
    :class="[item.class]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      ...item.style,
    }">
    <label v-if="!item.useHtmlLabel && item.label" :class="[item.labelClass]" :for="vueId">
      {{ item.label }}
    </label>
    <label
      v-if="item.useHtmlLabel && item.label"
      :class="['flex items-center', item.labelClass]"
      :for="vueId"
      v-html="item.label" />
    <div class="element">
      <input type="text" :id="vueId" style="display: none" />
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

const vueId = useId();
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
