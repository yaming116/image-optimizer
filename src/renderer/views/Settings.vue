<template>
  <div class="settings">
    <h2>设置</h2>
    <div class="settings__body">
      <AppSettingRow title="JPEG质量 (mozjpeg)">
        <AppInput
          v-model="jpegQuality"
          type="number"
          :valid="jpegQuality > 0"
        />
      </AppSettingRow>
      <AppSettingRow title="PNG质量范围 (pngquant)">
        <div class="flex">
          <AppInput
            v-model="pngQualityMin"
            type="number"
            :valid="validatePngQualityRange()"
          />
          -
          <AppInput
            v-model="pngQualityMax"
            type="number"
            :valid="validatePngQualityRange()"
          />
        </div>
      </AppSettingRow>
      <AppSettingRow title="转换 JPG / PNG to WebP">
        <AppToggle v-model="convertToWebp" />
      </AppSettingRow>
      <AppSettingRow title="压缩后文件名添加 '.mini'">
        <AppToggle v-model="addMinSuffix" />
      </AppSettingRow>
      <AppSettingRow title="压缩后文件输出在 'minified' 文件夹">
        <AppToggle v-model="addToSubfolder" />
      </AppSettingRow>
      <AppSettingRow title="添加新文件是否清空历史记录">
        <AppToggle v-model="clearResultList" />
      </AppSettingRow>
      <AppSettingRow title="压缩动画">
        <AppToggle v-model="animationOnCompletion" />
      </AppSettingRow>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useStore } from '@/store'
import { store as electronStore } from '@/electron'

const store = useStore()

const getQualityNumber = (min: number, max: number, value: number) => {
  let result = value <= min ? min : value
  result = result > max ? max : value
  return result
}

const validatePngQualityRange = () => pngQualityMin.value < pngQualityMax.value

const jpegQuality = computed({
  get: () => store.settings.mozjpeg.quality,
  set: v => {
    const value = getQualityNumber(0, 100, Number(v))
    store.settings.mozjpeg.quality = v
    console.log(value)
    electronStore.set('mozjpeg.quality', value)
  }
})
const pngQualityMin = computed({
  get: () => store.settings.pngquant.qualityMin,
  set: v => {
    const value = getQualityNumber(0, 99, Number(v))
    store.settings.pngquant.qualityMin = value
    if (validatePngQualityRange()) {
      electronStore.set('pngquant.qualityMin', value)
    }
  }
})
const pngQualityMax = computed({
  get: () => store.settings.pngquant.qualityMax,
  set: v => {
    const value = getQualityNumber(0, 100, Number(v))
    store.settings.pngquant.qualityMax = value
    if (validatePngQualityRange()) {
      electronStore.set('pngquant.qualityMax', value)
    }
  }
})
const convertToWebp = computed({
  get: () => store.settings.convertToWebp,
  set: v => {
    store.settings.convertToWebp = v
    electronStore.set('convertToWebp', v)
  }
})
const addMinSuffix = computed({
  get: () => store.settings.addMinSuffix,
  set: v => {
    store.settings.addMinSuffix = v
    electronStore.set('addMinSuffix', v)
  }
})
const addToSubfolder = computed({
  get: () => store.settings.addToSubfolder,
  set: v => {
    store.settings.addToSubfolder = v
    electronStore.set('addToSubfolder', v)
  }
})
const clearResultList = computed({
  get: () => store.settings.clearResultList,
  set: v => {
    store.settings.clearResultList = v
    electronStore.set('clearResultList', v)
  }
})
const animationOnCompletion = computed({
  get: () => store.settings.animationOnCompletion,
  set: v => {
    store.settings.animationOnCompletion = v
    electronStore.set('animationOnCompletion', v)
  }
})
</script>

<style lang="scss" scoped>
.flex {
  display: flex;
  align-items: center;
  gap: 10px;
}
</style>
