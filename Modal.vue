<template>
  <transition
    name="fade"
    @before-enter="beforeEnter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @after-leave="afterLeave">
    <div
      v-if="modalOpen"
      :key="id"
      :class="['modal-box gicons', { draggable }]"
      :style="{
        zIndex: zIndex || 'var(--z-index-centerModal)',
      }">
      <div v-if="backdrop" class="backdrop" @click="modalOpen = backdropDisabled"></div>
      <div
        class="content"
        :style="{
          maxWidth: `${maxWidth}px`,
          inset: inset,
        }">
        <div v-if="showTop" class="top">
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
  </transition>
</template>

<script setup lang="ts">
import Button from './Button.vue';
import type { ModalProps } from '@/composables/useModalManager';
const { locale, t, ct } = useI18nGlobal();

// 定義 Props
const props = withDefaults(defineProps<ModalProps>(), {
  component: () => {},
  id: () => `modal-${Date.now()}`,
  type: '',
  showTop: true,
  showBottom: false,
  title: '',
  subTitle: '',
  maxWidth: 480,
  height: 0,
  maxHeight: 0,
  zIndex: 0,
  backdrop: true,
  backdropDisabled: false,
  modalLoading: false,
  draggable: false,
  inset: '0 0 0 0',
  onOpen: () => {},
  onClose: () => {},
  close,
});

// 定義 modal 的顯示狀態
const modalOpen = ref<boolean>(false);

const subTitleType = ref<Record<string, string>>({
  add: t('Util.add'),
  edit: t('Util.edit'),
  delete: t('Util.delete'),
});

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
  padding: 16px;
  &.draggable {
    align-items: start;
    justify-content: flex-end;
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
