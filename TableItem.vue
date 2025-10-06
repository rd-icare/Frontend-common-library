<template>
  <template v-if="divType === 'thead' && showThead">
    <div
      v-for="({ name, label, style, hide, showArrow }, idx) in tableItem"
      :key="idx"
      v-show="!hide"
      :class="[`th column-${idx + 1}`]"
      :style="style">
      <Text :title="label" :text="label" />
      <ArrowBox
        v-if="showArrow"
        :name
        :idx
        :activeIndex
        :click
        :isSortArrow="isSortArrow ?? idx === activeIndex"
        @click="activeIndex = idx" />
    </div>
  </template>
  <template v-else-if="divType === 'tbody'">
    <div
      v-for="({ name, label, style, hide, component }, idx) in tableItem"
      :key="idx"
      v-show="!hide"
      :class="[`td column-${idx + 1}`]"
      :style="style">
      <component v-if="component" :is="component" :name :item :index />
      <Text v-else :title="label" :text="label" />
    </div>
  </template>
</template>

<script setup lang="ts"></script>

<style lang="scss" scoped></style>
