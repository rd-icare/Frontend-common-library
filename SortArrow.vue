<template>
  <div class="arrow-box" @click="toggleSort">
    <span v-if="idx === activeIndex">
      {{ sortState ? 'arrow_drop_down' : 'arrow_drop_up' }}
    </span>
    <span v-else style="opacity: 0.5">arrow_drop_down</span>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 欄位名稱 */
  name: string;
  /** 表格項目索引 */
  idx: number;
  /** 當前激活表格項目索引 */
  activeIndex: number;
  /** 排序狀態 */
  sortState: boolean;
}
const props = defineProps<Props>();
const emit = defineEmits<{ (e: 'toggle', payload: TableItemClickFn): void }>();
/** 切換排序 */
function toggleSort() {
  emit('toggle', { name: props.name, sortState: !props.sortState });
}
</script>

<style lang="scss" scoped>
.arrow-box {
  cursor: pointer;
  position: absolute;
  inset: 0 2px 0 0;
  /* top: 0px;
  left: 0px;
  width: 100%;
  height: 100%; */
  display: flex;
  align-items: center;
  justify-content: flex-end;
  > span {
    font-size: 28px;
  }
}
</style>
