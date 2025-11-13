<template>
  <div
    class="input"
    :class="[
      item.class,
      item.directionMode,
      {
        password: item.type === 'password' || item.class === 'font-password',
        file: item.type === 'file',
        checkbox: item.type === 'checkbox',
        radio: item.type === 'radio',
        hidden: ['hidden', 'array'].includes(item.type),
        checked: item.checked ?? checked,
      },
    ]"
    :style="{
      minWidth: item.minWidth ? item.minWidth + 'px' : '',
      maxWidth: item.maxWidth ? item.maxWidth + 'px' : '',
      minHeight: item.minHeight ? item.minHeight + 'px' : '',
      maxHeight: item.maxHeight ? item.maxHeight + 'px' : '',
      ...item.style,
    }">
    <label
      v-if="['checkbox', 'radio'].includes(item.type ?? '') && item.hideShape"
      class="icon-box gicons"
      :for="vueId">
      <span>{{
        item.disabled ? 'indeterminate_check_box' : item.checked ?? checked ? 'check_box' : 'check_box_outline_blank'
      }}</span>
    </label>
    <label v-if="item.label && item.hideLabel !== true" :for="vueId ?? item.id ?? item.name" :class="[item.labelClass]">
      {{ item.label }}
      <div v-if="!['checkbox', 'radio'].includes(item.type ?? '') && item.need" class="form-required"></div>
    </label>
    <div class="element">
      <input
        v-if="item.type !== 'date'"
        :id="vueId ?? item.id ?? item.name"
        :type="item.type || 'text'"
        :name="item.name"
        :class="{
          'custom-date': item.type === 'date',
          invalid: errorMessage,
        }"
        :placeholder="item.placeholder"
        :title="item.title"
        :value="
          item.type !== 'file'
            ? (modelValue && item.dayjsFormat ? dayjs(modelValue).format(item.dayjsFormat) : modelValue) ??
              (item.value && item.dayjsFormat ? dayjs(item.value).format(item.dayjsFormat) : item.value) ??
              value
            : ''
        "
        @input="!isComposing && eventName === 'input' ? runFn($event, item) : ''"
        @change="eventName === 'change' ? runFn($event, item) : ''"
        @blur="handleBlur($event, true)"
        :minlength="item.minlength"
        :maxlength="item.maxlength"
        @compositionstart="isComposing = true"
        @compositionend="onCompositionEnd"
        :true-value="item.trueValue"
        :false-value="item.falseValue"
        :checked="item.checked ?? checked"
        :accept="item.accept"
        :autocomplete="item.autocomplete"
        :disabled="item.disabled || processing" />
      <DatePicker
        v-else-if="item.type === 'date' && item.yearType === 'initial'"
        :input-attr="{ name: item.name, id: vueId ?? item.id ?? item.name }"
        :placeholder="item.placeholder || '年/月/日'"
        :value="value"
        @update:value="(val: string | null) => (value = val)"
        @change="runFn($event, item)"
        :="item.dateAttr"
        :disabled="item.disabled"
        :input-class="{ invalid: errorMessage }" />
      <DatePickerTW
        v-else-if="item.type === 'date' && item.yearType === 'tw'"
        :lang="{ yearFormat: 'YYYY' }"
        :input-attr="{ name: item.name, id: vueId ?? item.id ?? item.name }"
        :placeholder="item.placeholder || '年/月/日'"
        :value="value"
        @update:value="(val: string | null) => (value = val)"
        @change="runFn($event, item)"
        :="item.dateAttr"
        :disabled="item.disabled"
        :input-class="{ invalid: errorMessage }" />
      <div
        v-if="item.type === 'file'"
        v-drag-upload="{ item, setErrors }"
        class="file-box gicons"
        :class="[
          {
            'is-icon-type': item.isIconType,
            'is-icon-type-active': item.isIconType && value,
          },
        ]"
        :style="{
          height: item.fileBoxHeight ? item.fileBoxHeight + 'px' : '',
        }">
        <label
          v-if="!item.isIconType"
          class="file-shape"
          :class="[
            {
              active: value,
              invalid: errorMessage,
              disabled: item.disabled,
              'disabled-label': item.disabledLabelEvent,
            },
          ]"
          :for="vueId ?? item.id ?? item.name">
          <div v-show="value" class="image-box">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <div class="box">
            <div class="middle-text">
              <template v-if="!processing">
                <span>upload</span>
                <div class="placeholder" v-html="item.placeholder || '上傳檔案'"></div>
              </template>
              <template v-else>
                <div class="hidden">圖片壓縮中...</div>
              </template>
            </div>
            <div v-if="item.formatText" class="format-text font-small-4">
              {{ item.formatText ? `檔案格式：${item.formatText}` : '' }}
            </div>
          </div>
          <Button
            v-show="value"
            class="delete-button icon-style color-red-mode"
            icon="delete"
            :title="$t('Util.delete')"
            @click.prevent="(value = null), fn.click?.($event, value, item, 'delete')" />
          <CurrentLoading :show="processing" />
        </label>
        <template v-else>
          <div v-show="false" class="image-box">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <label v-if="!value" class="file-shape" :for="vueId ?? item.id ?? item.name">
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
      <div v-if="!item.hideError && !['checkbox', 'radio'].includes(item.type ?? '') && errorMessage" class="error">
        {{ errorMessage }}
      </div>
      <IconEye
        v-if="item.type === 'password' || item.class === 'font-password'"
        v-show="!item.hideEye"
        :type-text="item.type === 'text'" />
    </div>
    <div v-if="item.unit" class="unit">{{ item.unit }}</div>
    <div v-if="!item.hideError && ['checkbox', 'radio'].includes(item.type ?? '') && errorMessage" class="error">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import dayjs from 'dayjs';
import { useField } from 'vee-validate';

import DatePicker from '@/vue-datepicker-next/index.es.js';
import DatePickerTW from '@/vue-datepicker-next/index.tw.js';
import { getUrl, downloadFile, onFileChange } from '@/utils/common';

const props = withDefaults(defineProps<FormElementProps>(), {
  item: () => ({
    name: '',
    type: 'text',
    label: '',
  }),
  eventName: 'change',
  fn: () => ({
    input: () => {},
    change: () => {},
    click: () => {},
  }),
  modelValue: undefined,
});

const vueId = useId();

/** 圖片壓縮完成 url */
const previewUrl = ref<string>('');

/** 圖片壓縮中 */
const processing = ref(false);

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

const runFn = async (e: Event, item: FormElements) => {
  // console.log('runFn', e, item);

  // 圖片壓縮
  if (item.type === 'file' && item.imageCompressor) {
    const res = await onFileChange(e, item, previewUrl, processing);
    // console.log('imageCompressor');
    value.value = res;
  }

  // 去除空白
  if (typeof value.value === 'string') value.value = value.value.trim();

  if (item.type !== 'file') {
    // 更新 vee-validate value
    handleChange(e, !!errorMessage);
  }

  if (props.eventName === 'input') {
    props.fn.input && props.fn.input(e, value.value, props.item);
    return;
  }
  if (props.eventName === 'change') {
    props.fn.change && props.fn.change(e, value.value, props.item);
    return;
  }
};

watch(
  () => props.item.checked,
  (val, oldVal, onCleanup) => {
    value.value = val; // 因不是透過 click 觸發，如果 item.checked 值有變化，就將值設定給 value
    // console.log(`watch checked ${props.item.name}`, {
    //   'item.checked': val,
    //   checked: checked?.value,
    //   value: value.value,
    // });
  }
);
</script>

<style lang="scss" scoped></style>
