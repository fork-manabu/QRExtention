<script setup lang="ts">
import { onMounted, ref } from "vue";
import QRCode from "qrcode";

const chrome = window.chrome;
const currentUrl = ref<string>("");
const canvas = ref<HTMLCanvasElement>();
const errorText = ref<string>("");

const generateQRCode = () => {
  if (canvas.value) {
    QRCode.toCanvas(canvas.value, currentUrl.value, (error: any) => {
      if (error) {
        errorText.value = "Error generating QR code";
      }
    });
  }
};

onMounted(() => {
  chrome.tabs.query({ active: true, currentWindow: true }, (e: any) => {
    currentUrl.value = e[0].url;
    generateQRCode();
  });
});

const handleUpdateClick = () => {
  const url = document.getElementById("url") as HTMLInputElement;
  const size = document.getElementById("pass") as HTMLInputElement;
  currentUrl.value = `${currentUrl.value}?id=${url.value}&pass=${size.value}`;
  generateQRCode();
};
</script>

<template>
  <p>QR Code for Current URL:<br />{{ currentUrl }}</p>
  <canvas ref="canvas"></canvas>
  <p class="error">{{ errorText }}</p>
  <p>Option</p>
  <div class="input-area">
    <input type="text" id="url" placeholder="ID" />
    <input type="text" id="pass" placeholder="PASS" />
  </div>
  <button @click="handleUpdateClick" class="update-button">Update</button>
</template>

<style scoped>
.error {
  color: red;
  font-weight: bold;
  font-size: 1.5em;
  text-align: center;
}
.input-area {
  display: flex;
  gap: 10px;
}
.update-button {
  margin-block: 10px;
  width: 100%;
}
</style>
