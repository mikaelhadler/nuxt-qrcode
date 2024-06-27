<script setup>
import VueQrcode from "@chenfengyuan/vue-qrcode";
import { toPng } from "html-to-image";
import "~/public/main.css";

useHead({
  title: "QR Code Generator",
  meta: [
    {
      name: "description",
      content: "Generate QR code for everything you want to share"
    },
    {
      name: "keywords",
      content: "QR code, QR code generator, QR code for "
    }
  ]
});
useSeoMeta({
  title: "QR Code Generator",
  ogTitle: "QR Code Generator",
  ogDescription: "Generate QR code for everything",
  description: "Generate QR code for everything",
  keywords: "QR code, QR code generator, QR code for everything"
});

const link = ref("");
const qrCodeValue = ref("");
const downloading = ref(false);
const templateReference = ref(null);

const downloadQRCode = () => {
  downloading.value = true;

  toPng(templateReference.value)
    .then((dataUrl) => {
      const anchor = document.createElement("a");
      anchor.download = "my-qrcode.png";
      anchor.href = dataUrl;
      anchor.click();
      downloading.value = false;
    })
    .catch((err) => {
      console.log(err, templateReference.value);
      downloading.value = false;
    });
};

const keyPressHandler = (e) => {
  console.log(e.target.value.trim());
  if (e.target.value) {
    generateQrCode();
  }
};
const generateQrCode = () => {
  qrCodeValue.value = link.value;
};
</script>
<template>
  <div class="flex w-fit mx-auto min-h-full flex-col justify-center items-center px-6 py-12 lg:px-8">
    <h1 class="text-2xl font-bold mt-4">QR Code Generator</h1>
    <p class="text-gray-500 mt-2">Enter what you want to generate QR code</p>
    <input type="text" v-model="link" class="w-80 mt-4 p-2 border border-gray-300 rounded-md" spellcheck="false"
      @keyup.enter="keyPressHandler" />
    <div class="flex justify-center mt-4">
      <button target="_blank" rel="noopener"
        class="w-80 bg-yellow-400 rounded-lg text-gray-800 font-medium text-base md:text-lg py-3 px-8 md:px-12 hover:bg-yellow-500 transition-all duration-150 ease-in-out"
        :class="!link ? 'cursor-not-allowed' : 'cursor-pointer'" :disabled="!link" @click="generateQrCode">
        Generate QR Code
      </button>
    </div>
    <div class="p-8 w-80 border-gray-500 border  rounded-2xl hover:shadow-lg flex flex-col items-center mt-6"
      v-if="qrCodeValue">
      <div v-if="qrCodeValue" ref="templateReference">
        <VueQrcode :value="qrCodeValue" :options="{ width: 250, height: 250 }" />
      </div>
      <p class="mt-4 font-bold">Scan the QR code</p>
      <button target="_blank" rel="noopener"
        class="mt-4 w-64 bg-yellow-400 rounded-lg text-gray-800 font-medium text-base md:text-lg py-3 px-8 md:px-12 hover:bg-yellow-500 transition-all duration-150 ease-in-out"
        @click="downloadQRCode">
        Download
      </button>
    </div>
  </div>
</template>