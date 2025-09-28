<template>
  <div :class="[styleVerson, { 'no-placeholder': noPlaceholder }]">
    <template v-for="(item, index) in formContent" :key="item.name || index">
      <component
        v-if="componentMap[item.type as keyof typeof componentMap]"
        :is="componentMap[item.type as keyof typeof componentMap]"
        :="{
          item,
          option: optionContent[item.name],
          fn,
        }"
        :eventName="item.eventName" />

      <Input v-else :="{ item, fn }" :eventName="item.eventName" v-model:modelValue="item.modelValue" />
    </template>
    <slot />
  </div>
</template>

<script setup lang="ts">
import None from './None.vue';
import Select from './Select.vue';
import Textarea from './Textarea.vue';
import Input from './Input.vue';

/* 父層透過 props 傳進來的參數 */
interface Props {
  /** 表單內容 */
  formContent: FormElements[];
  /** 選項內容 */
  optionContent?: Record<string, any>;
  /** 函式集合 */
  fn?: FormFnType;
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
