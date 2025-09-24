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
    ]">
    <!-- 組件 -->
    <Component v-if="item.component" :is="item.component" :item />

    <template v-else>
      <!-- Checkbox -->
      <div v-if="item.type === 'checkbox' && item.hideShape !== true" class="inputShape">
        <div class="shape"></div>
      </div>
      <label v-if="item.type === 'checkbox' && item.hideShape" class="checkboxIcon gicons" :for="item.id || item.name">
        <span>{{ item.disabled ? 'indeterminate_check_box' : checked ? 'check_box' : 'check_box_outline_blank' }}</span>
      </label>

      <!-- Label -->
      <label v-if="item.type !== 'hidden' && item.hideLabel !== true" :for="item.id || item.name">
        {{ item.label }}
        <span v-if="item.need" class="form-star" :class="{ active: item.need }"></span>
      </label>

      <!-- Input -->
      <input
        v-if="item.type !== 'date'"
        :id="item.id || item.name"
        :type="item.type || 'text'"
        :name="item.name"
        :class="{ 'custom-date': item.type == 'date', invalid: errorMessage, isValue: value }"
        :placeholder="!item.disabled ? item.placeholder || '輸入內容' : ''"
        :value="item.type !== 'file' ? props.modelValue ?? value ?? item.value : ''"
        @input="
          eventName === 'input' ? (handleChange($event, !!errorMessage), fn.input && fn.input($event, value, item)) : ''
        "
        @change="
          eventName === 'change'
            ? (handleChange($event, !!errorMessage), fn.change && fn.change($event, value, item))
            : '',
            change($event, item.type)
        "
        @blur="handleBlur($event, true)"
        :disabled="item.disabled"
        :true-value="item.trueValue"
        :false-value="item.falseValue"
        :checked="item.checked || checked"
        :minlength="item.minlength || undefined"
        :maxlength="item.maxlength || undefined"
        :autocomplete="item.autocomplete || undefined"
        :accept="item.accept || undefined" />

      <!-- DatePicker -->
      <DatePicker
        v-else-if="item.type === 'date' && item.yearType === 'initial'"
        :input-attr="{ name: item.name, id: item.id || item.name }"
        :placeholder="!item.disabled ? item.placeholder || '選擇日期' : ''"
        :value="value"
        @update:value="(val: string | null) => (value = val)"
        @change="
          handleChange($event, !!errorMessage);
          fn.change && fn.change($event, value, item);
          change($event, item.type);
        "
        :="item.attr"
        :disabled="item.disabled"
        :input-class="{ invalid: errorMessage }" />

      <!-- File Input -->
      <div
        v-if="item.type === 'file'"
        v-drag-upload="{ item, setErrors }"
        class="fileBox theme"
        :class="[{ isIconType: item.isIconType, isIconTypeActive: item.isIconType && value }, item.fileShapeClass]">
        <label
          v-if="!item.isIconType"
          class="fileShape"
          :class="[{ active: value, invalid: errorMessage, disabled: item.disabled }]"
          :for="item.id || item.name">
          <div v-show="value" class="imageBox">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <div class="icon gicons">
            <div class="text1 font-small-1 font-normal">
              <span>add</span>
              <div v-html="item.placeholder || '上傳檔案'"></div>
            </div>
            <div class="text2 font-small-4 font-normal">{{ item.formatText || '檔案格式：JPG/PNG/PDF' }}</div>
            <Button v-if="item.showResetBtn && value" class="white" text="重新上傳" />
            <div v-if="item.showBtn && value" class="btn-box">
              <button type="button" @click.stop="fn.click && fn.click($event, value, item, 'btn-box')">
                <span v-if="item.showBtnIcon">{{ item.showBtnIcon }}</span>
              </button>
            </div>
          </div>
          <div v-if="!value && item.fileShapeClass" class="text font-small-1 font-normal">{{ item.placeholder }}</div>
        </label>
        <template v-else>
          <div v-show="false" class="image-box">
            <Img :src="getUrl(value as string | File | null | undefined)" />
          </div>
          <label v-if="!value" class="file-shape" :for="item.id || item.name">
            <span>upgrade</span>
          </label>
          <span
            v-else
            class="icon-image"
            @click="downloadFile($event, getUrl(value as string | File | null | undefined), item.label)"
            >image</span
          >
        </template>
      </div>

      <!-- Error -->
      <div v-if="!item.hideError && errorMessage" class="error">{{ errorMessage }}</div>

      <!-- IconEye -->
      <IconEye
        v-if="item.type === 'password' || item.class === 'font-password'"
        v-show="!item.hideEye"
        :type-text="item.type === 'text'" />
    </template>
  </div>
