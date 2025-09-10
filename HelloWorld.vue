<template>
  <div>
    <h1>{{ msg }}</h1>
    <button type="button" @click="count++">count is {{ count }}</button>
  </div>
</template>

<script setup lang="ts">
defineProps<{ msg: string }>();

const count = ref<number>(0);
</script>

<style lang="scss" scoped></style>
