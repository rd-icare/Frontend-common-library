<template>
  <Transition
    name="fade"
    @before-enter="beforeEnter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @after-leave="afterLeave">
    <Teleport defer :to="`#${id}`" :disabled="!optionsMode">
      <div
        v-if="modalOpen"
        tabindex="-1"
        :key="id"
        :class="[
          'modal-box gicons',
          {
            draggable,
            optionsMode,
            topLeft: direction === 'topLeft',
            topRight: direction === 'topRight',
            rightTop: direction === 'rightTop',
            bottomLeft: direction === 'bottomLeft',
            bottomRight: direction === 'bottomRight',
            leftTop: direction === 'leftTop',
          },
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
// import type { ModalProps } from '@/types/index';
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
  backdropDisabled: false,
  modalLoading: false,
  draggable: false,
  optionsMode: false,
  direction: 'bottomRight',
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
    props.onClose && props.onClose();
  }
}

/** 關閉後的後續動作 */
function afterLeave() {
  props.close();
}

/** 模糊失焦 */
function blur(e: FocusEvent) {
  console.log(`${props.id} blur`, e);
  // if (props.optionsMode) {
  //   modalOpen.value = false;
  // }
}

onMounted(() => {
  modalOpen.value = true;
  nextTick(() => {
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
  &.optionsMode {
    position: absolute;
    inset: auto;
    width: max-content;
    padding: 0px;
    &:is(.topLeft, .topRight) {
      bottom: 100%;
      align-items: flex-end;
      padding-bottom: 4px;
    }
    &:is(.bottomLeft, .bottomRight) {
      top: 100%;
      align-items: flex-start;
      padding-top: 4px;
    }
    &:is(.topLeft, .bottomLeft) {
      left: 6px;
    }
    &:is(.topRight, .bottomRight) {
      right: 6px;
    }
    &:is(.leftTop, .rightTop) {
      top: 0px;
      align-items: flex-start;
    }
    &.leftTop {
      left: calc(100% - 6px);
      padding-left: 4px;
    }
    &.rightTop {
      right: calc(100% - 6px);
      padding-right: 4px;
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
