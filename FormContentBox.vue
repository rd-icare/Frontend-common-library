<template>
  <div
    class="input-box"
    :class="{
      'no-placeholder': noPlaceholder,
      'horizontal-mode': directionMode === 'horizontal',
      'vertical-mode': directionMode === 'vertical',
      'adaptive-width': adaptiveWidth,
    }">
    <template v-for="(item, index) in formContent" :key="item.name || index">
      <div v-if="item.breakLine" class="break-line"></div>
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
import InputNone from './InputNone.vue';
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
  /** 不顯示 placeholder */
  noPlaceholder?: boolean;
  /** 表單元素方向模式 */
  directionMode?: 'horizontal' | 'vertical';
  /** 表單元素自適應寬度 */
  adaptiveWidth?: boolean;
}
withDefaults(defineProps<Props>(), {
  formContent: () => [],
  optionContent: () => ({}),
  adaptiveWidth: true,
});

/**
 * type → 元件對應表
 */
const componentMap: Record<string, any> = {
  select: Select,
  textarea: Textarea,
  none: InputNone,
};
</script>

<style lang="scss" scoped></style>
