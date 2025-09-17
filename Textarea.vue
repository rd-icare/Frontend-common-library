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
  id?: string;
  name: string;
  type?: string;
  class?: string | string[];
  label?: string;
  value?: string | number;
  need?: boolean;
  disabled?: boolean;
  placeholder?: string;
  hideLabel?: boolean;
  hideError?: boolean;
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
