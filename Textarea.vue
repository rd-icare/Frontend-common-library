<template>
  <div
    class="textarea"
    :class="[item.class, item.directionMode]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      maxWidth: item.maxWidth ? item.maxWidth + 'px' : '',
      minHeight: item.minHeight ? item.minHeight + 'px' : '',
      maxHeight: item.maxHeight ? item.maxHeight + 'px' : '',
      ...item.style,
    }">
    <label v-if="item.label && item.hideLabel !== true" :for="vueId ?? item.id ?? item.name" :class="[item.labelClass]">
      {{ item.label }}
      <div v-if="item.need" class="form-required"></div>
    </label>
    <div class="element">
      <textarea
        :id="vueId ?? item.id ?? item.name"
        :name="item.name"
        :class="{ invalid: errorMessage }"
        :placeholder="item.placeholder"
        :value="item.value ?? value"
        @input="eventName === 'input' ? runFn($event, item) : ''"
        @change="eventName === 'change' ? runFn($event, item) : ''"
        @blur="handleBlur($event, true)"
        :rows="item.rows"
        :cols="item.cols"
        :wrap="item.textareaWrap"
        :disabled="item.disabled"></textarea>
      <div v-if="!item.hideError && errorMessage" class="error">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

/**
 * - \t 製表符 水平 → 等於 Tab 鍵
 * - \v 製表符 垂直
 * - \f 換頁
 * - \n 換行符
 * - \r 回車
 */

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

const vueId = useId();

// vee-validate useField
const obj: Record<string, unknown> = {
  initialValue: props.item.value,
};

if (props.item.controlled !== undefined) {
  obj.controlled = false;
}

const { value, errorMessage, handleChange, handleBlur, meta, validate } = useField(
  () => props.item.name,
  undefined,
  obj
);

const runFn = async (e: Event, item: FormElements) => {
  // console.log('runFn', e, item);

  // 更新 vee-validate value
  handleChange(e, !!errorMessage);

  if (props.eventName === 'input') {
    props.fn.input && props.fn.input(e, value.value, props.item);
    return;
  }
  if (props.eventName === 'change') {
    props.fn.change && props.fn.change(e, value.value, props.item);
    return;
  }
};
</script>

<style lang="scss" scoped></style>
