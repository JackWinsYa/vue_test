<template>
  <div class="flex-1 bg-gray-800 rounded-lg border border-gray-700 p-4 m-4">
    <h2 class="text-xl text-gray-100 font-semibold mb-4">違規開單系統</h2>
    <div class="p-4">
      <div class="flex flex-col space-y-4">
        <!-- 時間 -->
        <div class="flex flex-col">
          <label class="text-md text-gray-400 mb-1">時間</label>
          <input
            type="text"
            :value="currentTime"
            readonly
            class="w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-gray-100 text-md"
          />
        </div>

        <!-- 舉發 ID -->
        <div class="flex flex-col">
          <label class="text-md text-gray-400 mb-1">舉發 ID</label>
          <input
            type="text"
            :value="formData.serialNumber"
            readonly
            class="w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-gray-100 text-md"
          />
        </div>

        <!-- 員工 ID -->
        <div class="flex flex-col">
          <label class="text-md text-gray-400 mb-1">員工 ID</label>
          <input
            type="text"
            :value="formData.employeeId"
            readonly
            class="w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-gray-100 text-md"
          />
        </div>

        <!-- 車牌號碼 -->
        <div class="flex flex-col">
          <label class="text-md text-gray-400 mb-1">車牌號碼</label>
          <input
            type="text"
            v-model="formData.licensePlate"
            placeholder="請輸入車牌號碼"
            class="w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-gray-100 text-md"
          />
        </div>

        <!-- 違規事實 -->
        <div class="flex flex-col">
          <label class="text-md text-gray-400 mb-1">違規事實</label>
          <textarea
            v-model="formData.violationFact"
            rows="2"
            placeholder="請輸入違規事實"
            class="w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-gray-100 text-md"
          ></textarea>
        </div>
      </div>

      <!-- 圖片與按鈕區域 -->
      <div class="mt-6 flex">
        <!-- 左半邊圖片 -->
        <div class="flex-1 flex justify-center items-center">
          <img
            src="/src/assets/images/Jufa_tongzhidan.jpg"
            alt="罰單通知單"
            class="w-full max-w-xs h-auto object-cover border border-gray-600 rounded-md"
          />
        </div>

        <!-- 右半邊按鈕 -->
        <div class="flex-1 flex flex-col justify-center space-y-4 pl-4">
          <button
            @click="showModal = true"
            class="w-full px-3 py-2 bg-yellow-600 hover:bg-yellow-700 text-white rounded-lg"
          >
            預覽罰單
          </button>
          <button
            @click="resetForm"
            class="w-full px-3 py-2 bg-gray-600 hover:bg-gray-700 text-white rounded-lg"
          >
            重置
          </button>
          <button
            @click="submitForm"
            class="w-full px-3 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg"
          >
            提交
          </button>
        </div>
      </div>
    </div>

    <!-- 預覽罰單的彈窗 -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-black bg-opacity-75 flex justify-center items-center"
    >
      <div class="relative bg-gray-800 p-4 rounded-lg">
        <button
          @click="showModal = false"
          class="absolute top-2 right-2 px-2 py-1 bg-red-600 hover:bg-red-700 text-white rounded"
        >
          關閉
        </button>
        <!-- Canvas for drawing image and text -->
        <canvas
          ref="previewCanvas"
          class="max-w-full max-h-[80vh] rounded-md"
        ></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, ref, onMounted, watch } from "vue";

export default {
  setup() {
    const formData = reactive({
      serialNumber: "12345678", // 固定舉發 ID
      employeeId: "EMP001", // 固定員工 ID
      licensePlate: "ABC-1234", // 示例車牌號碼
      violationFact: "超速行駛 30 公里", // 示例違規事實
    });

    const currentTime = ref("");
    const showModal = ref(false);
    const previewCanvas = ref(null);

    const updateTime = () => {
      const now = new Date();
      currentTime.value = now.toLocaleString();
    };

    onMounted(() => {
      updateTime();
      setInterval(updateTime, 1000);
    });

    const resetForm = () => {
      formData.licensePlate = "";
      formData.violationFact = "";
    };

    const submitForm = () => {
      console.log("提交表單數據:", { ...formData, currentTime: currentTime.value });
      alert(`提交成功！\n時間：${currentTime.value}\n舉發 ID：${formData.serialNumber}`);
    };

    watch(showModal, (newValue) => {
      if (newValue && previewCanvas.value) {
        const canvas = previewCanvas.value;
        const ctx = canvas.getContext("2d");
        const image = new Image();
        image.src = require("@/assets/images/Jufa_tongzhidan.jpg");
        image.onload = () => {
          canvas.width = 1000; // 確保畫布大小與需求一致
          canvas.height = 1400;
          ctx.drawImage(image, 0, 0, canvas.width, canvas.height);

          // 在駕駛人姓名欄位寫入舉發 ID
          ctx.font = "20px Arial";
          ctx.fillStyle = "black";
          ctx.fillText(`舉發 ID: ${formData.serialNumber}`, 150, 300);

          // 在車牌號碼欄位寫入車牌號碼
          ctx.fillText(`車牌號碼: ${formData.licensePlate}`, 150, 350);

          // 在違規事實欄位寫入違規事實
          ctx.fillText(`違規事實: ${formData.violationFact}`, 150, 400);
        };
      }
    });

    return {
      formData,
      currentTime,
      resetForm,
      submitForm,
      showModal,
      previewCanvas,
    };
  },
};
</script>

<style scoped>
.flex-1 img {
  max-height: 100%; /* 確保圖片不超出父容器 */
  max-width: 100%; /* 確保圖片適應左側寬度 */
}
</style>
