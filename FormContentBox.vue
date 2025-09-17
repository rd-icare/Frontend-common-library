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
  name: string;
  type?: string;
  modelValue?: unknown;
  eventName?: string;
  selectedText?: string;
  inputName?: string;
  disabled?: boolean;
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
  formContent: FormItem[];
  optionContent?: Record<string, any>;
  fn?: FnType;
  styleVerson?: string;
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
