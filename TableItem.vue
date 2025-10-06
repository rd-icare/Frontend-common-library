<template>
  <!-- <template
    v-if="divType === 'thead' && showThead"
    v-for="({ name, label, style, hide, showArrow, isSortArrow }, idx) in tableItem">
    <div :class="[`column-${idx + 1}`]" :style="style" :title="label">
      <div class="text">{{ label }}</div>
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
  <template
    v-else-if="divType === 'tbody'"
    v-for="(
      { label, name, type, stateType, stateText, stateTextArr, icon, hide, style, toDate = undefined, component }, idx
    ) in tableItem">
    <div
      v-if="type === undefined"
      :class="[{ icon: type === 'icon', state: component, hidden: hide }, `column-${idx + 1}`]"
      :style="style"
      :title="!toDate ? (!component ? item[name] : '') : item[name] ? dayjs(item[name]).format(toDate) : ''">
      <Component v-if="component" :is="component" :name :item :index />
      <template v-else-if="item[name]">
        {{ !toDate ? item[name] : item[name] ? dayjs(item[name]).format(toDate) : '' }}
      </template>
    </div>
    <div v-else-if="type === 'state'" :class="[`column-${idx + 1}`, { state: type === 'state' }]">
      <TextRoundBox
        v-if="
          stateType === 6 || item?.staff_type !== 'visitor' || !['train_statis', 'proof', 'body_status'].includes(name)
        "
        :stateType
        :value="name !== 'proof' ? item[name] : true"
        :stateText
        :stateTextArr />
      <TextRoundBox v-else :stateType="3" :value="false" :stateTextArr="['']" />
    </div>
    <div
      v-else-if="type === 'icon'"
      :class="[classObj({ type, icon })]"
      @click="
        !['picture_as_pdf'].includes(icon)
          ? click(name, item, index)
          : downloadFile(
              $event,
              getUrl(item[name]),
              `${item.class_staff_name ? `${item.class_staff_name}_${label}` : label}`
            )
      ">
      <Component v-if="component" :is="component" :name :item :index />
      <span v-else>{{ icon }}</span>
    </div>
  </template> -->
</template>

<script setup lang="ts"></script>

<style lang="scss" scoped></style>
