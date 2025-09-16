<script setup lang="ts">
import { Codemirror } from 'vue-codemirror'
import { css } from '@codemirror/lang-css'

const props = defineProps<{ text: string; fontFamily: string; base64: string }>()

const emit = defineEmits(['download'])

const message = useMessage()

const defaultContent = [
  '沁园春·雪',
  '北国风光，千里冰封，万里雪飘。',
  '望长城内外，惟余莽莽；',
  '大河上下，顿失滔滔。',
  '山舞银蛇，原驰蜡象，欲与天公试比高。',
  '须晴日，看红装素裹，分外妖娆。',
  '江山如此多娇，引无数英雄竞折腰。',
  '惜秦皇汉武，略输文采；唐宗宋祖，稍逊风骚。',
  '一代天骄，成吉思汗，只识弯弓射大雕。',
  '俱往矣，数风流人物，还看今朝。',
]

const preview = computed(() => props.text || defaultContent.join('<br>'))

const styles = computed(() => {
  return {
    fontFamily: props.fontFamily,
  }
})

const code = computed(() => {
  if (props.base64) {
    return `@font-face {\r  font-family: ${props.fontFamily};\r  src: url('data:application/x-font-woff2;charset=utf-8;base64,${props.base64}');\r}`
  }
  return ''
})

const copyCode = () => {
  navigator.clipboard
    .writeText(code.value)
    .then(() => {
      message.success('复制成功')
    })
    .catch(() => {
      message.error('复制失败')
    })
}

const downloadFont = () => {
  emit('download')
}
</script>

<template>
  <div class="font-preview">
    <div class="font-preview-wrap" :style="[styles]" v-html="preview"></div>
    <div class="font-preview-toolbar">
      <h3>CSS样式</h3>
      <div class="font-preview-toolbar-button">
        <n-button type="primary" :disabled="!props.base64" @click="copyCode">复制代码</n-button>
        <n-button type="info" :disabled="!props.base64" @click="downloadFont"
          >下载字体文件</n-button
        >
      </div>
    </div>
    <div class="font-preview-css">
      <Codemirror style="height: 150px" v-model="code" disabled :extensions="[css()]" />
    </div>
  </div>
</template>

<style scoped lang="scss">
.font-preview {
  flex: 1;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  box-sizing: border-box;
  gap: 10px;

  &-wrap {
    border: 1px solid rgb(224, 224, 230);
    border-radius: 3px;
    font-size: 20px;
    padding: 20px;
    flex: 1;
  }

  &-toolbar {
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    gap: 10px;

    &-button {
      display: flex;
      flex-direction: row;
      gap: 10px;
    }
  }

  &-css {
    height: 150px;
  }
}
</style>
