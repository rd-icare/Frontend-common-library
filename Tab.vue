<template>
  <div v-if="Object.keys(data || {}).length" class="tab-box">
    <div
      v-for="(item, index) in data"
      :key="item.text"
      :class="[
        'item whitespace-nowrap',
        { active: item.id ? item.id === routeParamsId : item.sub_path === routeSubPath },
      ]"
      @click="clickFn({ type: 'router', sub_path: item.sub_path, id: item.id, index })">
      <Text class="text" :text="item.text" />
      <Button
        v-if="item.id"
        class="close-btn icon-style no-border"
        icon="close"
        :title="$t('Util.close_index')"
        @click.stop="clickFn({ type: 'close', sub_path: item.sub_path, id: item.id, index })" />
    </div>
  </div>
</template>

<script setup lang="ts">
const storeIndex = indexStore();
const { routePath, routeSubPath, routeSideMenu, routeParamsId } = storeToRefs(storeIndex);
const {} = storeIndex;
interface Props {
  /** 類型 */
  type?: string;
  /** 資料 */
  data?: TabDataItem[];
  /** 函式 */
  clickFn?: (item: TabClickFn) => void;
}
const props = withDefaults(defineProps<Props>(), {
  type: '',
  data: () => [],
  clickFn: () => {},
});

// const emit = defineEmits<{
//   (e: 'clickFn', item: ClickItem): void;
// }>();
// async function clickFn(item: ClickItem) {
//   console.log('已觸發點擊', item);
//   emit('clickFn', item);
// }

// watch(
//   () => props.data,
//   () => {
//     console.log('Tab data', props.data);
//   }
// );
</script>

<style lang="scss" scoped>
.tab-box {
  position: relative;
  flex: 0 0 36px;
  display: flex;
  border-bottom: var(--border-1);
  > .item {
    position: relative;
    top: 1px;
    flex: 0 0 140px;
    display: flex;
    align-items: center;
    margin-top: 3px;
    padding: 0 12px 1px 12px;
    border: var(--border-1);
    border-radius: var(--border-radius-2) var(--border-radius-2) 0 0;
    background-color: var(--surface);
    /* transition: var(--transition-fast); */
    &:first-child {
      margin-left: 4px;
    }
    &:not(:last-child) {
      margin-right: 4px;
    }
    &.active {
      pointer-events: none;
      border-bottom: 1px solid var(--white);
      background-color: var(--white);
    }
    > .text {
      user-select: none;
    }
    > .close-btn {
      pointer-events: auto;
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 0px;
      z-index: 1;
      :deep(span) {
        font-size: 16px;
      }
    }
  }
}
</style>
