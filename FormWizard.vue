<template>
  <form @submit="onSubmit" @keypress.enter="(e) => (!useEnter ? e.preventDefault() : null)" novalidate>
    <!-- 用 fieldset 將整個 slot 包起來：HTML 會把所有子 input/textarea/select/button disabled -->
    <fieldset :disabled="isReadOnly" class="form-fieldset">
      <!-- 插槽 表單內容 -->
      <slot name="content" :values="values" :errors="errors" :handleReset="handleReset" />
    </fieldset>
    <!-- 插槽 表單底部 -->
    <slot name="bottom" :handleReset="handleReset" :resetForm="resetForm" />
    <slot />
    <!-- debug -->
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
import { useForm, type SubmissionContext, type InvalidSubmissionContext } from 'vee-validate';

interface Props<T extends Record<string, any>> {
  /** 表單名稱 */
  name?: string;
  /** 初始值 */
  initialValues?: T;
  /** 有效性驗證 */
  schema?: T;
  /** 保持值 */
  keepValues?: boolean;
  /** 是否使用 enter 鍵 */
  useEnter?: boolean;
  /** 顯示表單值 */
  showFormValues?: boolean;
}

const props = withDefaults(defineProps<Props<any>>(), {
  name: 'edit-form',
  initialValues: () => ({}),
  schema: () => ({}),
  keepValues: true,
  useEnter: false,
  showFormValues: false,
});

const emit = defineEmits<{
  <T extends Record<string, any>>(e: 'submit', values: T, actions: SubmissionContext<T>): void;
  <T extends Record<string, any>>(e: 'invalidSubmit', values: T, errors: T, results: InvalidSubmissionContext<T>): void;
  (e: 'update:readonly', val: boolean): void;
}>();

const isReadOnly = defineModel<boolean | undefined>('readonly', {
  type: Boolean || undefined,
  default: undefined,
});
const active = ref(true);

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
});

const onSubmit = handleSubmit(
  (values, actions) => {
    emit('submit', values, actions);
  },
  (results) => {
    emit('invalidSubmit', values as any, errors, results);
  }
);

// optional: 當切回只讀時，自動還原到 initialValues（視需求可移除）
watch(
  () => isReadOnly.value,
  (bool) => {
    if (bool) {
      // resetForm 會把 values 還原為 initialValues（也會重置 touched），避免未儲存的改動殘留
      resetForm({ values: props.initialValues });
    }
  }
);

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
  > .form-fieldset {
    border: none;
    display: flex;
    flex-direction: column;
    &:has(.table-box) {
      flex-grow: 1;
    }
  }
}
</style>
