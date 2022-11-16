<script setup lang="ts">
import { ref, watch } from 'vue';
import * as qrCode from 'qrcode';
import JSZip from 'jszip';
import { saveAs } from 'file-saver';

const reference = ref('');
const result = ref('');

watch(reference, async (val) => {
  if (val === '') {
    result.value = '';
    return
  }
  result.value = await qrCode.toDataURL(val);
})

const commandToDownload = async () => {
  const zip = new JSZip();
  zip.file('test.txt', 'hello world\n');
  const img = zip.folder('image')
  if (!img) return;
  const coolBase64 = await qrCode.toDataURL('cool');
  const wowBase64 = await qrCode.toDataURL('wow');
  console.log(coolBase64);
  console.log(wowBase64);
  img.file('cool.jpg', coolBase64.replace('data:image/png;base64,', ''), {base64: true});
  img.file('wow.jpg', wowBase64.replace('data:image/png;base64,', ''), {base64: true});
  const content = await zip.generateAsync({ type: 'blob'})
  saveAs(content, 'all.zip');
}

</script>

<template>
  <button
    @click="commandToDownload"
  >test click</button>
  <input type="text" v-model="reference">
  <img
    v-if="result"
    :src="result" 
    alt="reference"
  >

</template>

<style scoped></style>
