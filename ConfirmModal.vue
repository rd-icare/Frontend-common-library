<template>
  <div>
    <div class="main">
      <component v-if="item.subComponent" :is="item.subComponent" />
      <Text v-else :icon="item.subComponentIcon" :text="item.subComponentText" />
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

/** 處理點擊事件 */
function handleClick() {
  console.log('已觸發點擊');
}
</script>

<style lang="scss" scoped>
.center {
  > .main {
    padding: 12px;
  }
}
</style>
