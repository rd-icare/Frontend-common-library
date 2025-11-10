<template>
  <Editor
    v-model="content"
    :init="initOptions"
    :disabled="readonly"
    :id="editorId"
    @onBlur="handleBlur"
    @onFocus="handleFocus" />
</template>

<!-- 使用方法 <RichTextEditor v-model="" language="zh_TW" :readonly /> -->

<script setup lang="ts">
import { ref, computed, watch, onBeforeUnmount } from 'vue';
import Editor from '@tinymce/tinymce-vue';
import tinymce from 'tinymce/tinymce';

// 主題與樣式
import 'tinymce/icons/default';
import 'tinymce/themes/silver/theme';
import 'tinymce/skins/ui/oxide/skin.min.css';

// plugins（按需引入）
import 'tinymce/plugins/advlist';
import 'tinymce/plugins/autolink';
import 'tinymce/plugins/lists';
import 'tinymce/plugins/link';
import 'tinymce/plugins/image';
import 'tinymce/plugins/charmap';
import 'tinymce/plugins/preview';
import 'tinymce/plugins/anchor';
import 'tinymce/plugins/searchreplace';
import 'tinymce/plugins/visualblocks';
import 'tinymce/plugins/code';
import 'tinymce/plugins/fullscreen';
import 'tinymce/plugins/insertdatetime';
import 'tinymce/plugins/media';
import 'tinymce/plugins/table';
import 'tinymce/plugins/help';
import 'tinymce/plugins/wordcount';

interface Props {
  modelValue: string;
  id?: string;
  readonly?: boolean;
  height?: number;
  toolbar?: string;
  language?: string;
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: '',
  // height: 400,
  language: 'zh_TW',
  toolbar:
    'undo redo | formatselect | bold italic underline | alignleft aligncenter alignright | bullist numlist outdent indent | link image code',
});

const emit = defineEmits<{
  'update:modelValue': [value: string];
  blur: [event: FocusEvent];
  focus: [event: FocusEvent];
}>();

const editorId = props.id || `tiny-${Math.random().toString(36).slice(2)}`;
const content = ref(props.modelValue);
let editorInstance: any = null;

const initOptions = computed(() => ({
  height: props.height,
  content_style: `
    body {
      font-family: 'Noto Sans TC', sans-serif;
      padding: 0 8px;
      color: #3c3c3c;
    }
    p {
      margin: 0 0 8px;
    }
  `,
  menubar: false,
  plugins: [
    'advlist autolink lists link image charmap preview anchor',
    'searchreplace visualblocks code fullscreen',
    'insertdatetime media table help wordcount',
  ],
  toolbar: props.toolbar,
  branding: false,
  skin: 'oxide',
  language: props.language,
  language_url: `/tinymce/langs/${props.language}.js`,
  setup(editor: any) {
    editorInstance = editor;
    editor.on('change keyup', () => {
      emit('update:modelValue', editor.getContent());
    });
    // 縮排
    // editor.on('keydown', (e: KeyboardEvent) => {
    //   if (e.key === 'Tab') {
    //     e.preventDefault();
    //     editor.execCommand('mceInsertContent', false, '&nbsp;&nbsp;&nbsp;&nbsp;');
    //   }
    // });
  },
}));

watch(
  () => props.modelValue,
  (val) => {
    if (val !== content.value) {
      content.value = val;
      editorInstance?.setContent(val || '');
    }
  }
);

const handleBlur = (e: FocusEvent) => emit('blur', e);
const handleFocus = (e: FocusEvent) => emit('focus', e);

onBeforeUnmount(() => {
  editorInstance?.remove();
  editorInstance = null;
});
</script>

<style lang="scss" scoped></style>
