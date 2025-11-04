<template>
  <div>
    <div class="modal-main">
      <component v-if="item.subComponent" :is="item.subComponent" />
      <div v-else-if="item.subComponentIcon || item.subComponentText">
        <Text typeStyle="icon-style" :icon="item.subComponentIcon" :text="item.subComponentText" />
      </div>
      <div v-else class="flex flex-col items-center gap-12">
        <div class="flex items-center">
          <div class="shrink-0">變更內容：</div>
          <Text
            typeStyle="round-style color-secondary"
            :text="`${typeSideMenu} / ${subPath}${customPaginName ? ` / ${customPaginName}` : ''}`" />
        </div>
        <Text text="離開前是否儲存本頁資料所做變更？" />
      </div>
    </div>
    <slot name="bottom" />
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const {} = storeToRefs(storeIndex);

const props = withDefaults(defineProps<ModalComponentProps>(), {
  item: () => ({}),
});

/** 彈出視窗顯示狀態 */
const modalOpen = defineModel<boolean>('modalOpen', {
  type: Boolean,
  default: true,
});

const { path, typeSideMenu, subPath, customPaginName } = props.item?.breadcrumbs ?? {};

/** 處理點擊事件 */
function handleClick() {
  console.log('已觸發點擊');
}
</script>

<style lang="scss" scoped>
.center {
  > .modal-main {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 12px;
  }
  :deep(.bottom) {
    justify-content: center !important;
  }
}
</style>
