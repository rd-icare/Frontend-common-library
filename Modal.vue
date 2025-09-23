<template>
  <transition name="fade" @after-enter="afterEnter" @after-leave="afterLeave">
    <div
      v-if="modalShow"
      :key="id"
      class="modal-box"
      :style="{
        zIndex: zIndex || 'var(--z-index-centerModal)',
      }">
      <div v-if="backdrop" class="backdrop" @click="modalShow = false"></div>
      <div
        class="content"
        :style="{
          maxWidth: `${maxWidth}px`,
        }">
        <div class="top">
          <h2>{{ title }}</h2>
          <button class="close-button" @click="modalShow = false">×</button>
        </div>
        <div
          class="center"
          :style="{
            height: height ? `${height}px` : 'auto',
            maxHeight: maxHeight ? `${maxHeight}px` : '80vh',
          }">
          <component :is="component" :="props" @close="close" />
        </div>
        <div class="bottom">
          <button class="close-button" @click="modalShow = false">關閉</button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup lang="ts">
// 定義 Props
interface ModalProps {
  component?: any;
  id?: string;
  title?: string;
  maxWidth?: number;
  height?: number;
  maxHeight?: number;
  zIndex?: number;
  backdrop?: boolean;
  onOpen?: () => void;
  onClose?: () => void;
  close: () => void;
}
const props = withDefaults(defineProps<ModalProps>(), {
  component: null,
  id: () => `modal-${Date.now()}`,
  title: '',
  maxWidth: 480,
  height: 0,
  maxHeight: 0,
  zIndex: 0,
  backdrop: true,
  onOpen: () => {},
  onClose: () => {},
});

// 定義 modal 的顯示狀態
const modalShow = ref<boolean>(false);

// modal 開啟後的後續動作
function afterEnter() {
  // 如果有 onOpen 回調，則執行
  if (props.onOpen && typeof props.onOpen === 'function') {
    props.onOpen();
  }
}

// modal 關閉後的後續動作
function afterLeave() {
  // 如果有 onClose 回調，則執行
  if (props.onClose && typeof props.onClose === 'function') {
    props.onClose && props.onClose();
  }
  props.close();
}

onMounted(() => {
  modalShow.value = true;
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
  .backdrop {
    pointer-events: auto;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: var(--background-mask);
  }
  .content {
    pointer-events: auto;
    position: relative;
    width: 100%;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  .top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }
  .center {
    overflow-y: auto;
    padding: 16px;
    > .center-content {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
    }
  }
  .bottom {
    display: flex;
    justify-content: flex-end;
    margin-top: 16px;
  }
  .close-button {
    background: none;
    border: none;
    font-size: 16px;
    cursor: pointer;
    color: #333;
  }
}
</style>
