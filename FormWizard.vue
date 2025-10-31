<template>
  <form @submit="onSubmit" @keypress.enter="(e) => handleEnter(e, useEnter)" novalidate>
    <!-- 插槽 表單內容 -->
    <slot name="content" :values="values" :errors="errors" :handleReset="handleReset" />
    <!-- 插槽 表單底部 -->
    <slot name="bottom" :handleReset="handleReset" :resetForm="resetForm" />
    <slot />
    <FormValues v-if="$global.isTest" :values="values" :errors="errors" :meta="meta" />
  </form>
</template>

<script setup lang="ts">
import { useForm, type SubmissionContext, type InvalidSubmissionContext } from 'vee-validate';
import { toErrorFocus } from '@/utils/common';

interface Props {
  /** 表單名稱 */
  name?: string;
  /** 初始值 */
  initialValues?: Record<string, any>;
  /** 有效性驗證 */
  schema?: Record<string, any>;
  /** 保持值 */
  keepValues?: boolean;
  /** 是否使用 enter 鍵 */
  useEnter?: boolean;
  /** 是否為只讀 */
  // readonly?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  name: 'edit-form',
  initialValues: () => ({}),
  schema: () => ({}),
  keepValues: true,
  useEnter: false,
});

const emit = defineEmits<{
  <T extends Record<string, any>>(e: 'submit', values: T, actions: SubmissionContext<T>): void;
  <T extends Record<string, any>>(e: 'invalidSubmit', values: T, errors: T, results: InvalidSubmissionContext<T>): void;
}>();

const {
  name,
  values,
  errors,
  meta,
  handleReset,
  handleSubmit,
  resetField,
  resetForm,
  setValues,
  setFieldValue,
  setErrors,
  setFieldError,
  defineField,
  submitForm,
} = useForm({
  name: props.name,
  initialValues: props.initialValues,
  validationSchema: computed(() => props.schema),
  keepValuesOnUnmount: props.keepValues,
  validateOnMount: false, // 掛載時不驗證
});

const onSubmit = handleSubmit(
  (values, actions) => {
    emit('submit', values, actions);
  },
  (results) => {
    console.log('invalidSubmit', results);
    emit('invalidSubmit', values as any, errors, results);
    toErrorFocus(results.errors);
  }
);

/** 阻止 enter 鍵送出表單，僅 textarea 可用 */
function handleEnter(e: KeyboardEvent, allowEnter: boolean) {
  const target = e.target as HTMLElement;
  if (!allowEnter && target.tagName !== 'TEXTAREA') {
    e.preventDefault();
  }
}

// optional: 當切回只讀時，自動還原到 initialValues（視需求可移除）
// watch(
//   () => props.readonly,
//   (bool) => {
//     if (bool) {
//       // resetForm 會把 values 還原為 initialValues（也會重置 touched），避免未儲存的改動殘留
//       resetForm({ values: props.initialValues });
//     }
//   }
// );

/** 定義 vee-validate 表單暴露型別 */
export interface _VeeFormExpose {
  name: typeof name;
  values: typeof values;
  errors: typeof errors;
  meta: typeof meta;
  handleReset: typeof handleReset;
  resetField: typeof resetField;
  resetForm: typeof resetForm;
  setValues: typeof setValues;
  setFieldValue: typeof setFieldValue;
  setErrors: typeof setErrors;
  setFieldError: typeof setFieldError;
  defineField: typeof defineField;
  onSubmit: typeof onSubmit;
}
defineExpose<_VeeFormExpose>({
  name,
  values,
  errors,
  meta,
  handleReset,
  resetField,
  resetForm,
  setValues,
  setFieldValue,
  setErrors,
  setFieldError,
  defineField,
  onSubmit,
});
</script>

<style lang="scss" scoped>
form {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  :deep(.form-fieldset) {
    border: none;
    display: flex;
    flex-direction: column;
    &:has(.table-box) {
      flex-grow: 1;
    }
  }
}
</style>
