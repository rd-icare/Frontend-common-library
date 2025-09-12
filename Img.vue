<template>
  <img ref="imgRef" :src="getUrlFn()" :alt="alt" @error="imgErrorFn" @load="imgLoadFn" />
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue';
import { fileToBase64 } from '@/utils/common';
import { loadFile } from '@/composables/toPDFView';

// 定義 props 型別
type SrcType = string | File | null | undefined; // 你原本有 Array，但程式沒有處理 Array，這裡我暫時拿掉
interface Props {
  src: SrcType;
  alt?: string;
  errorImg?: string;
}

const props = withDefaults(defineProps<Props>(), {
  src: '',
  alt: '',
  errorImg: '/img/errorImg.svg',
});

// 定義 emit 型別
const emit = defineEmits<{
  (e: 'imgLoad', event: Event): void;
}>();

// ref 型別：指向 <img>
const imgRef = ref<HTMLImageElement | null>(null);

// 取得圖片 URL
const getUrlFn = (): string => {
  if (props.src instanceof File) {
    nextTick(() => {
      if (!imgRef.value) return;
      const srcStr = imgRef.value.src;
      if (!srcStr.includes('errorImg')) {
        if (props.src instanceof File && props.src.type === 'application/pdf') {
          imgRef.value.src = '/img/space.gif';
        }
        fileToBase64({ node: imgRef.value, value: props.src });
      }
    });
    return ''; // 預設先回空字串，因為 File 要 async 處理
  } else if (typeof props.src === 'string' && props.src.includes('.pdf')) {
    nextTick(() => {
      if (!imgRef.value) return;
      imgRef.value.src = '/img/space.gif';
      loadFile(props.src).then((res) => {
        if (imgRef.value) {
          imgRef.value.parentElement?.classList.remove('error');
          imgRef.value.src = res ?? '';
        }
      });
    });
    return ''; // 預設先回空字串
  }

  // 預設回傳字串資源路徑
  if (typeof props.src === 'string') {
    return new URL(props.src, import.meta.url).href;
  }
  return '';
};

// 錯誤事件
const imgErrorFn = (event: Event) => {
  const target = event.target as HTMLImageElement;
  target.parentElement?.classList.add('error');
  target.src = new URL(props.errorImg, import.meta.url).href;
};

// 載入完成事件
const imgLoadFn = (event: Event) => {
  emit('imgLoad', event);
};
</script>

<style lang="scss" scoped>
img {
  object-fit: contain;
  object-position: center;
  width: 100%;
  height: 100%;
}
</style>
