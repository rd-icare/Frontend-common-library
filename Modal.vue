<template>
  <Transition
    name="fade"
    @before-enter="beforeEnter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @after-leave="afterLeave">
    <div
      v-if="modalOpen"
      tabindex="-1"
      :key="id"
      :class="[
        'modal-box gicons',
        {
          draggable,
          'options-mode': optionsMode,
          'top-left': direction === 'topLeft',
          'top-right': direction === 'topRight',
          'right-top': direction === 'rightTop',
          'bottom-left': direction === 'bottomLeft',
          'bottom-right': direction === 'bottomRight',
          'left-top': direction === 'leftTop',
        },
        optionsMode ? `options-mode-${id}` : '',
      ]"
      :style="{
        zIndex: zIndex || 'var(--z-index-centerModal)',
      }"
      @blur="optionsMode ? blur($event) : ''">
      <div v-if="backdrop && !draggable && !optionsMode" class="backdrop" @click="modalOpen = backdropDisabled"></div>
      <div
        class="content"
        :style="{
          width: maxWidth && draggable ? `${maxWidth}px` : '',
          maxWidth: maxWidth && !draggable ? `${maxWidth}px` : '',
        }">
        <div v-if="showTop && !optionsMode" class="top" v-drag-move="draggable ? '.content' : false">
          <div v-if="subTitle || subTitleType[type]" class="sub-title font-small-2">
            {{ subTitle || subTitleType[type] }}
          </div>
          <div v-if="title" class="title text-ellipsis font-small-1 font-bold" title="">{{ title }}</div>
          <Button
            class="close-btn icon-style no-border"
            icon="close"
            :title="$t('Util.close')"
            @click="modalOpen = false" />
        </div>
        <!-- 插槽 -->
        <template v-if="!component">
          <slot>
            <div class="center">
              <div class="main">Demo</div>
            </div>
          </slot>
        </template>
        <slot :modalOpen="modalOpen"> </slot>
        <!-- 插入組件 -->
        <component :is="component" :="{ item: props }" v-model:modalOpen="modalOpen">
          <template #bottom>
            <div class="bottom">
              <Button :text="$t('Util.cancel')" @click="modalOpen = false" />
              <Button class="c-primary" type="submit" :text="$t('Util.submit')" />
            </div>
          </template>
        </component>
        <div v-if="showBottom" class="bottom">
          <Button :text="$t('Util.close')" @click="modalOpen = false" />
        </div>
        <CurrentLoading :show="modalLoading" />
      </div>
    </div>
  </Transition>
</template>

<script setup lang="ts">
// import type { ModalProps } from '@/types/index';
const storeIndex = useIndexStore();
const { modals } = storeToRefs(storeIndex);
const { locale, t, ct } = useI18nGlobal();

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
  backdrop: true,
  backdropDisabled: true,
  modalLoading: false,
  draggable: false,
  optionsMode: false,
  direction: '',
  optionsModeId: '',
  itemHeight: 0,
  onOpen: () => {},
  onClose: () => {},
  close,
});

/** 是否開啟彈出視窗 */
const modalOpen = ref<boolean>(false);

/** 副標題類型文字顯示 */
const subTitleType = ref<Record<string, string>>({
  add: t('Util.add'),
  edit: t('Util.edit'),
  delete: t('Util.delete'),
});

/** 彈出視窗 main 的高度 */
const mainHeight = ref(props.height ? `${props.height}px` : '');
const mainMaxHeight = ref(props.maxHeight ? `${props.maxHeight}px` : props.optionsMode ? '' : 'calc(100vh - 116px)');

/** 開啟彈出視窗 */
const itemHeight = ref(`${props.itemHeight * props.index}px` || '0px');

/** 是否為中間彈出視窗 */
const isCenterModal = props.backdrop && !props.draggable && !props.optionsMode;

/** 開啟前的前置動作 */
function beforeEnter() {
  if (isCenterModal) {
    // 隱藏 body 滾動條
    document.body.classList.add('overflow-hidden');
  }
  if (props.onOpen && typeof props.onOpen === 'function') {
    props.onOpen();
  }
}

