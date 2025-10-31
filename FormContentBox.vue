<template>
  <fieldset class="form-fieldset" :class="[fieldsetClass]" :disabled="readonly">
    <div
      class="input-box"
      v-bind="attrs"
      :class="{
        'no-placeholder': noPlaceholder,
        'horizontal-mode': directionMode === 'horizontal',
        'vertical-mode': directionMode === 'vertical',
        'adaptive-width': adaptiveWidth,
        'label-width': labelWidth,
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
  </fieldset>
</template>

<script setup lang="ts">
import InputNone from './InputNone.vue';
import Select from './Select.vue';
import Textarea from './Textarea.vue';
import Input from './Input.vue';

// 阻止自動繼承屬性到 root (fieldset)
defineOptions({ inheritAttrs: false });
const attrs = useAttrs();

/* 父層透過 props 傳進來的參數 */
interface Props {
  /** fieldset 樣式 */
  fieldsetClass?: string;
  /** 不顯示 placeholder */
  noPlaceholder?: boolean;
  /** 表單元素方向模式 */
  directionMode?: 'horizontal' | 'vertical';
  /** 表單元素自適應寬度 */
  adaptiveWidth?: boolean;
  /** 表單元素標籤寬度 */
  labelWidth?: number;
  /** 表單內容 */
  formContent?: FormElements[];
  /** 選項內容 */
  optionContent?: Record<string, any>;
  /** 函式集合 */
  fn?: FormFnType;
  /** 是否為只讀 */
  readonly?: boolean;
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

<style lang="scss" scoped>
.input-box {
  &.label-width {
    :deep(> div) {
      label:not(.checkbox-icon) {
        min-width: calc(v-bind(labelWidth) * 1px);
        justify-content: flex-end;
      }
    }
  }
}
</style>
