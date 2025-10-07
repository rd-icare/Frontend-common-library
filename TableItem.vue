<template>
  <!-- thead 表頭 -->
  <template v-if="divType === 'thead'">
    <div
      v-for="({ name, label, style, hidden, sortable, component }, idx) in tableItem"
      :key="`thead-${name || idx}`"
      v-show="!hidden"
      :class="['th', `column-${idx + 1}`]"
      :style="style">
      <component v-if="component" :is="component" :name :item :index />
      <Text v-else class="font-bold" :title="label" :text="label" />
      <!-- 排序箭頭 -->
      <SortArrow
        v-if="sortable ?? true"
        :name="name"
        :idx="idx"
        :activeIndex="activeIndex"
        :sortState="idx === activeIndex ? sortState : false"
        @toggle="handleSort"
        @click="activeIndex = idx" />
    </div>
  </template>
  <!-- tbody 表身 -->
  <template v-else-if="divType === 'tbody'">
    <div
      v-for="({ name, style, hidden, dayjsFormat, component }, idx) in tableItem"
      :key="`tbody-${name || idx}`"
      v-show="!hidden"
      :class="['td', `column-${idx + 1}`]"
      :style="style">
      <component v-if="component" :is="component" :name :item :index />
      <Text v-else :title="valueFormat(item[name], dayjsFormat)" :text="valueFormat(item[name], dayjsFormat)" />
    </div>
  </template>
</template>

<script setup lang="ts">
import dayjs from 'dayjs';
// defineOptions({ inheritAttrs: false });
interface Props<T = Record<string, any>> {
  /** 元素類型 */
  divType: 'thead' | 'tbody';
  /** 表格項目資料 */
  tableItem: TableItem[];
  /** API 資料項目 */
  item?: T;
  /** API 資料項目索引 */
  index?: number;
  /** 函式 */
  clickFn?: (item: TableItemClickFn) => void;
}
const props = withDefaults(defineProps<Props>(), {
  divType: 'tbody',
  tableItem: () => [],
  item: () => ({}),
  index: 0,
  clickFn: () => {},
});
/** 當前激活表格項目索引 */
const activeIndex = ref(0);
/** 排序狀態 */
const sortState = ref(true);
/** 值的格式轉換 */
function valueFormat(value: any, dayjsFormat?: string) {
  return dayjsFormat ? dayjs(value).format(dayjsFormat) : value;
}
/** 排序 */
function handleSort({ name, sortState: state }: TableItemClickFn) {
  sortState.value = state;
  props.clickFn({ name, sortState: state });
}
onMounted(() => {
  // 預設第一個排序
  const firstSortable = props.tableItem.findIndex((item) => item.sortable);
  if (firstSortable >= 0) activeIndex.value = firstSortable;
});
</script>

<style lang="scss" scoped></style>
