<template>
  <div class="container">
    <div class="editor-header">
      <div class="language-picker" :style="showLanguagePicker">
        <p>语言：</p>
        <a-space>
          <a-select
            ref="select"
            v-model:value="currentLanguage"
            style="width: 120px"
            :options="languagesOption"
          ></a-select>
        </a-space>
      </div>
      <div class="theme-picker" :style="showThemePicker">
        <p>主题：</p>
        <a-radio-group v-model:value="currentTheme" button-style="solid">
          <a-radio-button v-for="theme in themesOption" :value="theme.value">
            {{ theme.label }}
          </a-radio-button>
        </a-radio-group>
      </div>
      <div class="get-code-button">
        <Notification :button="button" />
      </div>
    </div>
    <div id="editor" ref="editor"></div>
  </div>
</template>

<script setup lang="ts" name="MonacoEditor">
import * as monaco from 'monaco-editor/esm/vs/editor/editor.api'
import { ref, onMounted, watch, reactive } from 'vue'
import Notification from './Notification.vue'

// @ts-ignore 注册自定义语法高亮
import customLangMonarch from '@/custom-lang-monarch'
monaco.languages.register({ id: 'custom' })
monaco.languages.setMonarchTokensProvider('custom', customLangMonarch)
//引入Javascript
import 'monaco-editor/esm/vs/basic-languages/javascript/javascript.contribution'
//引入Java
import 'monaco-editor/esm/vs/basic-languages/java/java.contribution'
// 引入 go
import 'monaco-editor/esm/vs/basic-languages/go/go.contribution'
// 引入 Python
import 'monaco-editor/esm/vs/basic-languages/python/python.contribution'
// 引入 C++
import 'monaco-editor/esm/vs/basic-languages/cpp/cpp.contribution'
// 引入 sql
import 'monaco-editor/esm/vs/basic-languages/sql/sql.contribution'
const editor = ref()
// 默认语言 JavaScript
const currentLanguage = ref('java')
const languagesOption = ref([
  { value: 'javascript', label: 'JavaScript' },
  { value: 'java', label: 'Java' },
  { value: 'go', label: 'Go' },
  { value: 'python', label: 'Python' },
  { value: 'c++', label: 'C++' },
  { value: 'sql', label: 'Sql' }
])
//默认主题 vs
const currentTheme = ref('vs')
const themesOption = ref([
  { value: 'vs', label: 'vs' },
  { value: 'vs-dark', label: 'vs-dark' },
  { value: 'hc-black', label: 'hc-black' }
])

//默认代码
const soureCode = ref(`// Type source code in your language here...`)
//语法和主题配置选择配置
const showLanguagePicker = ref({})
const showThemePicker = ref({})
//只读
const readOnly = ref(false)
const codeText = ref('')

const loadMonacoEditor = async () => {
  return new Promise((resolve) => {
    resolve(monaco)
  })
}

watch(currentLanguage, (newLanguage) => {
  if (editor.value) {
    const models = monaco.editor.getModels()
    monaco.editor.setModelLanguage(models[0], newLanguage)
  }
})

watch(currentTheme, (newTheme) => {
  if (editor.value) {
    monaco.editor.setTheme(newTheme)
  }
})

// 组件配置初始化
const initConfig = () => {
  soureCode.value = props.sourceCode
  currentTheme.value = props.theme
  currentLanguage.value = props.language
  readOnly.value = props.readOnly
  showLanguagePicker.value = props.enableLanguagePicker ? {} : { display: 'none' }
  showThemePicker.value = props.enableThemePicker ? {} : { display: 'none' }
}

onMounted(async () => {
  //初始化组件参数
  initConfig()
  await loadMonacoEditor()
  const editorInstance = monaco.editor.create(editor.value, {
    value: soureCode.value, //初始代码
    theme: currentTheme.value, //主题 默认 vs, 可选值: 'vs','vs-dark','hc-black'
    language: currentLanguage.value, // 初始语言
    lineNumbers: 'on', //展示行数 默认 on
    readOnly: readOnly.value, //只读    默认false
    autoIndent: 'advanced' // 自动缩进	默认'advanced', 可选值 'none'/'keep'/'brackets'/'advanced'/'full'
  })

  //监控代码变化
  editorInstance.onDidChangeModelContent(() => {
    const model = monaco.editor.getModels()[0]
    codeText.value = model.getValue()
    console.log('代码变化: \n', codeText.value)
  })
})

//组件参数
const props = defineProps({
  style: {
    type: Object,
    required: true,
    default: () => ({
      height: '300px',
      width: '100%',
      boreder: '1px solid #ccc',
      borderRadius: '4px'
    })
  },
  enableLanguagePicker: {
    type: Boolean,
    required: false,
    default: () => false
  },
  enableThemePicker: {
    type: Boolean,
    required: false,
    default: () => false
  },
  sourceCode: {
    type: String,
    required: false,
    default: () => `//Type source code in your language here...
    `
  },
  language: {
    type: String,
    required: false,
    default: () => 'java'
  },
  theme: {
    type: String,
    required: false,
    default: () => 'vs-dark'
  },
  readOnly: {
    type: Boolean,
    required: false,
    default: () => false
  }
})

//代码提示框按钮
let button = ref({
  name: '获取代码',
  title: '提示',
  message: props.sourceCode,
  onClose: () => {
    console.log('close code text button')
  }
})

watch(codeText, () => {
  button.value.message = codeText.value
})
</script>

<style scoped>
.container {
  margin: 0 auto;
  padding: 20px;
}

.editor-header {
  display: flex;
  margin-bottom: 10px;
}

.language-picker {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.language-picker p {
  margin: 0 10px 0 0;
}

.theme-picker {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  margin-left: 30px;
}

.theme-picker p {
  margin: 0 10px 0 0;
}
.get-code-button {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  margin-left: 30px;
}

#editor {
  width: 100%;
  height: 600px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>
