<template>
  <div id="app">
    <MonacoEditor
      :style="editorStyle"
      :enableLanguagePicker="true"
      :enableThemePicker="true"
      :sourceCode="sourceCode"
      language="java"
    />
  </div>
</template>

<script setup lang="ts" name="App">
import { ref } from 'vue'
import MonacoEditor from '@/components/MonacoEditor.vue'
const editorStyle = ref({
  height: '300px',
  width: '500px',
  boreder: '1px solid #ccc',
  borderRadius: '4px'
})

let sourceCode = ref(`// Type source code in your language here...
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}`)
</script>

<style lang="scss" scoped></style>
