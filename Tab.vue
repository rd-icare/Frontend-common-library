<template>
  <div class="tab-box">
    <div
      v-for="(item, index) in data"
      :key="item.text"
      :class="[
        'item whitespace-nowrap',
        {
          active: item.id ? item.id == routeParamsId : item.subPath === routeSubPath,
          '!pr-24': item.id,
        },
      ]"
      :title="item.text"
      @click="
        clickFn({
          type: 'router',
          text: item.text,
          subPath: item.subPath,
          id: item.id,
          paginName: item.paginName,
          index,
        })
      ">
      <Text class="pointer-events-none" :text="item.text" />
      <Button
        v-if="item.id"
        class="close-btn icon-style no-border"
        icon="close"
        :title="$t('Util.close_index')"
        @click.stop="
          clickFn({
            type: 'close',
            text: item.text,
            subPath: item.subPath,
            id: item.id,
            paginName: item.paginName,
            index,
          })
        " />
    </div>
  </div>
</template>

<!-- v-if="Object.keys(data || {}).length" -->

<script setup lang="ts">
const storeIndex = indexStore();
const { routePath, routeSubPath, routeTypeSideMenu, routeParamsId } = storeToRefs(storeIndex);
const {} = storeIndex;
interface Props {
  /** 資料 */
  data?: TabDataItem[];
  /** 返回點擊事件 */
  clickFn?: (item: TabClickFn) => void;
}
const props = withDefaults(defineProps<Props>(), {
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
    /* cursor: pointer; */
    position: relative;
    top: 0px;
    flex: 0 0 140px;
    display: flex;
    align-items: center;
    margin-top: 0px;
    margin-bottom: -1px;
    padding: 0 12px 1px 12px;
    border: var(--border-1);
    border-radius: var(--border-radius-2) var(--border-radius-2) 0 0;
    background-color: var(--surface);
    /* transition: var(--transition-fast); */
    &:first-child {
      margin-left: 8px;
    }
    &:not(:last-child) {
      margin-right: 6px;
    }
    &.active {
      /* pointer-events: none; */
      cursor: default;
      font-weight: bold;
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
  &.small-style {
    /* flex: 0 0 32px; */
    > .item {
      flex: 0 0 100px;
      justify-content: center;
    }
  }
}
</style>
