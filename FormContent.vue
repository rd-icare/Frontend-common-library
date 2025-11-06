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
import Select from './Select.vue';
import Textarea from './Textarea.vue';
import Input from './Input.vue';
import Label from './Label.vue';
import InputNone from './InputNone.vue';

// 阻止自動繼承屬性到 root (fieldset)
defineOptions({ inheritAttrs: false });

const attrs = useAttrs();

withDefaults(defineProps<FormContentProps>(), {
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
  label: Label,
  none: InputNone,
};
</script>

<style lang="scss" scoped>
.input-box {
  &.label-width {
    :deep(> div) {
      label:not(.icon-box) {
        min-width: calc(v-bind(labelWidth) * 1px);
        justify-content: flex-end;
      }
    }
  }
}
</style>
