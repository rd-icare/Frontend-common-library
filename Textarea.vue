<template>
  <div class="textarea" :class="[item.class]">
    <label v-if="item.type !== 'hidden' && item.hideLabel !== true" :for="item.id || item.name">
      {{ item.label }}
      <span class="form-star" :class="{ active: item.need }"></span>
    </label>
    <textarea
      :id="item.id || item.name"
      :type="item.type || 'text'"
      :name="item.name"
      :placeholder="item.placeholder"
      :value="item.value ?? value"
      @input="
        eventName === 'input' ? (handleChange($event, !!errorMessage), fn.input && fn.input($event, value, item)) : ''
      "
      @change="
        eventName === 'change'
          ? (handleChange($event, !!errorMessage), fn.change && fn.change($event, value, item))
          : ''
      "
      @blur="handleBlur($event, true)"
      :disabled="item.disabled"></textarea>
    <div v-if="item.hideError !== true && errorMessage && meta.touched" class="error">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    name: '',
  }),
  eventName: 'change',
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
});

const { value, errorMessage, handleChange, handleBlur, meta, validate } = useField(
  () => props.item.name,
  undefined,
  {
    // 這裡若要雙向綁定可以加上 syncVModel: true
  }
);
</script>

<style lang="scss" scoped></style>
