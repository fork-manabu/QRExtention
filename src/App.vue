<script setup lang="ts">
import { onMounted, ref, computed } from "vue";
import QRCode from "qrcode";
const chrome = window.chrome;

const currentUrl = ref<string>("");
const canvas = ref<HTMLCanvasElement>();
const errorText = ref<string>("");

function generateQRCode() {
  if (canvas.value) {
    QRCode.toCanvas(canvas.value, currentUrl.value, (error: any) => {
      if (error) {
        errorText.value = "Error generating QR code";
      }
    });
  }
}

onMounted(() => {
  chrome.tabs.query({ active: true, currentWindow: true }, (e: any) => {
    currentUrl.value = e[0].url;
    generateQRCode();
  });
});
</script>

<template>
  <h1>QR Code for Current URL:</h1>
  <p>{{ currentUrl }}</p>
  <canvas ref="canvas"></canvas>
  <p class="error">{{ errorText }}</p>

  <p>Option</p>
  <!-- <input type="text" id="url" placeholder="ID" />
  <input type="text" id="size" placeholder="PASS" /> -->
</template>

<style scoped>
.error {
  color: red;
  font-weight: bold;
  font-size: 1.5em;
  text-align: center;
}
</style>
