<template>
  <div class="container">
    <div class="editor-header">
      <div class="language-picker">
        <p>语言：</p>
        <a-space>
          <a-select
            ref="select"
            v-model:value="currentLanguage"
            style="width: 120px"
            @change="selectLanguage"
            :options="languagesOption"
          ></a-select>
        </a-space>
      </div>
      <div class="theme-picker">
        <p>主题：</p>
        <a-radio-group v-model:value="currentTheme" button-style="solid" @change="selectTheme">
          <a-radio-button v-for="theme in themesOption" :value="theme.value">
            {{ theme.label }}
          </a-radio-button>
        </a-radio-group>
      </div>
    </div>
    <div id="editor" ref="editor"></div>
  </div>
</template>

<script setup lang="ts" name="MonacoEditor">
import * as monaco from 'monaco-editor/esm/vs/editor/editor.api'
import { ref, onMounted } from 'vue'

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

const code = ref(`
// Type source code in your language here...
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}`)

function selectLanguage(value: string) {
  currentLanguage.value = value
  const models = monaco.editor.getModels()
  monaco.editor.setModelLanguage(models[0], currentLanguage.value)
}

function selectTheme(e: any) {
  currentTheme.value = e.target.value
  monaco.editor.setTheme(currentTheme.value)
}

onMounted(() => {
  monaco.editor.create(editor.value, {
    value: code.value, //初始代码
    theme: currentTheme.value, //主题 默认 vs, 可选值: 'vs','vs-dark','hc-black'
    language: currentLanguage.value, // 初始语言
    lineNumbers: 'on', //展示行数 默认 on
    readOnly: false, //只读    默认false
    autoIndent: 'advanced' // 自动缩进	默认'advanced', 可选值 'none'/'keep'/'brackets'/'advanced'/'full'
  })
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

#editor {
  width: 100%;
  height: 600px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>
