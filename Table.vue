<template>
  <div
    class="table-box gicons"
    :class="[
      storeParams.tableClass,
      {
        disabled: !storeParams.data?.length,
      },
    ]">
    <div class="thead-box" :class="{ 'show-scroll': showScroll }">
      <slot name="thead" :key="`thead-${storeParams.tableClass}`" class="item thead"></slot>
    </div>
    <div class="tbody-main">
      <div
        ref="scrollRef"
        class="tbody-box fade-loading"
        :class="[
          {
            loading: storeParams.loading,
            'show-scroll': showScroll,
          },
        ]">
        <TransitionGroup :name="isTransition" @after-enter="afterEnter" @after-leave="afterLeave">
          <slot
            name="tbody"
            v-for="(item, index) in storeParams.data"
            :key="item.id || item"
            class="item tbody"
            :="{ item, index }"></slot>
        </TransitionGroup>
      </div>
      <NoDataBox
        v-show="!storeParams.data?.length"
        class="fade-loading"
        :class="{ loading: storeParams.loading }"
        :imgSrc="noDataImgSrc"
        :text="noDataText" />
      <CurrentLoading :show="storeParams.loading" colorCode="transparent" />
    </div>
    <!-- 頁碼 -->
    <div v-if="!hidePageBox" class="page-box font-small-3">
      <div class="total-count">共 {{ storeParams.totalCount }} 筆</div>
      <div v-if="!hideControl" class="control">
        <div class="icons">
          <div class="box" :class="{ disabled: storeParams.currentPage === 1 }" @click="clickFn('first')">
            <span>first_page</span>
          </div>
          <div class="box" :class="{ disabled: storeParams.currentPage === 1 }" @click="clickFn('backward')">
            <span>chevron_backward</span>
          </div>
          <div
            v-for="page in visiblePages"
            :key="page"
            class="box page-number"
            :class="{ active: page === storeParams.currentPage }"
            @click="clickFn(page)">
            {{ page }}
          </div>
          <div
            class="box"
            :class="{ disabled: storeParams.currentPage === storeParams.totalPage || storeParams.totalPage === 0 }"
            @click="clickFn('forward')">
            <span>chevron_forward</span>
          </div>
          <div
            class="box"
            :class="{ disabled: storeParams.currentPage === storeParams.totalPage || storeParams.totalPage === 0 }"
            @click="clickFn('last')">
            <span>last_page</span>
          </div>
        </div>
        <div class="select">
          <div class="text">第</div>
          <div class="input-box">
            <Select
              :item="{
                type: 'select',
                id: `${storeParams.tableClass}-${idSubStr}-page_num`,
                name: `${storeParams.tableClass}_page_num`,
                value: storeParams.currentPage,
                controlled: false,
              }"
              :option="selectPageNum"
              :fn />
            <Select
              :item="{
                type: 'select',
                id: `${storeParams.tableClass}-${idSubStr}-show_num`,
                name: `${storeParams.tableClass}_show_num`,
                value: storeParams.perPage,
                controlled: false,
              }"
              :option="[20, 40, 60, 80, 100]"
              :fn />
          </div>
          <div class="text">/ {{ storeParams.totalPage }} 頁</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** tableClass */
  tableClass?: string;
  /** ID 子字串 */
  idSubStr?: string;
  /** 顯示滾動條 */
  showScroll?: boolean;
  /** body-box 滾動動畫 */
  isTransition?: string;
  /** 請求資料 */
  getDatas?: () => Promise<void>;
  /** 無資料圖片 */
  noDataImgSrc?: string;
  /** 無資料文字 */
  noDataText?: string;
  /** 隱藏頁碼 */
  hidePageBox?: boolean;
  /** 隱藏控制 */
  hideControl?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  tableClass: '',
  idSubStr: 'one',
  showScroll: false,
  isTransition: '',
  getDatas: async () => {},
  noDataImgSrc: '',
  noDataText: '',
  hidePageBox: false,
  hideControl: false,
});

const emit = defineEmits(['afterEnter', 'afterLeave']);
/** 狀態管理的參數 */
const storeParams = defineModel<ReturnType<storeParams>>('storeParams', { type: Object, default: () => ({}) });
/** 滾動條模板引用 */
const scrollRef = ref<HTMLElement | null>(null);
/** 顯示最多 5 個頁碼 */
const visiblePages = computed(() => {
  const total = storeParams.value.totalPage || 1;
  const current = storeParams.value.currentPage || 1;
  const range = 2; // 左右各顯示 2 個
  const start = Math.max(1, current - range);
  const end = Math.min(total, current + range);
  const pages = [];
  for (let i = start; i <= end; i++) pages.push(i);
  return pages;
});
/** 頁碼數量選項 */
const selectPageNum = computed(() =>
  Array(storeParams.value.totalPage)
    .fill('')
    .map((item, index) => index + 1)
);
/** 表單輸入事件 */
const fn = ref<FormFnType>({
  change: (e, value, item) => {
    console.log('change', value, item);
    // 前往頁數
    if (item?.name === `${storeParams.value.tableClass}_page_num`) {
      // storeParams.value.currentPage = Number(value);
      storeParams.value.currentPage = +value;
      nextTick(async () => {
        await props.getDatas();
        setScroll();
      });
    }
    // 切換顯示筆數
    if (item?.name === `${storeParams.value.tableClass}_show_num`) {
      storeParams.value.perPage = +value;
      // 當前頁數重新計算
      storeParams.value.currentPage = Math.ceil(storeParams.value.totalCount / storeParams.value.perPage);
      nextTick(async () => {
        await props.getDatas();
        setScroll();
      });
    }
  },
});
/** 頁碼控制點擊事件 */
const clickFn = (type: string | number) => {
  const sp = storeParams.value;
  if (!sp) return;
  // const current = sp.currentPage ?? 1;
  const current = sp.currentPage;
  const total = sp.totalPage;
  switch (type) {
    case 'first':
      sp.currentPage = 1;
      break;
    case 'backward':
      sp.currentPage = Math.max(1, current - 1);
      break;
    case 'forward':
      sp.currentPage = Math.min(total, current + 1);
      break;
    case 'last':
      sp.currentPage = total;
      break;
    default:
      // 點擊頁碼按鈕
      if (typeof type === 'number') sp.currentPage = type;
      break;
  }
  nextTick(async () => {
    await props.getDatas();
    setScroll();
  });
};
/** 設定滾動條至頂部 */
function setScroll() {
  // if (scrollRef.value) scrollRef.value.scrollTop = 0;
  if (scrollRef.value) scrollRef.value.scrollTo({ top: 0, behavior: 'smooth' });
}
function afterEnter() {
  emit('afterEnter');
}
function afterLeave() {
  emit('afterLeave');
}
/* 監聽列表資料 */
const stopWatch = watch(
  () => storeParams.value.data,
  async (newValue, oldValue) => {
    // console.log(`watch Table`)
    if (storeParams.value.isScrollToTop) {
      setScroll();
      storeParams.value.isScrollToTop = false;
    }
  },
  { deep: true }
);

onMounted(async () => {});
</script>

<style lang="scss" scoped></style>