/** 開啟後的後續動作 */
function afterEnter() {}

/** 關閉前的前置動作 */
function beforeLeave() {
  if (isCenterModal) {
    // 顯示 body 滾動條
    document.body.classList.remove('overflow-hidden');
  }
  if (props.onClose && typeof props.onClose === 'function') {
    props.onClose();
  }
}

/** 關閉後的後續動作 */
function afterLeave() {
  props.close();
}

/** 聚焦 */
function focus(e: any) {
  // console.log(`${props.id} focus`, e);
}

/** 失焦 */
function blur(e: any) {
  // console.log(`${props.id} blur`, e);
  // 如果分頁不在前景 → 不處理
  if (document.hidden || !document.hasFocus()) {
    return;
  }
  // 如果關閉的不是選項模式
  if (!e.relatedTarget?.className.includes('options-mode')) {
    // modalOpen.value = false;
    Object.keys(modals.value).forEach((key, index) => {
      if (modals.value[key].optionsMode) {
        modals.value[key].modalOpen = false;
      }
    });
  }
}

/** 分享變量與方法，在 useModalManager.ts 內 return 也要同步  */
defineExpose({
  optionsMode: props.optionsMode,
  modalOpen,
});

onMounted(() => {
  modalOpen.value = true;
  nextTick(() => {
    // console.log(`id: ${props.id} index: ${props.index} itemHeight: ${props.itemHeight} mounted`);
    // 如果是拖曳模式
    if (props.draggable) {
      const el = document.querySelector(`#${props.id} .content`) as HTMLElement;
      if (el) {
        const rect = el.getBoundingClientRect();
        const left = window.innerWidth - rect.width - 16;
        el.style.inset = `${48 + props.index * 16}px auto auto ${left}px`;
      }
      return;
    }
    // 如果是選項模式
    if (props.optionsMode) {
      const el = document.querySelector(`#${props.id} .modal-box`) as HTMLElement;
      // 聚焦最後啟用的彈出視窗
      if (el) el.focus();
      return;
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
  &:focus {
    outline: 0;
  }
  /* 在頂部進行拖曳 */
  &.draggable {
    /* position: absolute;
    width: auto;
    height: auto; */
    display: block;
    /* align-items: flex-start;
    justify-content: flex-start; */
    padding: 0px;
  }
  /* 使用傳送到指定的 DOM */
  &.options-mode {
    position: absolute;
    inset: auto;
    width: max-content;
    height: max-content;
    padding: 0px;
    &:is(.top-left, .top-right) {
      bottom: 100%;
      align-items: flex-end;
      margin-bottom: 4px;
    }
    &:is(.bottom-left, .bottom-right) {
      top: 100%;
      align-items: flex-start;
      margin-top: 4px;
    }
    &:is(.top-left, .bottom-left) {
      left: 0px;
    }
    &:is(.top-right, .bottom-right) {
      right: 0px;
    }
    &:is(.left-top, .right-top) {
      top: v-bind(itemHeight);
      align-items: flex-start;
    }
    &.right-top {
      left: calc(100% - 0px);
      margin-left: 4px;
    }
    &.left-top {
      right: calc(100% - 0px);
      margin-right: 4px;
    }
  }
  > .backdrop {
    pointer-events: auto;
    position: fixed;
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
      padding: 0 64px;
      border-radius: var(--border-radius-1) var(--border-radius-1) 0 0;
      border-bottom: var(--border-2);
      background-color: var(--surface);
      > :is(.sub-title, .close-btn) {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
      }
      > .sub-title {
        left: 12px;
      }
      > .close-btn {
        right: 0px;
        z-index: 1;
      }
    }
    > :deep(.center) {
      display: flex;
      flex-direction: column;
      > .main {
        flex-grow: 1;
        overflow: auto;
        height: v-bind(mainHeight);
        max-height: v-bind(mainMaxHeight);
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
      border-radius: 0 0 var(--border-radius-1) var(--border-radius-1);
      border-top: var(--border-2);
      background-color: transparent;
    }
  }
}
</style>
