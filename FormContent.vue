<template>
  <fieldset class="form-fieldset" :class="[fieldsetClass, { 'vertical-scroll': verticalScroll }]" :disabled="readonly">
    <slot name="top" />
    <div
      ref="inputBoxRef"
      class="input-box"
      v-bind="attrs"
      :class="{
        'horizontal-mode': directionMode === 'horizontal',
        'vertical-mode': directionMode === 'vertical',
        'adaptive-width': adaptiveWidth,
        'label-width': labelWidth,
      }">
      <template v-for="(item, index) in formContent" :key="item.name || index">
        <div v-if="item.breakLine" class="break-line"></div>
        <component
          v-if="componentMap[item.type as keyof typeof componentMap]"
          :is="componentMap[item.type as keyof typeof componentMap]"
          :="{
            item,
            option: optionContent[item.name],
            fn,
          }"
          :eventName="item.eventName" />

        <Input v-else :="{ item, fn }" :eventName="item.eventName" v-model:modelValue="item.modelValue" />
      </template>
      <slot />
    </div>
  </fieldset>
</template>

<script setup lang="ts">
import Select from './Select.vue';
import Textarea from './Textarea.vue';
import Input from './Input.vue';
import Label from './Label.vue';
import InputNone from './InputNone.vue';

// 阻止自動繼承屬性到 root (fieldset)
defineOptions({ inheritAttrs: false });

const attrs = useAttrs();

const storeIndex = indexStore();
const { routeSubPath, routeParamsId } = storeToRefs(storeIndex);

const props = withDefaults(defineProps<FormContentProps>(), {
  formContent: () => [],
  optionContent: () => ({}),
  adaptiveWidth: true,
  inputBoxKey: '',
  verticalScroll: false,
});

const inputBoxRef = ref<HTMLElement | null>(null);

/**
 * type → 元件對應表
 */
const componentMap: Record<string, any> = {
  select: Select,
  textarea: Textarea,
  label: Label,
  none: InputNone,
};

/** 取得 key */
function getKey() {
  return `input-box-scroll-${routeParamsId.value}-${routeSubPath.value}-${props.inputBoxKey}`;
}

onMounted(async () => {
  if (props.verticalScroll) {
    getScrollPosition({ getKey, nodeRef: inputBoxRef });
  }
});

onBeforeRouteUpdate(async (to, from, next) => {
  if (props.verticalScroll) {
    setScrollPosition({ getKey, nodeRef: inputBoxRef });
    delScrollPosition({ getKey, from, to });
  }
  next();
});

onBeforeRouteLeave(async (to, from, next) => {
  if (props.verticalScroll) {
    setScrollPosition({ getKey, nodeRef: inputBoxRef });
    delScrollPosition({ getKey, from, to });
  }
  next();
});
</script>

<style lang="scss" scoped>
.input-box {
  &.label-width {
    :deep(> div) {
      label:not(.icon-box) {
        min-width: calc(v-bind(labelWidth) * 1px);
        justify-content: flex-end;
      }
    }
  }
}
fieldset {
  &.vertical-scroll {
    flex-grow: 1;
    > .input-box {
      overflow-y: auto;
      height: 0px;
      flex-grow: 1;
      flex-wrap: nowrap;
      flex-direction: column;
    }
  }
  &.file-fill-style {
    flex-grow: 1;
    gap: 8px;
    > .input-box {
      flex-grow: 1;
      flex-direction: column;
      > :deep(.file) {
        > .element {
          height: 100%;
          > .file-box {
            flex-grow: 1;
          }
        }
      }
    }
  }
}
</style>
