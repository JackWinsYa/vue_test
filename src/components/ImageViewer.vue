<template>
   <div class="flex-1 bg-gray-800 rounded-lg border border-gray-700 p-4 m-4">
  <h2 class="text-xl font-semibold text-white mb-4">車牌照片</h2>
  <div class="image-container rounded-lg border border-gray-600">
    <img :src="currentImage" :alt="'車牌照片 ' + (currentImageIndex + 1)" class="w-full h-full object-contain">
    {{ currentImage }} <!-- 檢查 currentImage 的值 -->
  </div>
  <div class="flex gap-2 justify-center mt-4">
    <!-- 切換圖片按鈕 -->
    <button @click="prevImage" class="p-2 rounded bg-gray-600 hover:bg-gray-700 text-white">
      <i class="fas fa-chevron-left text-xl"></i>
    </button>
    <button @click="nextImage" class="p-2 rounded bg-gray-600 hover:bg-gray-700 text-white">
      <i class="fas fa-chevron-right text-xl"></i>
    </button>
  </div>
</div>
  </template>
  
  <script>
  import { ref, computed, onMounted } from 'vue';
  
  export default {
    setup() {
      const currentImageIndex = ref(0);
      const randomImages = ref([]);
  
   const loadRandomImages = () => {
  const images = import.meta.glob("/src/assets/test_images/*.jpg", { eager: true });
  console.log(images); // 檢查加載的圖片
  const allImages = Object.keys(images);
  console.log(allImages); // 檢查所有圖片的路徑

  if (allImages.length === 0) {
    console.error('No images found in src/assets/test_images');
    return;
  }

  // 隨機選擇三張圖片
  const shuffled = allImages.sort(() => 0.5 - Math.random());
  randomImages.value = shuffled.slice(0, 3);
  console.log(randomImages.value); // 檢查隨機選擇的圖片
};
  
      const currentImage = computed(() => randomImages.value[currentImageIndex.value]);
  
      const nextImage = () => {
        currentImageIndex.value = (currentImageIndex.value + 1) % randomImages.value.length;
      };
  
      const prevImage = () => {
        currentImageIndex.value = (currentImageIndex.value - 1 + randomImages.value.length) % randomImages.value.length;
      };
  
      onMounted(() => {
        loadRandomImages(); // 在组件挂载时加载图片
      });
  
      return {
        currentImageIndex,
        currentImage,
        nextImage,
        prevImage,
      };
    },
  };
  </script>
  
  <style>
  .image-container {
    position: relative;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%; /* 限制寬度 */
    height: 80vh; /* 調整高度，讓圖片稍微大一些 */
  }
  
  .image-container img {
    max-width: 100%;
    max-height: 100%;
    transition: transform 0.3s ease;
  }
  
  .image-container:hover img {
    transform: scale(1.8); /* 增加放大倍率 */
  }
  
  .image-container img {
    cursor: zoom-in;
  }
  
  .image-container:hover img {
    cursor: zoom-out;
  }
  </style>
  
