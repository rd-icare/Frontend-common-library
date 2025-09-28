<template>
  <div class="icon-eye gicons">
    <div class="icon" @click="open($event, false)" v-if="isShow">
      <span>visibility</span>
    </div>
    <div class="icon" @click="open($event, true)" v-else>
      <span>visibility_off</span>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  /** 是否用字型顯示 */
  useFont?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  useFont: false,
});

const isShow = ref(false);

const open = (event: MouseEvent, bool: boolean) => {
  isShow.value = bool;

  // 找到最近的 .password 容器，再找裡面的 input
  const target = event.target as HTMLElement;
  const passwordWrapper = target.closest('.password');
  const node = passwordWrapper?.querySelector('input') as HTMLInputElement | null;

  if (!node) return;

  if (!props.useFont) {
    // 切換 input type
    node.setAttribute('type', isShow.value ? 'text' : 'password');
  } else {
    // 切換字型顯示
    node.style.fontFamily = isShow.value ? 'inherit' : '';
  }
};
</script>

<style lang="scss" scoped>
.icon-eye {
  position: absolute;
  bottom: 0px;
  right: 0px;
  > .icon {
    cursor: pointer;
    width: 32px;
    height: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>
