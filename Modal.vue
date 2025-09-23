<template>
  <Transition
    name="fade"
    @before-enter="beforeEnter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @after-leave="afterLeave">
    <Teleport defer :to="`#${id}`" :disabled="!useTeleport">
      <div
        v-if="modalOpen"
        :key="id"
        :class="[
          'modal-box gicons',
          {
            draggable,
            useTeleport,
            top: direction === 'top',
            right: direction === 'right',
            bottom: direction === 'bottom',
            left: direction === 'left',
          },
        ]"
        :style="{
          zIndex: zIndex || 'var(--z-index-centerModal)',
        }">
        <div v-if="backdrop" class="backdrop" @click="modalOpen = backdropDisabled"></div>
        <div
          class="content"
          :style="{
            maxWidth: maxWidth ? `${maxWidth}px` : `initial`,
          }">
          <div v-if="showTop" class="top" v-drag-move="draggable ? '.content' : false">
            <div v-if="subTitle || subTitleType[type]" class="sub-title font-small-2">
              {{ subTitle || subTitleType[type] }}
            </div>
            <div v-if="title" class="title font-small-1 font-bold">{{ title }}</div>
            <Button
              class="close-btn icon-style no-border"
              icon="close"
              :title="$t('Util.close')"
              @click="modalOpen = false" />
          </div>
          <div class="center">
            <component :is="component" :="{ item: props }" v-model:modalOpen="modalOpen">
              <template #bottom>
                <div class="bottom">
                  <Button :text="$t('Util.cancel')" @click="modalOpen = false" />
                  <Button class="c-primary" type="submit" :text="$t('Util.submit')" />
                </div>
              </template>
            </component>
          </div>
          <div v-if="showBottom" class="bottom">
            <Button :text="$t('Util.close')" @click="modalOpen = false" />
          </div>
          <CurrentLoading :show="modalLoading" />
        </div>
      </div>
    </Teleport>
  </Transition>
</template>

<script setup lang="ts">
const { locale, t, ct } = useI18nGlobal();

interface ModalProps {
  /** 彈出視窗的內容組件 */
  component?: Component;
  /** ID */
  id?: string;
  /** 迴圈的索引編號 */
  index?: number;
  /** 類型，影響副標題狀態顯示 */
  type?: 'add' | 'edit' | 'delete' | '';
  /** 是否顯示頂部 */
  showTop?: boolean;
  /** 是否顯示底部 */
  showBottom?: boolean;
  /** 頂部標題文字 */
  title?: string;
  /** 頂部左方的副標題文字 */
  subTitle?: string;
  /** 彈出視窗的 .content 最大寬度 */
  maxWidth?: number; // 最大寬度
  /** 彈出視窗的內容 .main 高度 */
  height?: number;
  /** 彈出視窗的內容 .main 最大高度 */
  maxHeight?: number;
  /** z-index */
  zIndex?: number;
  /** 是否顯示背景 */
  backdrop?: boolean;
  /** 是否禁用背景觸發事件 */
  backdropDisabled?: boolean;
  /** 是否顯示加載圖標 */
  modalLoading?: boolean;
  /** 是否在頂部進行拖曳 */
  draggable?: boolean;
  /** 彈出視窗的 DOM 是否傳送到指定的 DOM */
  useTeleport?: boolean;
  /** 彈出視窗顯示於哪個方向 */
  direction?: 'top' | 'right' | 'bottom' | 'left';
  /** 開啟前的前置動作 */
  onOpen?: () => void;
  /** 關閉前的前置動作 */
  onClose?: () => void;
  /** 關閉彈出視窗 */
  close?: () => void;
}
const props = withDefaults(defineProps<ModalProps>(), {
  component: () => {},
  id: () => `modal-${Date.now()}`,
  index: 0,
  type: '',
  showTop: true,
  showBottom: false,
  title: '',
  subTitle: '',
  maxWidth: 0,
  height: 0,
  maxHeight: 0,
  zIndex: 0,
  backdrop: false,
  backdropDisabled: false,
  modalLoading: false,
  draggable: false,
  useTeleport: false,
  direction: 'bottom',
  onOpen: () => {},
  onClose: () => {},
  close,
});

// 定義 modal 的顯示狀態
const modalOpen = ref<boolean>(false);

// 定義子標題
const subTitleType = ref<Record<string, string>>({
  add: t('Util.add'),
  edit: t('Util.edit'),
  delete: t('Util.delete'),
});

// 定義 modal main 的高度
const mainHeight = ref(props.height ? `${props.height}px` : 'auto');
const mainMaxHeight = ref(
  props.maxHeight ? `${props.maxHeight}px` : props.useTeleport ? 'initial' : 'calc(100vh - 116px)'
);

// modal 開啟前的前置動作
function beforeEnter() {
  if (props.backdrop) {
    // 隱藏 body 滾動條
    document.body.classList.add('overflow-hidden');
  }
  if (props.onOpen && typeof props.onOpen === 'function') {
    props.onOpen();
  }
}

// modal 開啟後的後續動作
function afterEnter() {}

// modal 關閉前的前置動作
function beforeLeave() {
  if (props.backdrop) {
    // 顯示 body 滾動條
    document.body.classList.remove('overflow-hidden');
  }
  if (props.onClose && typeof props.onClose === 'function') {
    props.onClose && props.onClose();
  }
}

// modal 關閉後的後續動作
function afterLeave() {
  props.close();
}

onMounted(() => {
  modalOpen.value = true;
  nextTick(() => {
    // 如果能拖動，則設置彈出視窗位置
    if (!props.draggable) return;
    const el = document.querySelector(`#${props.id} .content`) as HTMLElement;
    if (el) {
      const rect = el.getBoundingClientRect();
      const left = window.innerWidth - rect.width - 16;
      el.style.inset = `${48 + props.index * 16}px auto auto ${left}px`;
    }
  });
});
</script>

<style lang="scss" scoped>
.modal-box {
  pointer-events: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 12px;
  &.draggable {
    display: block;
    padding: 0px;
  }
  &.useTeleport {
    position: absolute;
    padding: 0px;
    &.bottom {
      top: 32px;
      left: initial;
      right: 6px;
      width: 240px;
    }
  }
  > .backdrop {
    pointer-events: auto;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: var(--background-mask);
  }
  > .content {
    pointer-events: auto;
    position: relative;
    width: 100%;
    background-color: var(--white);
    border: var(--border-1);
    border-radius: var(--border-radius-1);
    box-shadow: var(--box-shadow-2);
    > .top {
      position: relative;
      height: 36px;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 0 60px;
      border-radius: var(--border-radius-1) var(--border-radius-1) 0 0;
      border-bottom: var(--border-2);
      background: var(--surface);
      > :is(.sub-title, .close-btn) {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
      }
      > .sub-title {
        left: 12px;
      }
      > .close-btn {
        right: 2px;
        z-index: 1;
      }
    }
    > .center {
      > :deep(.center-box) {
        padding: 0px;
        display: flex;
        flex-direction: column;
        > .box {
          flex-grow: 1;
          display: flex;
          flex-direction: column;
          > .main {
            flex-grow: 1;
            overflow: auto;
            padding: 12px;
            height: v-bind(mainHeight);
            max-height: v-bind(mainMaxHeight);
          }
        }
      }
    }
    :is(.bottom) {
      flex: 0 0 56px;
      height: 56px;
      display: flex;
      align-items: center;
      justify-content: flex-end;
      gap: 8px;
      padding: 0 12px;
    }
  }
}
</style>
