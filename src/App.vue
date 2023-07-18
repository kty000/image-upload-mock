<script setup>
import axios from 'axios'

import Button from './components/Button.vue'
import { onMounted, reactive, ref } from 'vue';
import ImageUploadModal from './components/ImageUploadModal.vue';

const isShowModal = ref(false)
const imageList = ref([])
const isLoading = ref(false)
const isDraggingMode = ref(false)
const draggingDropItem = ref(null)

onMounted(() => {
  fetchImageList([])
})

const dragstart = (e) => {
  if (!isDraggingMode.value) return
  e.dataTransfer.effectAllowed = 'move'
  e.target.style.opacity = 0.5
}

const dragenter = (item) => {
  if (!isDraggingMode.value) return
  draggingDropItem.value = item
}

const dragend = (item, e) => {
  if (!isDraggingMode.value) return

  const draggingItemList = JSON.parse(JSON.stringify(imageList.value)) // structuredCloneでうまく行かなかった
  console.log('draggingItemList 変更前', draggingItemList)

  const itemIdx = draggingItemList.findIndex((v) => v.id === item.id)
  draggingItemList.splice(itemIdx, 1);

  const draggingItemIdx = draggingItemList.findIndex((v) => v.id === draggingDropItem.value.id)
  draggingItemList.splice(draggingItemIdx, 0, item);

  draggingItemList.forEach((v , idx) => 
    v.order = idx + 1
  )

  imageList.value = draggingItemList
  e.target.style.opacity = 1
  draggingDropItem.value = null
}

const fetchImageList = async(list) => {
  isLoading.value = true
  try {
    // mockなので、現在のimageListのデータを返すようにする
    const { data } = await axios.get(`https://httpbin.org/get?list=${list}`)
    imageList.value = data.args.list
  } catch(e) {
    console.error('fetchImageList Error', e)
  }
  isLoading.value = false

}

const onClickCancelModal = () => isShowModal.value = false;

const onClickSubmitModal = async (value) => {
  if (!value) return

  // 本来apiでid自動採番してほしいがmockなのでid生成する
  // 本来apiでorderを生成してほしいがmockなので省略
  const param = value.map((v, idx) => ({
    ...v,
    ...{id: `${v.name}-${idx}`}
  }))
  isLoading.value = true
  try {
    await axios.post(`https://httpbin.org/post`, param)
    .then(() => {
      imageList.value = [...imageList.value, ...param] // mockなのでimageListを直接更新
      isShowModal.value = false
    })
    .catch((error) => console.error(error))
    isLoading.value = false
  } catch(e) {
    console.error('onClickSubmitModal Error', e)
    isLoading.value = false
    throw e
  }
}

const onClickDraggingSubmitButton = async () => {
  isLoading.value = true
  try {
    await axios.post(`https://httpbin.org/post`, imageList.value)
    .then(() => {
      isDraggingMode.value = false
    })
    .catch((error) => console.error(error))
    isLoading.value = false
  } catch(e) {
    console.error('onClickDraggingSubmitButton Error', e)
    isLoading.value = false
    throw e
  }
}

</script>

<template>
  <div v-if="isLoading" class="App__loading">
    <p>loading...</p>
  </div>
  <div class="App">
    <header class="App__header">
      <Button
        label="画像アップロード"
        type="file"
        @clickButton="isShowModal = true"
      />
    </header>
    <body>
      <div v-if="imageList.length > 0">
        <Button
          v-if="!isDraggingMode"
          label='順番を入れ替える'
          @click="isDraggingMode = true"
        />
        <div class="App__content">
          <img
            v-for="image in imageList"
            :src="image.url"
            :alt="image.name"
            draggable="true"
            @dragstart="dragstart"
            @dragenter="dragenter(image)"
            @dragend.stop.prevent="dragend(image, $event)"
            class="App__image"
          />
        </div>
        <Button
          v-if="isDraggingMode"
          label='保存'
          @click="onClickDraggingSubmitButton"
        />
      </div>
      <div v-else>
        画像がありません
      </div>
      <ImageUploadModal
        :isShowModal=isShowModal 
        @click-cancel="onClickCancelModal"
        @click-submit="value => onClickSubmitModal(value)"
      >
        アップロード
      </ImageUploadModal>
    </body>
  </div>
</template>

<style scoped>
body {
  width: 100%;
  height: calc(100vh - 60px);
  height: calc(100dvh - 60px);
}

.App {
  width: 100vw;
  width: 100dvw;
  padding: 0 16px;
}

.App__loading {
  position: fixed;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  background-color: #000000a0;
  z-index: 2;
}
.App__header {
  width: 100%;
  height: 60px;
  display: flex;
  align-items: center;
}

.App__content {
  padding: 16px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 10px;
  justify-items: center;
  align-items: center;
}

.App__image {
  height: 150px;
  width: 150px;
  object-fit: contain;
  background-color: #fff;
  border-radius: 10%;
}
</style>
