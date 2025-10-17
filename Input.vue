<template>
  <div
    class="input"
    :class="[
      item.class,
      {
        password: item.type === 'password' || item.class === 'font-password',
        file: item.type === 'file',
        checkbox: item.type === 'checkbox',
        hidden: item.type === 'hidden',
        checked,
      },
    ]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      ...item.style,
    }">
    <label v-if="item.type === 'checkbox' && item.hideShape" class="checkbox-icon gicons" :for="item.id || item.name">
      <span>{{ item.disabled ? 'indeterminate_check_box' : checked ? 'check_box' : 'check_box_outline_blank' }}</span>
    </label>
    <label v-if="item.label && item.type !== 'hidden' && item.hideLabel !== true" :for="item.id || item.name">
      {{ item.label }}
      <div v-if="item.need" class="form-required"></div>
    </label>
    <div class="element">
      <input
        v-if="item.type !== 'date'"
        :id="item.id || item.name"
        :type="item.type || 'text'"
        :name="item.name"
        :class="{ 'custom-date': item.type === 'date', invalid: errorMessage, isValue: value }"
        :placeholder="!item.disabled ? item.placeholder || '輸入內容' : ''"
        :value="item.type !== 'file' ? props.modelValue ?? item.value ?? value : ''"
        @input="
          !isComposing && eventName === 'input'
            ? (handleChange($event, !!errorMessage), fn.input && fn.input($event, value, item))
            : ''
        "
        @change="
          eventName === 'change'
            ? (handleChange($event, !!errorMessage), fn.change && fn.change($event, value, item))
            : '',
            change()
        "
        @blur="handleBlur($event, true)"
        :disabled="item.disabled"
        :true-value="item.trueValue"
        :false-value="item.falseValue"
        :checked="item.checked || checked"
        :minlength="item.minlength || undefined"
        :maxlength="item.maxlength || undefined"
        :autocomplete="item.autocomplete || undefined"
        :accept="item.accept || undefined"
        @compositionstart="isComposing = true"
        @compositionend="onCompositionEnd" />
      <DatePicker
        v-else-if="item.type === 'date' && item.yearType === 'initial'"
        :input-attr="{ name: item.name, id: item.id || item.name }"
        :placeholder="!item.disabled ? item.placeholder || '選擇日期' : ''"
        :value="value"
        @update:value="(val: string | null) => (value = val)"
        @change="
          handleChange($event, !!errorMessage);
          fn.change && fn.change($event, value, item);
          change();
        "
        :="item.attr"
        :disabled="item.disabled"
        :input-class="{ invalid: errorMessage }" />
      <DatePickerTW
        v-else-if="item.type === 'date' && item.yearType === 'tw'"
        :lang="{ yearFormat: 'YYYY' }"
        :input-attr="{ name: item.name, id: item.id || item.name }"
        :placeholder="!item.disabled ? item.placeholder || '選擇日期' : ''"
        :value="value"
        @update:value="(val: string | null) => (value = val)"
        @change="
          handleChange($event, !!errorMessage);
          fn.change && fn.change($event, value, item);
          change();
        "
        :="item.attr"
        :disabled="item.disabled"
        :input-class="{ invalid: errorMessage }" />
      <div
        v-if="item.type === 'file'"
        v-drag-upload="{ item, setErrors }"
        class="file-box gicons"
        :class="[{ 'is-icon-type': item.isIconType, 'is-icon-type-active': item.isIconType && value }]">
        <label
          v-if="!item.isIconType"
          class="file-shape"
          :class="[{ active: value, invalid: errorMessage, disabled: item.disabled }]"
          :for="item.id || item.name">
          <div v-show="value" class="image-box">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <div class="box">
            <div class="text1">
              <span>add</span>
              <div v-html="item.placeholder || '上傳檔案'"></div>
            </div>
            <div class="text2 font-small-4">{{ item.formatText || '檔案格式：JPG/PNG/PDF' }}</div>
          </div>
        </label>
        <template v-else>
          <div v-show="false" class="image-box">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <label v-if="!value" class="file-shape" :for="item.id || item.name">
            <span class="icon">upgrade</span>
          </label>
          <span
            v-else
            class="icon"
            @click="downloadFile($event, getUrl(value as string | File | null | undefined), item.label)"
            >image</span
          >
        </template>
      </div>
      <div v-if="!item.hideError && errorMessage" class="error">{{ errorMessage }}</div>
      <IconEye
        v-if="item.type === 'password' || item.class === 'font-password'"
        v-show="!item.hideEye"
        :type-text="item.type === 'text'" />
    </div>
  </div>
</template>

<script setup lang="ts">
import DatePicker from '@/vue-datepicker-next/index.es.js';
import DatePickerTW from '@/vue-datepicker-next/index.tw.js';
import { useField } from 'vee-validate';
import { getUrl, downloadFile } from '@/utils/common';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    name: '',
  }),
  eventName: 'change',
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
  modelValue: undefined,
});

const obj: any = {
  type: 'default',
  initialValue: props.item.value,
};

// 是否為 vee-validate 受控元件
if (props.item.controlled !== undefined) obj.controlled = false;

// 如果是 Checkbox & Radio 類型
if (['checkbox', 'radio'].includes(props.item.type ?? '')) {
  obj.type = props.item.type;
  obj.initialValue = props.item.checked;
  obj.checkedValue = props.item.trueValue ?? true;
  obj.uncheckedValue = props.item.falseValue ?? false;
}

// 如果有傳入 v-model
if (props.item.modelValue !== undefined) {
  delete obj.initialValue;
  obj.syncVModel = 'modelValue';
  obj.checkedValue = props.item.modelValue;
}

const { value, errorMessage, handleChange, handleBlur, meta, validate, checked, setErrors } = useField(
  () => props.item.name,
  undefined,
  obj
);

/** 是否正在中文輸入，判斷組字狀態 */
const isComposing = ref(false);
/** 中文輸入結束 */
function onCompositionEnd(e: CompositionEvent) {
  // console.log('中文輸入完成');
  isComposing.value = false;
  // 中文輸入完成後再執行方法
  handleChange(e, !!errorMessage);
  props.fn.input && props.fn.input(e, value.value, props.item);
}

const change = () => {
  if (typeof value.value === 'string') value.value = value.value.trim();
};
</script>

<style lang="scss" scoped></style>
