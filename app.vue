<script setup>
import VueQrcode from "@chenfengyuan/vue-qrcode";
import { toPng } from "html-to-image";
import "~/public/main.css";

const link = ref("");
const qrCodeValue = ref("");
const isInvalid = ref(false);
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
const isValidUrl = urlString => {
  var urlPattern = new RegExp('^(https?:\\/\\/)?' +
    '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|' +
    '((\\d{1,3}\\.){3}\\d{1,3}))' +
    '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*' +
    '(\\?[;&a-z\\d%_.~+=-]*)?' +
    '(\\#[-a-z\\d_]*)?$', 'i');
  return !!urlPattern.test(urlString);
}
watch(link, (value) => {
  if (value && !isValidUrl(value)) {
    isInvalid.value = true;
  } else {
    isInvalid.value = false;
  }
});

</script>
<template>
  <div class="flex w-fit mx-auto min-h-full flex-col justify-center items-center px-6 py-12 lg:px-8">
    <h1 class="text-2xl font-bold mt-4">QR Code Generator</h1>
    <p class="text-gray-500 mt-2">Enter a link to generate QR code</p>
    <input type="text" v-model="link"
      :class="isInvalid ? 'border-red-500 text-red-500 border outline-red-500' : 'border-gray-300'"
      class="w-80 mt-4 p-2 border border-gray-300 rounded-md" placeholder="Enter a link to generate QR code"
      spellcheck="false" @keyup.enter="keyPressHandler" />
    <div class="flex w-full mt-1">
      <p class="text-red-500 text-xs" v-if="isInvalid">Invalid URL</p>
    </div>
    <div class="flex justify-center mt-4">
      <button target="_blank" rel="noopener"
        class="w-80 bg-yellow-400 rounded-lg text-gray-800 font-medium text-base md:text-lg py-3 px-8 md:px-12 hover:bg-yellow-500 transition-all duration-150 ease-in-out"
        :class="!link || isInvalid ? 'cursor-not-allowed' : 'cursor-pointer'" :disabled="!link || isInvalid"
        @click="generateQrCode">
        Generate QR Code
      </button>
    </div>
    <div class="p-8 w-80 border-gray-500 border  rounded-2xl hover:shadow-lg flex flex-col items-center mt-6"
      v-if="qrCodeValue">
      <div v-if="qrCodeValue" ref="templateReference">
        <VueQrcode :value="qrCodeValue" :options="{ width: 250, height: 250 }" />
      </div>
      <p class="mt-4 font-bold">Scan the QR code to visit the link</p>
      <button target="_blank" rel="noopener"
        class="mt-4 w-64 bg-yellow-400 rounded-lg text-gray-800 font-medium text-base md:text-lg py-3 px-8 md:px-12 hover:bg-yellow-500 transition-all duration-150 ease-in-out"
        @click="downloadQRCode">
        Download
      </button>
    </div>
  </div>
</template>