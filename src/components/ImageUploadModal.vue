<script setup>
import Button from '@/components/Button.vue'
import { ref } from 'vue'

  defineProps({
    isShowModal: {
      type: Boolean,
      required: true,
    }
  })

  const Images = ref([])

  const changeInput =(e) => {
    const files = e.target.files
    if (!files || files?.length <= 0) return

    Array.from(files).forEach(file => {
      Images.value.push({
        "url": URL.createObjectURL(file),
        "name": file.name,
        "size": file.size,
        "type": file.type
      })
      URL.revokeObjectURL(file) // 破棄
    })
  }

  const emit = defineEmits(['click-submit']);
  const onClickSubmit = () => {
    emit('click-submit', Images.value)
    Images.value = []
  } 
</script>

<template>
  <div v-if=isShowModal class="Dialog__wrapper" >
    <dialog
      class="Dialog"
      open
    >
      <h1 class="Dialog__title">アップロードする画像を選択してください</h1>
      <div class="Dialog__content">
        <p v-for="image in Images">{{ image.name }}</p>
        <label>
          <input
            type="file"
            name="file"
            multiple
            accept="image/png, image/jpeg, image/jpg"
            ref="images"
            @change="changeInput"
          />
          画像を選択
        </label>
        <div class="Dialog__content__action">
          <Button label="アップロード" @click="onClickSubmit()" :disabled="Images.length <= 0"/>
          <Button label="キャンセル" @click="$emit('click-cancel')"/>
        </div>
      </div>
    </dialog>
  </div>
</template>

<style scoped>
label {
  padding: 16px 24px;
  color: #fff;
  background-color: #384878;
  cursor: pointer;
}
input[type="file"] {
  display: none;
}

.Dialog__wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100dvw;
  height: 100dvh;
  background: #00000046;
  display: flex;
  align-items: center;
  justify-content: center;
}

.Dialog {
  position: fixed;
  bottom: 0;
  left: 0;
  width: auto;
  height: auto;
  max-height: calc(100% - 60px);
  padding: 30px;
  border: none;
  border-radius: 8px 8px 0 0;
}

.Dialog__title {
  font-size: 1.5rem;
  text-align: center;
}

.Dialog__content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 30px 0 0;
}

.Dialog__content__action {
  display: flex;
  padding: 30px 0 0;
  gap: 30px;
  justify-content: space-between;
}

</style>
