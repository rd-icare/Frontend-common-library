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
      <Text v-else class="font-bold" :class="{ 'pr-16': sortable }" :title="label" :text="label" />
      <!-- 排序箭頭 -->
      <SortArrow
        v-if="sortable ?? true"
        :name="name"
        :idx="idx"
        :activeIndex="activeIndex"
        :sortState="idx === activeIndex ? currSortState : false"
        :title="label"
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
      <Text
        v-else
        :title="valueFormat(name, dayjsFormat, item[name])"
        :text="valueFormat(name, dayjsFormat, item[name])" />
    </div>
  </template>
</template>

<script setup lang="ts">
import dayjs from 'dayjs';
// defineOptions({ inheritAttrs: false });
interface Props {
  /** 元素類型 */
  divType: 'thead' | 'tbody';
  /** 表格項目資料 */
  tableItem: TableItem[];
  /** API 資料項目 */
  item?: any;
  /** API 資料項目索引 */
  index?: number;
  /** API 資料項目序號 */
  sn?: number;
  /** 函式 */
  clickFn?: (item: TableItemClickFn) => void;
}
const props = withDefaults(defineProps<Props>(), {
  divType: 'tbody',
  tableItem: () => [],
  item: () => ({}),
  index: 0,
  currentPage: 1,
  clickFn: () => {},
});
/** 當前欄位激活索引 */
const activeIndex = ref<number>(0);
/** 當前欄位排序狀態 */
const currSortState = ref<boolean>(true);
/** 值的格式轉換 */
function valueFormat(name: string, dayjsFormat?: string, value?: any) {
  // 每頁都要顯示序號
  if (props.divType === 'tbody' && name === 'sn') return props.sn;
  // 空值
  if (!value) return '';
  // 日期格式
  if (dayjsFormat) return dayjs(value).format(dayjsFormat);
  // 預設
  return value;
}
/** 排序 */
function handleSort({ name, sortState }: TableItemClickFn) {
  currSortState.value = sortState as boolean;
  props.clickFn({ name, sortStateStr: sortState ? 'asc' : 'desc' });
}
onMounted(() => {
  /** 預設左邊第一個欄位排序，忽略 false */
  // const firstSortable = props.tableItem.findIndex((item) => {
  //   return item.sortable !== false;
  // });
  // if (firstSortable >= 0) activeIndex.value = firstSortable;
});
</script>

<style lang="scss" scoped></style>
