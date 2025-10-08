<template>
  <div class="relative flex justify-end mb-4" :class="`modal-${modalKey}`">
    <Button
      :class="modalKey"
      :typeStyle="'select-style'"
      :text="text"
      @clickFn="selectClick"
      v-model:selectState="selectState" />
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const { modals } = storeToRefs(storeIndex);
const {} = storeIndex;

interface Props {
  /** 彈出視窗 key */
  modalKey?: string;
  /** 按鈕文字 */
  text?: string;
  /** 彈出視窗的內容組件 */
  component?: Component;
}

const props = withDefaults(defineProps<Props>(), {
  modalKey: '',
  text: '',
  component: () => {},
});

/** 選擇狀態 */
const selectState = ref(false);

/** 欄位設定 */
function selectClick() {
  handleModal({
    key: props.modalKey,
    payload: {
      // component: defineAsyncComponent(() => import(`@/components/home/modal/xxx.vue`)),
      component: props.component,
      optionsMode: true,
      direction: 'bottomRight',
    },
  });
}

/** 處理彈出視窗 */
async function handleModal({ key, payload }: { key: string; payload: ModalProps }) {
  // 當前啟用 => 關閉
  if (modals.value[key]) {
    modals.value[key].close();
    onClose();
    return;
  }
  // 創建
  createModal({
    id: key,
    ...payload,
    onOpen: () => {},
    onClose,
  });
  // 關閉
  function onClose() {
    selectState.value = false;
  }
}
</script>

<style lang="scss" scoped></style>
