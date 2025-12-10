<template>
  <div class="tab-box">
    <div class="tab-box-top" :class="[topClass, { 'z-1': useBackground }]">
      <div class="items" :class="{ 'bg-white': useBackground }">
        <TransitionGroup name="tab">
          <div
            v-for="(item, index) in data"
            :key="item.text"
            :class="[
              'item whitespace-nowrap',
              {
                active: item.id
                  ? item.id.toString().trim() == routeParamsId.toString().trim()
                  : item.subPath === routeSubPath ||
                    (useActiveIndex && activeIndex === index),
                '!pr-6': item.id,
              },
            ]"
            :title="item.text"
            @click="
              clickFn({
                type: 'tab',
                text: item.text,
                subPath: item.subPath,
                id: item.id,
                paginName: item.paginName,
                index,
              }),
                (activeIndex = index)
            "
          >
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
              "
            />
          </div>
        </TransitionGroup>
      </div>
    </div>
    <slot name="content" :activeIndex="activeIndex"></slot>
  </div>
</template>

<!-- v-if="Object.keys(data || {}).length" -->

<script setup lang="ts">
const storeIndex = indexStore();
const { isModalOpen, routeSubPath, routeParamsId } = storeToRefs(storeIndex);

interface Props {
  /** tabKey */
  tabKey?: string;
  /** 資料 */
  data?: TabDataItem[];
  /** 返回點擊事件 */
  clickFn?: (item: TabClickFn) => void;
  /** 是否使用激活索引 */
  useActiveIndex?: boolean;
  /** .items 是否使用背景色 */
  useBackground?: boolean;
  /** .top 頂部樣式 */
  topClass?: string;
}

const props = withDefaults(defineProps<Props>(), {
  tabKey: '',
  data: () => [],
  clickFn: () => {},
  useActiveIndex: false,
  useBackground: false,
});

/** 激活索引 */
const activeIndex = defineModel<number>('activeIndex', {
  type: Number,
  default: 0,
});

// const emit = defineEmits<{
//   (e: 'clickFn', item: ClickItem): void;
// }>();
// async function clickFn(item: ClickItem) {
//   console.log('已觸發點擊', item);
//   emit('clickFn', item);
// }

/** 取得 key */
function getKey() {
  return `tab-active-${routeParamsId.value}-${routeSubPath.value}-${props.tabKey}`;
}

watch(activeIndex, (val) => {
  // 會話存儲設定激活索引
  sessionStorage.setItem(getKey(), String(val));
});

onMounted(() => {
  // 會話存儲取得激活索引
  const saved = sessionStorage.getItem(getKey());
  if (saved !== null) activeIndex.value = Number(saved);

  if (isModalOpen.value) return;

  onBeforeRouteUpdate(async (to, from, next) => {
    // console.log('onBeforeRouteLeave', { from, to, tabKey: props.tabKey });

    delSessionStorage({ getKey, from, to });

    next();
  });

  onBeforeRouteLeave(async (to, from, next) => {
    // console.log('onBeforeRouteLeave', { from, to, tabKey: props.tabKey });

    delSessionStorage({ getKey, from, to });

    next();
  });
});
</script>

<style lang="scss" scoped>
.tab-box {
  position: relative;
  display: flex;
  flex-direction: column;
  > .tab-box-top {
    pointer-events: none;
    position: relative;
    flex: 0 0 36px;
    display: flex;
    border-bottom: var(--border-1);
    > .items {
      display: flex;
    }
    .item {
      /* cursor: pointer; */
      pointer-events: auto;
      position: relative;
      top: 0px;
      flex: 0 0 140px;
      display: flex;
      align-items: center;
      margin-top: 0px;
      margin-bottom: -1px;
      padding: 0 12px;
      border: var(--border-1);
      border-radius: var(--border-radius-2) var(--border-radius-2) 0 0;
      color: var(--placeholder-text);
      background-color: var(--surface);
      /* transition: var(--transition-fast); */
      &:first-child {
        margin-left: 12px;
      }
      &:not(:last-child) {
        margin-right: 6px;
      }
      &.active {
        /* pointer-events: none; */
        cursor: default;
        font-weight: var(--font-bold);
        border-bottom: 1px solid var(--white);
        color: var(--main-text);
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
    &:has(+ .tab-content) {
      .item {
        border-radius: var(--border-radius-1) var(--border-radius-1) 0 0;
        &:first-child {
          margin-left: 0px;
        }
      }
    }
  }
  &.small-style {
    > .tab-box-top {
      /* flex: 0 0 var(--box-height); */
      .item {
        flex: 0 0 100px;
        justify-content: center;
      }
    }
  }
  &.adaptive-style {
    > .tab-box-top {
      .item {
        flex: 0 0 auto;
        color: var(--placeholder-text);
        &.active {
          font-weight: normal;
          color: var(--main-text);
        }
        &:last-child {
          flex: auto;
        }
      }
    }
  }
}
</style>
