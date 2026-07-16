<template>
  <div class="image-rotator w-full rounded-2xl overflow-hidden mb-6">
    <img
      v-if="currentImage"
      :src="currentImage"
      alt="rotating"
      class="rot-img w-full h-[220px] object-cover"
    />
    <div v-else class="w-full h-[220px] bg-gray-100 flex items-center justify-center text-gray-400">
      이미지 없음
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import raw from '../assets/data/local-data.json' // 필요시 경로 조정

// JSON에서 이미지 URL을 꺼내는 매핑: 항목 구조에 맞게 수정하세요.
const images = (() => {
  const arr = Array.isArray(raw) ? raw : (raw.items || [])
  return arr
    .map(item =>
      // 흔한 필드들 시도: image, img, 사진, src, file
      item.image || item.img || item.src || item.url || item.photo || item.사진
    )
    .filter(Boolean)
})()

const currentImage = ref(images.length ? images[0] : '')
let timer = null

function pickRandom() {
  if (!images.length) return
  let idx = Math.floor(Math.random() * images.length)
  if (images.length > 1 && images[idx] === currentImage.value) {
    idx = (idx + 1) % images.length
  }
  currentImage.value = images[idx]
}

onMounted(() => {
  if (!images.length) return
  // 초기 이미지와 타이머 설정
  pickRandom()
  timer = setInterval(pickRandom, 3000)
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<style scoped>
.rot-img { transition: opacity .4s ease; }
</style>