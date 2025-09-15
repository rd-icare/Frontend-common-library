<template>
  <Transition name="out">
    <div v-if="show" class="current-loading" :style="{ position, backgroundColor: colorCode }">
      <div class="flex flex-col" style="align-items: center; text-align: center">
        <div class="lds-spinner">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
        <div v-if="text" class="text">{{ text }}</div>
      </div>
    </div>
  </Transition>
</template>

<script setup lang="ts">
interface Props {
  show?: boolean;
  position?: 'absolute' | 'fixed' | 'relative';
  colorCode?: string;
  text?: string | null;
}

withDefaults(defineProps<Props>(), {
  show: false,
  position: 'absolute',
  colorCode: 'rgba(255, 255, 255, 1)',
  text: null,
});
</script>

<style lang="scss" scoped>
.current-loading {
  pointer-events: auto;
  user-select: none;
  // position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: center;
}
.lds-spinner {
  position: relative;
  width: 80px;
  height: 80px;
  display: inline-block;
  // transform: scale(0.6);
  zoom: 0.6;
}
.lds-spinner div {
  transform-origin: 40px 40px;
  animation: lds-spinner 1.2s linear infinite;
}
.lds-spinner div:after {
  content: ' ';
  width: 6px;
  height: 18px;
  position: absolute;
  top: 3px;
  left: 37px;
  display: block;
  border-radius: 20%;
  background: #a8a7a7;
}
.lds-spinner div:nth-child(1) {
  transform: rotate(0deg);
  animation-delay: -1.1s;
}
.lds-spinner div:nth-child(2) {
  transform: rotate(30deg);
  animation-delay: -1s;
}
.lds-spinner div:nth-child(3) {
  transform: rotate(60deg);
  animation-delay: -0.9s;
}
.lds-spinner div:nth-child(4) {
  transform: rotate(90deg);
  animation-delay: -0.8s;
}
.lds-spinner div:nth-child(5) {
  transform: rotate(120deg);
  animation-delay: -0.7s;
}
.lds-spinner div:nth-child(6) {
  transform: rotate(150deg);
  animation-delay: -0.6s;
}
.lds-spinner div:nth-child(7) {
  transform: rotate(180deg);
  animation-delay: -0.5s;
}
.lds-spinner div:nth-child(8) {
  transform: rotate(210deg);
  animation-delay: -0.4s;
}
.lds-spinner div:nth-child(9) {
  transform: rotate(240deg);
  animation-delay: -0.3s;
}
.lds-spinner div:nth-child(10) {
  transform: rotate(270deg);
  animation-delay: -0.2s;
}
.lds-spinner div:nth-child(11) {
  transform: rotate(300deg);
  animation-delay: -0.1s;
}
.lds-spinner div:nth-child(12) {
  transform: rotate(330deg);
  animation-delay: 0s;
}
@keyframes lds-spinner {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
.text {
  display: flex;
  font-size: 22px;
  font-weight: 500;
  white-space: nowrap;
  color: var(--invert-text);
}
</style>
