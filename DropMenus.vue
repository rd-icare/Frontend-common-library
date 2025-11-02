<template>
  <div class="drop-menus-box">
    <div
      v-for="(value, key, index) in data"
      :key="key"
      :class="['item', { active: activeBtn[key] }]"
      @click="
        clickFn({ key }),
          key !== 'logout' &&
            handleModal({
              key: key,
              payload: {
                index: index,
                optionsMode: true,
                direction: 'leftTop',
                optionsModeId,
                itemHeight: itemHeight,
              },
            })
      ">
      <Icon icon="chevron_left" />
      <Text :text="value.text" />
    </div>
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const { modals } = storeToRefs(storeIndex);
const {} = storeIndex;

interface Props {
  /** 彈出視窗選項模式的子項 ID 名稱
   * 命名方式為小駝峰，調用此組件的組件名稱 */
  optionsModeId?: string;
  /** 當前頁面類型
   * 命名方式為中線 */
  pageType?: string;
  /** 目錄名稱
   * 命名方式為中線，調用此組件的組件名稱 */
  dirName?: string;
  /** 拉下式選單資料 */
  data?: Record<string, any>;
  /** 返回點擊事件 */
  clickFn?: (item: DropMenusClickFn) => void;
}

const props = withDefaults(defineProps<Props>(), {
  optionsModeId: '',
  pageType: '',
  dirName: '',
  data: () => ({}),
  clickFn: () => {},
});

/** 當前啟用按鈕 */
const activeBtn = ref<Record<string, boolean>>(
  Object.fromEntries(Object.entries(props.data).map(([key, value], index) => [key, false]))
);

/** 當前啟用的對象 */
const currActiveKey = ref<string>('');

/** 項目高度 */
const itemHeight = ref(36);

/** 處理彈出視窗 */
async function handleModal({ key, payload }: { key: string; payload: ModalProps }) {
  // 當前啟用 => 關閉
  if (activeBtn.value[key]) {
    modals.value[key].close();
    onClose();
    return;
  }

  // 關閉上一個
  if (currActiveKey.value) {
    modals.value[currActiveKey.value].close();
    activeBtn.value[currActiveKey.value] = false;
  }

  // 首字母大寫
  const keyFirst = key.charAt(0).toUpperCase() + key.slice(1);
  // console.log(keyFirst);

  // 創建
  const modal = createModal({
    component: defineAsyncComponent(() => import(`@/components/home/${props.pageType}/${props.dirName}/${keyFirst}.vue`)),
    id: key,
    ...payload,
    onOpen: () => {
      activeBtn.value[key] = true;
      currActiveKey.value = key;
    },
    onClose,
  });
  
  // 關閉
  function onClose() {
    activeBtn.value[key] = false;
    currActiveKey.value = '';
  }
}
</script>

<style lang="scss" scoped>
.drop-menus-box {
  display: flex;
  flex-direction: column;
  > .item {
    cursor: pointer;
    flex: 0 0 calc(v-bind(itemHeight) * 1px);
    display: flex;
    align-items: center;
    border: 1px solid transparent;
    border-bottom: var(--border-2);
    transition: var(--transition-fast);
    &:last-child {
      border-bottom: none;
    }
    &:hover {
      background-color: var(--surface);
    }
    > .icon {
      flex: 0 0 var(--box-height);
      width: var(--box-height);
      height: var(--box-height);
      display: flex;
      align-items: center;
      justify-content: center;
    }
    > .text {
      flex-grow: 1;
      padding: 0 16px 0 8px;
    }
    &.active {
      @include active-style;
    }
  }
}
</style>