</template>

<script setup lang="ts">
import IconEye from './IconEye.vue';
import DatePicker from 'vue-datepicker-next';
import { useField } from 'vee-validate';
import { getUrl, downloadFile } from '@/utils/common';

/** 傳入參數 */
interface ItemConfig {
  /** ID */
  id?: string;
  /** 名稱 */
  name: string;
  /** 類型 */
  type?: string;
  /** 樣式 */
  class?: string | string[];
  /** 標籤 */
  label?: string;
  /** 值 */
  value?: string | number | boolean | File;
  /** v-model */
  modelValue?: any;
  /** 是否為必填 */
  need?: boolean;
  /** 是否為禁用 */
  disabled?: boolean;
  /** 佔位符 */
  placeholder?: string;
  /** 是否隱藏標籤 */
  hideLabel?: boolean;
  /** 是否隱藏錯誤訊息 */
  hideError?: boolean;
  /** 已勾選 */
  checked?: boolean;
  /** 勾選值 */
  trueValue?: any;
  /** 取消勾選值 */
  falseValue?: any;
  /** 年份類型 */
  yearType?: string;
  /** 其它屬性 */
  attr?: Record<string, any>;
  /** 插入組件 */
  component?: any;
  /** 是否為圖標類型 */
  isIconType?: boolean;
  /** 是否顯示按鈕 */
  showBtn?: boolean;
  /** 是否顯示按鈕圖標 */
  showBtnIcon?: string;
  /** 是否顯示清除按鈕 */
  showResetBtn?: boolean;
  /** 檔案上傳樣式 */
  fileShapeClass?: string;
  /** 檔案格式 */
  formatText?: string;
  /** 是否隱藏眼睛 */
  hideEye?: boolean;
  /** 是否為受控 */
  controlled?: boolean;
  /** 是否隱藏圖形 */
  hideShape?: boolean;
  /** 最小長度 */
  minlength?: number;
  /** 最大長度 */
  maxlength?: number;
  /** 自動完成 */
  autocomplete?: string;
  /** 接受的檔案類型 */
  accept?: string;
  /** 其它屬性 */
  [key: string]: any;
}

interface FnType {
  input?: (e: Event, value: any, item: ItemConfig) => void;
  change?: (e: Event, value: any, item: ItemConfig) => void;
  click?: (e: Event, value: any, item: ItemConfig, type?: string) => void;
}

interface Props {
  /** 傳入參數 */
  item: ItemConfig;
  /** 事件名稱 */
  eventName?: 'input' | 'change' | 'click';
  /** 函式 */
  fn?: FnType;
  /** v-model */
  modelValue?: any;
  /** 插入組件 */
  component?: any;
}

const props = withDefaults(defineProps<Props>(), {
  item: () => ({} as ItemConfig),
  eventName: 'change',
  fn: () => ({
    input: () => {},
    change: (e: Event, value: any, item: ItemConfig) => {},
    click: () => {},
  }),
  modelValue: undefined,
  component: null,
});

const obj: any = {
  type: 'default',
  initialValue: props.item.value,
};

// 是否為 vee-validate 受控元件
if (props.item.controlled !== undefined) obj.controlled = false;

// 如果是 Checkbox & Radio 類型
if (['checkbox', 'radio'].includes(props.item.type || '')) {
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

const change = (_event: Event, _type?: string) => {
  if (typeof value.value === 'string') value.value = value.value.trim();
};
</script>

<style lang="scss" scoped></style>
