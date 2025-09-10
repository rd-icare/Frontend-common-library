<template>
  <transition name="fade" @after-leave="afterLeave">
    <div
      v-if="modalShow"
      :key="modal.id"
      class="modal-box"
      :style="{ zIndex: modal.props.zIndex || 'var(--z-index-centerModal)' }">
      <div class="backdrop" @click="modalShow = false"></div>
      <div class="content">
        <div class="top">
          <h2>{{ modal.props.title }}</h2>
          <button class="close-button" @click="modalShow = false">×</button>
        </div>
        <div class="center">
          <component :is="modal.component" :="modal.props" @close="modal.close" />
        </div>
        <div class="bottom">
          <button class="close-button" @click="modalShow = false">關閉</button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup lang="ts">
// 定義 modal 的 props 型別
interface ModalProps {
  id: string | number;
  component: any;
  props: {
    title?: string;
    zIndex?: number;
    onClose?: () => void;
    [key: string]: any;
  };
  close: () => void;
}
const { modal } = defineProps<{
  modal: ModalProps;
}>();

// 定義 modal 的顯示狀態
const modalShow = ref<boolean>(false);

// modal 關閉後的後續動作
function afterLeave() {
  // 如果有 onClose 回調，則執行
  if (modal.props.onClose && typeof modal.props.onClose === 'function') {
    modal.props.onClose();
  }
  modal.close();
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
  /* z-index: 9999; */
  .backdrop {
    pointer-events: auto;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
  }
  .content {
    pointer-events: auto;
    position: relative;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    width: 90%;
    max-width: 500px;
    padding: 16px;
  }
  .top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }
  .center {
    overflow-y: auto;
    max-height: 80vh;
    padding: 16px;
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
