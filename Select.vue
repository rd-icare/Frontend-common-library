<template>
  <div class="select" :class="[item.class]">
    <!-- Label -->
    <label
      v-if="item.type !== 'hidden' && item.hideLabel !== true"
      :for="item.id || item.name"
      :class="{ noValue: value === '_' }">
      {{ item.label }}
      <span v-if="item.need" class="formStar" :class="{ active: item.need }"></span>
    </label>

    <!-- Select -->
    <select
      :id="item.id || item.name"
      :name="item.name"
      class="customSelect"
      :class="{ invalid: errorMessage }"
      :value="item.value ?? (value as string | number | readonly string[] | null | undefined)"
      @input="
        eventName === 'input' ? (handleChange($event, !!errorMessage), fn.inputFn($event, value, validate, item)) : ''
      "
      @change="
        eventName === 'change' ? (handleChange($event, !!errorMessage), fn.changeFn($event, value, validate, item)) : ''
      "
      @blur="handleBlur($event, true)"
      :disabled="item.disabled"
      :autocomplete="item.autocomplete || undefined">
      <option v-if="item.noValue" value="_" selected hidden></option>
      <option v-if="item.select === true" value="" selected disabled hidden>
        {{ selectedText || '請選擇' }}
      </option>
      <option v-else-if="item.select === false" value="">
        {{ selectedText || '不限' }}
      </option>
      <option
        v-for="(opt, index) in option"
        :key="index"
        :value="typeof opt === 'object' && opt !== null && 'value' in opt ? opt.value : opt">
        {{ typeof opt === 'object' && opt !== null && 'text' in opt ? opt.text : opt }}
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

/** 下拉選項型別 */
interface SelectOption {
  value?: string | number | boolean;
  text?: string;
}

/** Item 配置型別 */
interface ItemConfig {
  id?: string;
  name: string;
  type?: string;
  class?: string | string[];
  label?: string;
  value?: string | number | boolean;
  need?: boolean;
  noValue?: boolean;
  select?: boolean;
  disabled?: boolean;
  hideLabel?: boolean;
  hideError?: boolean;
  autocomplete?: string;
  controlled?: boolean;
}

/** Props 型別 */
interface Props {
  selectedText?: string;
  item: ItemConfig;
  option: (SelectOption | string | number)[];
  eventName?: 'input' | 'change' | 'click';
  fn: {
    inputFn: (e: Event, value: any, validate: any, item: ItemConfig) => void;
    changeFn: (e: Event, value: any, validate: any, item: ItemConfig) => void;
    clickFn: (e: Event, value: any, validate: any, item: ItemConfig, type?: string) => void;
  };
}

const props = withDefaults(defineProps<Props>(), {
  selectedText: '',
  item: () => ({}) as ItemConfig,
  option: () => [],
  eventName: 'change',
  fn: () => ({
    inputFn: () => {},
    changeFn: () => {},
    blurFn: () => {},
    clickFn: () => {},
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
