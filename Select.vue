<template>
  <div class="select" :class="[item.class]">
    <!-- Label -->
    <label
      v-if="item.type !== 'hidden' && item.hideLabel !== true"
      :for="item.id || item.name"
      :class="{ noValue: value === '_' }">
      {{ item.label }}
      <span v-if="item.need" class="form-star" :class="{ active: item.need }"></span>
    </label>

    <!-- Select -->
    <select
      :id="item.id || item.name"
      :name="item.name"
      class="customSelect"
      :class="{ invalid: errorMessage }"
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
      :disabled="item.disabled"
      :autocomplete="item.autocomplete || undefined">
      <option v-if="item.noValue" value="_" selected hidden></option>
      <option v-if="item.select === true" value="" selected disabled hidden>
        {{ item.selectedText || '請選擇' }}
      </option>
      <option v-else-if="item.select === false" value="">
        {{ item.selectedText || '不限' }}
      </option>
      <option
        v-for="(opt, index) in option"
        :key="index"
        :value="typeof opt === 'object' && opt !== null && 'value' in opt ? opt.value : opt">
        {{ typeof opt === 'object' && opt !== null && 'label' in opt ? opt.label : opt }}
      </option>
    </select>

    <!-- Error -->
    <div v-if="!item.hideError && errorMessage" class="error">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({}),
  eventName: 'change',
  option: () => [],
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
});

// vee-validate useField
const obj: Record<string, unknown> = {
  initialValue: props.item.value,
};
if (props.item.controlled !== undefined) {
  obj.controlled = false;
}

const { value, errorMessage, handleChange, handleBlur, validate, resetField } = useField(
  () => props.item.name,
  undefined,
  obj
);

// 監聽外部傳入的值
watch(
  () => props.item.value,
  (newValue) => {
    if (newValue === undefined) resetField();
  }
);
</script>

<style lang="scss" scoped></style>
