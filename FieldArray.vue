<template>
  <slot name="content" :="{ fields, remove, replace, update, push, swap, insert, prepend, move }" />
</template>

<!-- generic="T extends Record<string, any>" -->

<script setup lang="ts" generic="T">
import { useFieldArray, type FieldArrayContext, type FieldEntry } from 'vee-validate';

interface Props {
  /** 欲綁定的欄位名稱，例如 "users" 或 "addresses" */
  fieldName: string;
}

const props = withDefaults(defineProps<Props>(), {
  fieldName: '',
});

// 定義 slots 型別，讓父層可透過 content slot 取用方法
defineSlots<{
  content: (args: {
    fields: FieldEntry<T>[];
    remove: (idx: number) => void;
    replace: (value: T[]) => void;
    update: (idx: number, value: T) => void;
    push: (value: T) => void;
    swap: (a: number, b: number) => void;
    insert: (idx: number, value: T) => void;
    prepend: (value: T) => void;
    move: (from: number, to: number) => void;
  }) => void;
}>();

// 使用 useFieldArray 並完整解構
const { fields, remove, replace, update, push, swap, insert, prepend, move }: FieldArrayContext<T> = useFieldArray<T>(
  props.fieldName
);
</script>

<style scoped lang="scss"></style>
