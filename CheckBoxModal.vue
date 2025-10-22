<template>
  <div>
    <div class="modal-main">
      <div class="input-box !gap-4">
        <template v-for="(item, index) in tableItem" :key="item.name">
          <Input
            v-if="item.isFieldSetting !== false"
            :item="{
              type: 'checkbox',
              name: item.name,
              label: item.label,
              hideShape: true,
              modelValue: item.name,
              controlled: false,
            }"
            v-model:modelValue="checkedValue" />
        </template>
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
const checkedValue = ref<string[]>(['birthday', 'test1']);

/** 表格項目 */
const tableItem = defineModel<TableItem[]>('tableItem', {
  type: Array,
  default: () => [],
});

/* 根據勾選同步更新 tableItem → hidden 狀態 */
watch(checkedValue, async (newVal, oldVal) => {
  // console.log(newVal);
  await updateTableItem({
    key: String(props.item.id),
    tableItem: tableItem,
    checkedValue: newVal,
  });
});

/* 初始化時同步 checkedValue 與 tableItem → hidden 狀態 */
onMounted(() => {
  // 先嘗試取出使用者的記錄
  const saved = sessionStorage.getItem(String(props.item.id));

  if (saved) {
    // 若有紀錄，直接還原
    checkedValue.value = JSON.parse(saved);
  } else {
    // 若無紀錄，根據 tableItem.hidden 初始化 checkedValue
    checkedValue.value = tableItem.value
      .filter((item) => item.hidden === undefined || item.hidden === false)
      .map((item) => item.name);

    // 若 hidden 是 undefined，統一初始化為 false
    // tableItem.value.forEach((item) => {
    //   if (item.hidden === undefined) item.hidden = false;
    // });
  }
  // console.log(checkedValue.value);

  // 初始化後馬上同步一次
  // tableItem.value.forEach((item) => {
  //   item.hidden = !checkedValue.value.includes(item.name);
  // });
});
</script>

<style lang="scss" scoped>
.center {
  > .main {
    padding: 12px 24px 12px 10px;
    > .input-box {
      flex-direction: column;
      > div {
        width: auto;
      }
    }
  }
}
</style>
