<template>
  <div class="icon-button">
    <Button class="fill no-border icon-style" :title="title" :icon="icon" @click="clickFn({ type: 'click' })" />
    <Button
      v-if="!readonly"
      class="no-border icon-style delete"
      :title="$t('Util.delete')"
      icon="cancel"
      @click="clickFn({ type: 'delete' })" />
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const { readonly } = storeToRefs(storeIndex);

interface Props {
  /** 標題提示文字 */
  title?: string;
  /** 圖標 */
  icon?: string;
  /** 返回點擊事件 */
  clickFn?: (item: IconButtonClickFn) => void;
}

withDefaults(defineProps<Props>(), {
  title: '',
  icon: 'folder',
  clickFn: () => {},
});
</script>

<style lang="scss" scoped>
.icon-button {
  user-select: none;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  > :deep(.delete) {
    position: absolute;
    top: 0px;
    right: -4px;
    width: 18px !important;
    height: 18px !important;
    border-radius: 50%;
    background-color: var(--white) !important;
    box-shadow: var(--box-shadow-1);
    > span {
      font-size: 18px;
    }
  }
}
</style>
