<template>
  <div
    class="textarea"
    :class="[item.class]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      ...item.style,
    }">
    <label v-if="item.label && item.hideLabel !== true" :for="item.id || item.name">
      {{ item.label }}
      <div v-if="item.need" class="form-required"></div>
    </label>
    <div class="element">
      <textarea
        :id="item.id || item.name"
        :name="item.name"
        :class="{ invalid: errorMessage }"
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
        :rows="item.rows"
        :cols="item.cols"
        :disabled="item.disabled"></textarea>
      <div v-if="item.hideError !== true && errorMessage && meta.touched" class="error">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    type: 'textarea',
    name: '',
  }),
  eventName: 'change',
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
});

const { value, errorMessage, handleChange, handleBlur, meta, validate } = useField(() => props.item.name, undefined, {
  // 這裡若要雙向綁定可以加上 syncVModel: true
});
</script>

<style lang="scss" scoped></style>
