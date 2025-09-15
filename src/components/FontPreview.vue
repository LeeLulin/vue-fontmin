<script setup lang="ts">
const props = defineProps<{ text: string; fontFamily: string }>()

const previewRef = ref()

const defaultContent = [
  '青玉案·元夕',
  '东风夜放花千树。',
  '更吹落、星如雨。',
  '宝马雕车香满路。',
  '凤箫声动，玉壶光转，一夜鱼龙舞。',
  '蛾儿雪柳黄金缕。',
  '笑语盈盈暗香去。',
  '众里寻他千百度。',
  '蓦然回首，那人却在，灯火阑珊处。',
]

const preview = computed(() => props.text || defaultContent.join('<br>'))

const styles = computed(() => {
  return {
    fontFamily: props.fontFamily,
  }
})
</script>

<template>
  <div ref="previewRef" class="font-preview" :style="[styles]" v-html="preview"></div>
</template>

<style scoped lang="scss">
.font-preview {
  flex: 1;
  height: 100%;
  padding: 20px;
  border: 1px solid rgb(224, 224, 230);
  border-radius: 3px;
  font-size: 20px;
}
</style>
