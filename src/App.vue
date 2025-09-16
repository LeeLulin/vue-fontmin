<script setup lang="ts">
import FontInput from '@/components/FontInput.vue'
import FontPreview from '@/components/FontPreview.vue'

const fontFamily = ref('')

const text = ref('')

const fontBase64 = ref('')

const fontArrayBuffer = ref<ArrayBuffer | null>()

const fileName = ref('')

const onFontLoaded = (font: string) => {
  fontFamily.value = font
}

const onReset = () => {
  fontFamily.value = ''
  fontBase64.value = ''
  fontArrayBuffer.value = null
  fileName.value = ''
}

const downloadFont = () => {
  const blob = new Blob([fontArrayBuffer.value!])
  const url = window.URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.style.display = 'none'
  link.href = url
  link.setAttribute('download', fileName.value)
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  window.URL.revokeObjectURL(url)
}

const arrayBufferToBase64 = (buffer: ArrayBuffer) => {
  let binary = ''
  const bytes = new Uint8Array(buffer)
  const len = bytes.byteLength
  for (let i = 0; i < len; i++) {
    binary += String.fromCharCode(bytes[i])
  }
  return btoa(binary)
}

const onGenerate = (data: { arrayBuffer: ArrayBuffer; name: string }) => {
  fontArrayBuffer.value = data.arrayBuffer
  fontBase64.value = arrayBufferToBase64(data.arrayBuffer)
  fileName.value = data.name
}
</script>

<template>
  <n-message-provider>
    <div class="main-container">
      <FontInput
        v-model:text="text"
        @font-loaded="onFontLoaded"
        @reset="onReset"
        @generate="onGenerate"
      />
      <FontPreview
        :text="text"
        :font-family="fontFamily"
        :base64="fontBase64"
        @download="downloadFont"
      />
    </div>
  </n-message-provider>
</template>

<style lang="scss" scoped>
.main-container {
  width: 100%;
  height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
  overflow: hidden;
  padding: 20px;
  gap: 20px;
}
</style>
