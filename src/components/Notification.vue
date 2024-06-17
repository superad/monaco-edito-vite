<template>
  <a-button type="primary" @click="openNotification">{{ button.name }}</a-button>
</template>
<script lang="ts" setup>
import { notification, Button } from 'ant-design-vue'
import { h } from 'vue'

const openNotification = () => {
  const key = `open${Date.now()}`
  notification.open({
    message: props.button.title,
    description: props.button.message,
    btn: () =>
      h(
        Button,
        {
          type: 'primary',
          size: 'small',
          onClick: () => notification.close(key)
        },
        { default: () => '确认' }
      ),
    key,
    onClose: props.button.onClose
  })
}
interface Button {
  name: string
  title: string
  message: string
  onClose: () => {}
}
const props = defineProps({
  button: {
    type: Button,
    required: false,
    default: () => {
      {
        name: '点击'
        title: '提示'
        message: '默认消息'
        onClose: () => {
          console.log('关闭提示框')
        }
      }
    }
  }
})
</script>
