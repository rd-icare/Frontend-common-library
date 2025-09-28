<template>
  <form @submit="onSubmit" @keypress.enter="(e) => (!useKeypressEnter ? e.preventDefault() : null)" novalidate>
    <slot name="content" :values="values" :errors="errors" :handleReset="handleReset" :resetForm="resetForm" />
    <div
      class="form-values-box"
      :class="{ active }"
      :style="{ display: showFormValues ? 'block' : 'none' }"
      @click.self="active = !active">
      <pre>values: {{ values }}</pre>
      <pre>errors: {{ errors }}</pre>
    </div>
  </form>
</template>

<script setup lang="ts">
import { useForm, type SubmissionContext } from 'vee-validate';

interface Props {
  /** 初始值 */
  initialValues?: Record<string, any>;
  /** 有效性驗證 */
  schema?: Record<string, any>;
  /** 無效提交 */
  onInvalidSubmit?: (values: Record<string, any>, errors: Record<string, any>, results: Record<string, any>) => void;
  /** 保持值 */
  keepValues?: boolean;
  /** 使用 enter 鍵提交 */
  useKeypressEnter?: boolean;
  /** 顯示表單值 */
  showFormValues?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  initialValues: () => ({}),
  schema: () => ({}),
  onInvalidSubmit: () => {},
  keepValues: true,
  useKeypressEnter: false,
  showFormValues: false,
});

const emit = defineEmits<{
  (e: 'submit', values: Record<string, any>, actions: SubmissionContext<any>): void;
  (e: 'invalidSubmit', values: Record<string, any>, errors: Record<string, any>, results: Record<string, any>): void;
}>();

const active = ref(true);

const {
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
} = useForm({
  initialValues: props.initialValues,
  validationSchema: computed(() => props.schema),
  keepValuesOnUnmount: props.keepValues,
});

const onSubmit = handleSubmit(
  (values, actions) => {
    emit('submit', values, actions);
  },
  (results) => {
    emit('invalidSubmit', values, errors, results);
  }
);

 /** 定義 vee-validate 表單暴露型別 */
export interface _VeeFormExpose {
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
}
defineExpose<VeeFormExpose>({
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
});
</script>

<style lang="scss" scoped></style>
