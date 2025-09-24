<template>
  <div :class="[styleVerson, { noPlaceholder }]">
    <template v-for="(item, index) in formContent" :key="item.name || index">
      <component
        v-if="componentMap[item.type as keyof typeof componentMap]"
        :is="componentMap[item.type as keyof typeof componentMap]"
        v-bind="{
          item,
          option: optionContent[item.name],
          selectedText: item.selectedText,
          eventName: item.eventName,
          fn,
        }" />

      <!-- 預設 Input -->
      <Input
        v-else
        :item="item"
        :eventName="(item.eventName as 'input' | 'change' | 'click' | undefined)"
        :fn="fn"
        v-model:modelValue="item.modelValue" />
    </template>
    <slot />
  </div>
</template>

<script setup lang="ts">
import None from './None.vue';
import Select from './Select.vue';
import Textarea from './Textarea.vue';
import Input from './Input.vue';

/**
 * 單一表單欄位的型別
 */
interface FormItem {
  /** 表單欄位名稱 */
  name: string;
  /** 表單欄位類型 */
  type?: string;
  /** v-model */
  modelValue?: unknown;
  /** 事件名稱 */
  eventName?: string;
  /** 選擇的文字 */
  selectedText?: string;
  /** input 欄位名稱 */
  inputName?: string;
  /** 是否禁用 */
  disabled?: boolean;
  /** 其它屬性 */
  [key: string]: any;
}

/* 父層透過 props 傳進來的函式集合 */
interface FnType {
  input?: (e: Event, value: any, item: object) => void;
  change?: (e: Event, value: any, item: object) => void;
  click?: (e: Event, value: any, item: object, type?: string) => void;
}
/* 父層透過 props 傳進來的參數 */
interface Props {
  /** 表單內容 */
  formContent: FormItem[];
  /** 選項內容 */
  optionContent?: Record<string, any>;
  /** 函式集合 */
  fn?: FnType;
  /** 樣式版本 */
  styleVerson?: string;
  /** 不顯示 placeholder */
  noPlaceholder?: boolean;
}
withDefaults(defineProps<Props>(), {
  formContent: () => [],
  optionContent: () => ({}),
  styleVerson: 'input-box',
  noPlaceholder: false,
});

/**
 * type -> 元件對應表
 */
const componentMap: Record<string, any> = {
  select: Select,
  textarea: Textarea,
  none: None,
};
</script>

<style lang="scss" scoped></style>
