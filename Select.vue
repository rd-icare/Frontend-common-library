<template>
  <div
    class="select"
    :class="[item.class, item.directionMode]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      maxWidth: item.maxWidth ? item.maxWidth + 'px' : '',
      minHeight: item.minHeight ? item.minHeight + 'px' : '',
      maxHeight: item.maxHeight ? item.maxHeight + 'px' : '',
      ...item.style,
    }">
    <label
      v-if="item.label && item.hideLabel !== true"
      :for="vueId ?? item.id ?? item.name"
      :class="[{ 'no-value': value === '_' }, item.labelClass]">
      {{ item.label }}
      <div v-if="item.need" class="form-required"></div>
    </label>
    <div class="element">
      <select
        :id="vueId ?? item.id ?? item.name"
        :name="item.name"
        class="custom-select"
        :class="{ invalid: errorMessage, 'is-value': value }"
        :value="item.value ?? value"
        @input="eventName === 'input' ? runFn($event, item) : ''"
        @change="eventName === 'change' ? runFn($event, item) : ''"
        @blur="handleBlur($event, true)"
        :autocomplete="item.autocomplete"
        :disabled="item.disabled">
        <option v-if="item.noValue" value="_" selected hidden></option>
        <option v-if="item.select === true" value="" selected disabled hidden>
          {{ item.selectedText || '請選擇' }}
        </option>
        <option v-else-if="item.select === false" value="">
          {{ item.selectedText || '不限' }}
        </option>
        <option
          v-for="(opt, index) in option"
          :key="index"
          :value="typeof opt === 'object' && opt !== null && 'value' in opt ? opt.value : opt"
          :data-label="opt.label">
          {{ typeof opt === 'object' && opt !== null && 'label' in opt ? opt.label : opt }}
        </option>
      </select>
      <div v-if="!item.hideError && errorMessage" class="error">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useField } from 'vee-validate';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    type: 'select',
    name: '',
  }),
  eventName: 'change',
  option: () => [],
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
});

const vueId = useId();

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

const runFn = async (e: Event, item: FormElements) => {
  // console.log('runFn', e, item);

  // 更新 vee-validate value
  handleChange(e, !!errorMessage);

  if (props.eventName === 'input') {
    props.fn.input && props.fn.input(e, value.value, props.item);
    return;
  }
  if (props.eventName === 'change') {
    props.fn.change && props.fn.change(e, value.value, props.item);
    return;
  }
};

// 監聽外部傳入的值
watch(
  () => props.item.value,
  (val, oldVal, onCleanup) => {
    value.value = val; // 因不是透過 click 觸發，如果 item.value 值有變化，就將值設定給 value
    // console.log(`watch Select value ${props.item.name}`, {
    //   'item.value': val,
    //   value: value.value,
    // });
    if (val === undefined) resetField();
  }
);
</script>

<style lang="scss" scoped></style>
