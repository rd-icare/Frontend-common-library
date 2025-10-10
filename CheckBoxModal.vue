<template>
  <div>
    <div class="main">
      <div>
        <input type="checkbox" id="case_no" value="case_no" v-model="checkedData" />
        <label for="case_no">案號</label>
      </div>
      <div>
        <input type="checkbox" id="name" value="name" v-model="checkedData" />
        <label for="name">姓名</label>
      </div>
      <div>
        <input type="checkbox" id="birthday" value="birthday" v-model="checkedData" />
        <label for="birthday">生日</label>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const {} = storeToRefs(storeIndex);
const { updateTableItem } = storeIndex;

const props = withDefaults(defineProps<ModalComponentProps>(), {
  item: () => ({}),
});

/** 已勾選的資料 */
const checkedData = ref<string[]>([]);

/** 表格項目 */
const tableItem = defineModel<TableItem[]>('tableItem', {
  type: Array,
  default: () => [],
});

/* 根據勾選同步更新 tableItem -> hidden 狀態 */
watch(checkedData, (newVal) => {
  updateTableItem({
    key: String(props.item.id),
    tableItem: tableItem,
    checkedValue: newVal,
  });
});

/* 初始化時同步 checkedData 與 tableItem -> hidden 狀態 */
onMounted(() => {
  // 先嘗試取出使用者的記錄
  const saved = sessionStorage.getItem(String(props.item.id));

  if (saved) {
    // 若有紀錄，直接還原
    checkedData.value = JSON.parse(saved);
  } else {
    // 若無紀錄，根據 tableItem.hidden 初始化 checkedData
    checkedData.value = tableItem.value
      .filter((item) => item.hidden === undefined || item.hidden === false)
      .map((item) => item.name);

    // 若 hidden 是 undefined，統一初始化為 false
    // tableItem.value.forEach((item) => {
    //   if (item.hidden === undefined) item.hidden = false;
    // });
  }

  // 初始化後馬上同步一次
  // tableItem.value.forEach((item) => {
  //   item.hidden = !checkedData.value.includes(item.name);
  // });
});
</script>

<style lang="scss" scoped>
.center {
  > .main {
    padding: 12px;
  }
}
</style>
