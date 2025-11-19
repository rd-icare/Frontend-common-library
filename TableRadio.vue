<template>
  <Table :isHeightAuto="true" :isEditable="true" :hidePageBox="true" v-model:editable="data">
    <template #thead>
      <TableItem divType="thead" :tableItem="tableItem" />
    </template>
    <template #tbody="{ item, index }">
      <TableItem divType="tbody" :tableItem="tableItem" :item :index />
    </template>
  </Table>
</template>

<script setup lang="ts">
import { Button, Text, Input, Select, Textarea } from '@/frontend-common-library/library';

interface Props {
  /** 標題 */
  questionText?: string;
  /** 題目資料 */
  questionData?: string[];
  /** 等級項目 */
  levels?: Record<string, any>[];
  /** VeeForm 模板引用 modal */
  veeformModalRef?: VeeFormExpose | null;
  /** 欄位名稱 */
  fieldName?: string;
  /** true 只讀 / false 編輯 */
  readonly?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  questionData: () => ['A', 'B', 'C'],
  levels: () => [
    { label: '輕', minWidth: 64 },
    { label: '中', minWidth: 64 },
    { label: '重', minWidth: 64 },
  ],
  fieldName: 'questions',
  readonly: false,
});

/** 表格資料 */
const data = ref<Models.Data[]>(props.questionData.map((item, index) => ({ id: index + 1, question_text: item })));

/** 總分 */
const totalScore = defineModel<number>('totalScore', {
  type: Number,
  default: 0,
});

/** 計算總分 */
const total = computed(() => {
  return data.value.reduce((total, current, index) => {
    const score = Number(props.veeformModalRef?.values?.[`${props.fieldName}`]?.[`question_${index + 1}`] ?? 0);
    return total + score;
  }, 0);
});

/** 表格項目 */
const tableItem = ref<TableItem[]>([
  {
    name: 'question_text',
    label: props.questionText ?? '標題',
    sortable: false,
  },
  ...props.levels
    .map((level, index) => ({ name: `level_${index}`, label: level.label, minWidth: level.minWidth }))
    .map<TableItem>((tItem, tIndex) => {
      return {
        name: tItem.name,
        label: tItem.label,
        minWidth: tItem.minWidth,
        style: {
          flex: 0,
          textAlign: 'center',
        },
        sortable: false,
        component: ({ name, item, index }: ComponentProps<Models.Data>, context) => {
          // console.log('item', name, item);

          /** 從欄位名稱取得分數字串，如 level_0 => 0 */
          const num = Number(String(name).split('_')[1]);

          return h(
            'div',
            {
              class: 'flex items-center justify-center absolute inset-0',
              onClick: () => {
                if (props.readonly) return;
                props.veeformModalRef?.setFieldValue?.(`${props.fieldName}.question_${index + 1}`, num);
              },
            },
            [
              h(Input, {
                class: 'pointer-events-none',
                item: {
                  type: 'radio',
                  name: `${props.fieldName}.question_${index + 1}`,
                  hideShape: true,
                  useRadioIcon: true,
                  trueValue: num,
                  directionMode: 'vertical',
                },
              }),
              h(Text, {
                class: 'pointer-events-none',
                text: String(num),
              }),
            ]
          );
        },
      };
    }),
]);

watchEffect(() => {
  totalScore.value = total.value;
});
</script>

<style lang="scss" scoped></style>
