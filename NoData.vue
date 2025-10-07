<template>
  <div class="no-data">
    <div v-if="imgSrc" class="image">
      <img :src="imgSrc" />
    </div>
    <Text class="color-secondary-text" :text="text" />
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 圖片路徑 */
  imgSrc?: string;
  /** 文字 */
  text?: string;
}

withDefaults(defineProps<Props>(), {
  text: '無資料',
});
</script>

<style lang="scss" scoped>
.no-data {
  width: 100%;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: var(--placeholder-text);
  > .image {
    width: 100px;
    height: 100px;
    display: flex;
    align-items: center;
    justify-content: center;
    img {
      width: 100%;
      height: auto;
    }
  }
  > .text {
    text-align: center;
  }
}
</style>
