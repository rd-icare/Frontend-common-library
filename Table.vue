<template>
  <div
    class="table-box gicons"
    :class="[
      storeParams.tableClass,
      {
        disabled: !data?.length,
      },
    ]">
    <div
      ref="mainRef"
      :class="[
        'table-main',
        {
          'thead-sticky-style': isTheadSticky,
          'height-auto': isHeightAuto,
          'editable-style': isEditable,
          'no-page-style': hidePageBox,
          'no-data-style': !data?.length,
        },
      ]"
      :style="{
        minHeight: minHeight ? minHeight + 'px' : '',
      }">
      <div class="thead" :class="{ 'show-scroll': showScroll }">
        <div class="tr">
          <slot name="thead"></slot>
        </div>
      </div>
      <div class="tbody-box">
        <div
          v-show="data?.length"
          ref="tbodyRef"
          class="tbody fade-loading"
          :class="[
            {
              loading: storeParams.loading,
              'show-scroll': showScroll,
            },
          ]">
          <TransitionGroup :name="isTransition" @after-enter="afterEnter" @after-leave="afterLeave">
            <!-- key 值 item => 解決表單元素不錯亂 index => 解決換頁滾動條不閃動 -->
            <div v-for="(item, index) in data" :key="isEditable ? item : index" class="tr">
              <slot
                name="tbody"
                :="{ item, index, sn: index + 1 + (storeParams.currentPage - 1) * storeParams.perPage }"></slot>
            </div>
          </TransitionGroup>
        </div>
        <NoData
          v-show="!data?.length"
          class="fade-loading"
          :class="{ loading: storeParams.loading }"
          :imgSrc="noDataImgSrc"
          :text="noDataText" />
        <CurrentLoading :show="storeParams.loading" colorCode="transparent" />
      </div>
    </div>
    <!-- 頁碼 -->
    <div v-if="!hidePageBox" class="page-box font-small-3">
      <div class="total-count">共 {{ storeParams.totalCount }} 筆</div>
      <div
        v-if="!hideControl && storeParams.totalPage > 1"
        class="control fade-loading"
        :class="{ loading: controlLoading }">
        <div v-if="!hidePageNum" class="icons hide-arrow-style">
          <div class="box" :class="{ disabled: storeParams.currentPage < 4 }" @click="clickFn('first')">
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
            :class="{ disabled: storeParams.currentPage > storeParams.totalPage - 3 || storeParams.totalPage === 1 }"
            @click="clickFn('last')">
            <span>last_page</span>
          </div>
        </div>
        <div v-if="!hidePageSelectNum" class="select">
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
          </div>
          <div class="text">/ {{ storeParams.totalPage }} 頁</div>
        </div>
        <div v-if="!hideShowNum" class="input-box">
          <Select
            :item="{
              type: 'select',
              id: `${storeParams.tableClass}-${idSubStr}-show_num`,
              name: `${storeParams.tableClass}_show_num`,
              value: storeParams.perPage,
              controlled: false,
            }"
            :option="[1, 2, 20, 40, 60, 80, 100].map((item) => ({ label: String(item), value: item }))"
            :fn />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts" generic="T">
interface Props {
  /** tableClass */
  tableClass?: string;
  /** ID 子字串 */
  idSubStr?: string;
  /** 顯示滾動條 */
  showScroll?: boolean;
  /** 是否為表頭位置黏住  */
  isTheadSticky?: boolean;
  /** 是否高度自動 for thead-sticky-style */
  isHeightAuto?: boolean;
  /** 是否為可編輯模式 */
  isEditable?: boolean;
  /** body-box 滾動動畫 */
  isTransition?: string;
  /** 請求資料 */
  getDatas?: () => Promise<any>;
  /** 無資料圖片 */
  noDataImgSrc?: string;
  /** 無資料文字 */
  noDataText?: string;
  /** 隱藏頁碼區塊 */
  hidePageBox?: boolean;
  /** 隱藏頁碼控制 */
  hideControl?: boolean;
  /** 隱藏頁碼數 */
  hidePageNum?: boolean;
  /** 隱藏頁碼數選項 */
  hidePageSelectNum?: boolean;
  /** 隱藏每頁顯示筆數選項 */
  hideShowNum?: boolean;
  /** .table-main 最小高度 */
  minHeight?: number;
}

const props = withDefaults(defineProps<Props>(), {
  tableClass: '',
  idSubStr: 'one',
  showScroll: true,
  isTheadSticky: true,
  isHeightAuto: false,
  isEditable: false,
  isTransition: '',
  getDatas: async () => {},
  hidePageBox: false,
  hideControl: false,
  hidePageNum: false,
  hidePageSelectNum: false,
  hideShowNum: false,
});

const emit = defineEmits(['afterEnter', 'afterLeave']);

/** 狀態管理的參數 */
const storeParams = defineModel<StoreParams>('storeParams', { type: Object, default: () => ({}) });

/** 可編輯的資料 */
const editable = defineModel<T[]>('editable', { type: Array, default: () => [] });

/** 資料 */
const data = computed(() => storeParams.value.data || editable.value);

/** 模板引用 */
const scrollRef = useTemplateRef<HTMLElement>(props.isTheadSticky ? 'mainRef' : 'tbodyRef');

/** 頁碼控制載入 */
const controlLoading = ref<boolean>(false);

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
    .map((item, index) => ({
      label: String(index + 1),
      value: index + 1,
    }))
);

/** 表單輸入事件 */
const fn = ref<FormFnType>({
  change: (e, value, item) => {
    console.log('change', value, item);

    // 前往頁數
    if (item?.name === `${storeParams.value.tableClass}_page_num`) {
      // storeParams.value.currentPage = Number(value);
      /* 當你確定 value 是字串（例如從 <input> 或 select 取到的值），但希望它變成數字時，用 +value 是最簡潔的寫法，同等於 Number(value) */
      storeParams.value.currentPage = +value;
      nextTick(async () => {
        await props.getDatas();
        setScroll();
      });
    }

    // 切換顯示筆數
    if (item?.name === `${storeParams.value.tableClass}_show_num`) {
      // controlLoading.value = true;
      storeParams.value.perPage = +value;
      // 當前總頁數重新計算
      storeParams.value.totalPage = Math.ceil(storeParams.value.totalCount / storeParams.value.perPage);
      // 當前頁數重新計算
      if (storeParams.value.currentPage > storeParams.value.totalPage) {
        storeParams.value.currentPage = storeParams.value.totalPage;
      }

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
  if (scrollRef) {
    scrollRef.value?.scrollTo({
      top: 0,
      // behavior: 'smooth',
    });
  }
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

    controlLoading.value = false;
  },
  { deep: true }
);

watch(
  () => storeParams.value.perPage,
  async (newValue, oldValue) => {
    controlLoading.value = true;
  }
);

onMounted(async () => {
  storeParams.value.scrollRef = scrollRef.value;
});
</script>

<style lang="scss" scoped></style>
