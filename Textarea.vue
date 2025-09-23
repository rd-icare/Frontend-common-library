<template>
  <div class="textarea" :class="item.class">
    <!-- Label -->
    <label v-if="item.type !== 'hidden' && item.hideLabel !== true" :for="item.id || item.name">
      {{ item.label }}
      <span class="form-star" :class="{ active: item.need }"></span>
    </label>

    <!-- Textarea -->
    <textarea
      :id="item.id || item.name"
      :type="item.type || 'text'"
      :name="item.name"
      :placeholder="item.placeholder"
      :value="item.value ?? (value as string | number | readonly string[] | null | undefined)"
      @input="
        eventName === 'input' ? (handleChange($event, !!errorMessage), fn.input($event, value, item)) : ''
      "
      @change="
        eventName === 'change' ? (handleChange($event, !!errorMessage), fn.change($event, value, item)) : ''
      "
      @blur="handleBlur($event, true)"
      :disabled="item.disabled"></textarea>

    <!-- Error -->
    <div v-if="item.hideError !== true && errorMessage && meta.touched" class="error">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

/** Textarea item 配置型別 */
interface ItemConfig {
  id?: string; // ID
  name: string; // 名稱
  type?: string; // 類型
  class?: string | string[]; // 樣式
  label?: string; // 標籤
  value?: string | number; // 值
  need?: boolean; // 是否必填
  disabled?: boolean; // 是否禁用
  placeholder?: string; // 佔位符
  hideLabel?: boolean; // 是否隱藏標籤
  hideError?: boolean; // 是否隱藏錯誤
}

/** Props 型別 */
interface Props {
  item: ItemConfig;
  eventName?: 'input' | 'change' | 'click';
  fn: {
    input: (e: Event, value: any, item: ItemConfig) => void;
    change: (e: Event, value: any, item: ItemConfig) => void;
    click: (e: Event, value: any, item: ItemConfig, type?: string) => void;
  };
}

const props = withDefaults(defineProps<Props>(), {
  item: () => ({}) as ItemConfig,
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
