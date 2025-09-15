<script setup lang="ts">
import FontInput from '@/components/FontInput.vue'
import FontPreview from '@/components/FontPreview.vue'

const fontFamily = ref('')

const text = ref('')

const onFontLoaded = (font: string) => {
  fontFamily.value = font
}

const onReset = () => {
  fontFamily.value = ''
}
</script>

<template>
  <n-message-provider>
    <div class="main-container">
      <FontInput v-model:text="text" @font-loaded="onFontLoaded" @reset="onReset" />
      <FontPreview :text="text" :font-family="fontFamily" />
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
