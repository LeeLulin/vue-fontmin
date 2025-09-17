<script setup lang="ts">
import { createFont } from 'fonteditor-core'
import type { UploadFileInfo } from 'naive-ui'

const emit = defineEmits(['font-loaded', 'reset', 'generate'])

const message = useMessage()

const uploadRef = useTemplateRef('uploadRef')

const inputValue = defineModel('text', { default: '' })

const fileList = ref<UploadFileInfo[]>([])

const loading = ref(false)

const beforeUpload = (data: { file: UploadFileInfo; fileList: UploadFileInfo[] }) => {
  const { file } = data
  const fileType = file.name.split('.').pop()?.toLowerCase()
  if (fileType !== 'ttf') {
    message.error('只能上传ttf文件')
    return false
  }
  return true
}

const onFileChange = (options: {
  file: UploadFileInfo
  fileList: UploadFileInfo[]
  event?: Event
}) => {
  const { file } = options
  fileList.value = [file]
  loadFont(file)
}

const loadFont = async (file: UploadFileInfo) => {
  if (!file.file) {
    message.error('文件无效')
    return
  }

  const arrayBuffer = await file.file.arrayBuffer()
  const font = new FontFace(file.name.split('.')[0], arrayBuffer)
  font.load().then((loadFont) => {
    document.fonts.add(loadFont)
    emit('font-loaded', loadFont.family)
  })
}

const reset = () => {
  uploadRef.value?.clear()
  fileList.value = []
  inputValue.value = ''
  emit('reset')
}

const getTextUnicode = (): number[] => {
  const unicodeArray = new Set<number>()
  for (let i = 0; i < inputValue.value.length; i++) {
    unicodeArray.add(inputValue.value.charCodeAt(i))
  }
  return Array.from(unicodeArray) as number[]
}

const generate = async () => {
  loading.value = true

  const [file] = fileList.value
  if (!file.file) {
    message.error('文件无效')
    return
  }

  const arrayBuffer = await file.file.arrayBuffer()
  const font = createFont(arrayBuffer, {
    type: 'ttf',
    subset: getTextUnicode(),
  })
  const buffer = font.write({
    type: 'ttf',
  })

  loading.value = false
  emit('generate', { arrayBuffer: buffer, name: file.name })
}
</script>

<template>
  <div class="text-input">
    <n-input
      class="text-input-wrap"
      v-model:value="inputValue"
      placeholder="输入文本进行字体子集化，输出字体只包含所选字型"
      type="textarea"
      :disabled="loading"
      clearable
      :resizable="false"
    />
    <div class="font-upload">
      <n-upload
        ref="uploadRef"
        class="font-upload-wrap"
        :multiple="false"
        directory-dnd
        :max="1"
        :show-file-list="false"
        :before-upload="beforeUpload"
        :on-change="onFileChange"
      >
        <n-upload-dragger v-if="fileList.length === 0">
          <n-text style="font-size: 16px">点击或者拖动文件到该区域来上传(仅支持.ttf格式)</n-text>
        </n-upload-dragger>
        <div v-else class="font-upload-file">
          <span>{{ fileList[0].name }}</span>
        </div>
      </n-upload>
      <n-button class="button" :disabled="fileList.length === 0" :loading="loading" @click="reset"
        >重置</n-button
      >
      <n-button
        class="button"
        type="primary"
        :disabled="!inputValue || fileList.length === 0"
        :loading="loading"
        @click="generate"
        >生成</n-button
      >
    </div>
  </div>
</template>

<style scoped lang="scss">
.text-input {
  flex: 1;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  gap: 10px;

  &-wrap {
    flex: 1;
    font-size: 20px;
  }

  .font-upload {
    width: 100%;
    display: flex;
    flex-direction: row;
    align-items: center;
    height: 80px;
    gap: 10px;

    &-wrap {
      flex: 1;
      height: 80px;
    }

    &-file {
      width: 100%;
      height: 80px;
      background: #fafafc;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      border: 1px solid #eee;

      span {
        font-size: 16px;
        color: #000;
      }
    }

    .button {
      height: 100%;
    }
  }
}
</style>
