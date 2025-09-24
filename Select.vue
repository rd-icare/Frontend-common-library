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
      :value="item.value ?? (value as string | number | readonly string[] | null | undefined)"
      @input="eventName === 'input' ? (handleChange($event, !!errorMessage), fn.input($event, value, item)) : ''"
      @change="eventName === 'change' ? (handleChange($event, !!errorMessage), fn.change($event, value, item)) : ''"
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

/** 下拉選項型別 */
interface SelectOption {
  value?: string | number | boolean; // 值
  label?: string; // 標籤
}

/** 傳入參數 */
interface ItemConfig {
  /** ID */
  id?: string;
  /** 名稱 */
  name: string;
  /** 類型 */
  type?: string;
  /** 樣式 */
  class?: string | string[];
  /** 標籤 */
  label?: string;
  /** 值 */
  value?: string | number | boolean;
  /** 是否為必填 */
  need?: boolean;
  /** 是否顯示空值 */
  noValue?: boolean;
  /** 是否顯示請選擇 */
  select?: boolean;
  /** 是否禁用 */
  disabled?: boolean;
  /** 是否隱藏標籤 */
  hideLabel?: boolean;
  /** 是否隱藏錯誤 */
  hideError?: boolean;
  /** 自動完成 */
  autocomplete?: string;
  /** 是否為受控 */
  controlled?: boolean;
}

/** Props 型別 */
interface Props {
  /** 選擇的文字 */
  selectedText?: string;
  /** 傳入參數 */
  item: ItemConfig;
  /** 選項 */
  option: (SelectOption | string | number)[];
  /** 事件名稱 */
  eventName?: 'input' | 'change' | 'click';
  /** 函數 */
  fn?: {
    input: (e: Event, value: any, item: ItemConfig) => void;
    change: (e: Event, value: any, item: ItemConfig) => void;
    click: (e: Event, value: any, item: ItemConfig, type?: string) => void;
  };
}

const props = withDefaults(defineProps<Props>(), {
  selectedText: '',
  item: () => ({} as ItemConfig),
  option: () => [],
  eventName: 'change',
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
