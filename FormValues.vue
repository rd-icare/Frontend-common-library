<template>
  <div
    ref="formValuesRef"
    class="form-values"
    :class="{ active }"
    :style="{ width: boxWidth + 'px' }"
    @click.self="active = !active">
    <div class="content">
      <pre>values: {{ values }}</pre>
      <pre>errors: {{ errors }}</pre>
      <pre>meta: {{ meta }}</pre>
    </div>
    <div v-if="!active" class="resizer" @mousedown="startResize"></div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  values: Record<string, any>;
  errors: Record<string, any>;
  meta: Record<string, any>;
}

defineProps<Props>();

const active = ref(true);
const boxWidth = ref(360);
const resizing = ref(false);
const startX = ref(0);
const startWidth = ref(0);
const formValuesRef = ref<HTMLElement | null>(null);

const startResize = (e: MouseEvent) => {
  e.preventDefault();
  resizing.value = true;
  startX.value = e.clientX;
  startWidth.value = formValuesRef.value?.offsetWidth || 360;

  document.addEventListener('mousemove', resizeBox);
  document.addEventListener('mouseup', stopResize);
};

const resizeBox = (e: MouseEvent) => {
  if (!resizing.value) return;
  const deltaX = e.clientX - startX.value;
  boxWidth.value = Math.max(200, startWidth.value + deltaX);
};

const stopResize = () => {
  resizing.value = false;
  document.removeEventListener('mousemove', resizeBox);
  document.removeEventListener('mouseup', stopResize);
};

onBeforeUnmount(stopResize);
</script>

<style lang="scss" scoped>
/* 表單值樣式 */
.form-values {
  overflow: hidden;
  height: calc(100% - 40px);
  position: fixed;
  top: 0;
  left: 0;
  z-index: 99999;
  display: block;
  font-size: 14px;
  background-color: var(--componets-fill);
  border: var(--border-1);
  border-radius: var(--border-radius-1);
  opacity: 0.95;

  .content {
    overflow-y: auto;
    height: 100%;
    padding: 16px;
  }

  &.active {
    z-index: 1;
    overflow: hidden;
    width: 16px !important;
    height: 16px;
    padding: 8px;
  }

  /* 右側可拖曳區域 */
  .resizer {
    position: absolute;
    top: 0;
    right: 0;
    z-index: 2;
    width: 6px;
    height: 100%;
    cursor: ew-resize;
    background-color: transparent;
    transition: background-color 0.2s;

    &:hover {
      background-color: rgba(0, 0, 0, 0.1);
    }
  }
}
</style>
