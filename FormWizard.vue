<template>
  <form @submit="onSubmit" @keypress.enter="(e) => (!useKeypressEnter ? e.preventDefault() : null)" novalidate>
    <slot name="content" :values="values" :errors="errors" :handleReset="handleReset" :resetForm="resetForm" />
    <div
      class="formValuesBox"
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
  initialValues?: Record<string, any>;
  schema?: Record<string, any>;
  onInvalidSubmit?: (values: Record<string, any>, errors: Record<string, any>, results: Record<string, any>) => void;
  keepValues?: boolean;
  useKeypressEnter?: boolean;
  showFormValues?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  initialValues: () => ({}), // 初始值
  schema: () => ({}), // 有效性驗證,
  onInvalidSubmit: () => {}, // 無效提交
  keepValues: true, // 保持值
  useKeypressEnter: false, // 使用 enter 鍵提交
  showFormValues: false, // 顯示表單值
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

defineExpose({
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
