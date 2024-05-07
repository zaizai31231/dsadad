<script setup lang="ts">
import { onMounted, ref, toRaw } from 'vue'

import * as pdfjsLib from 'pdfjs-dist/build/pdf.mjs'

// pdfjsLib.GlobalWorkerOptions.workerSrc =
//   "https://cdn.jsdelivr.net/npm/pdfjs-dist@4.2.67/build/pdf.worker.min.mjs";

pdfjsLib.GlobalWorkerOptions.workerSrc = new URL(
  'pdfjs-dist/build/pdf.worker.min.mjs',
  import.meta.url,
).toString()


defineProps<{ msg: string }>()
const pdfDocument = ref<any>(null);
const image = ref<any>();
const pdfCanvas = ref(null);

const loadData = async () => {




  const loadingTask = pdfjsLib.getDocument("https://ontheline.trincoll.edu/images/bookdown/sample-local-pdf.pdf");

  console.log(loadingTask);

  try {
    pdfDocument.value = await loadingTask.promise;
    getImageData();

    console.log(pdfDocument.value)
  } catch (error) {
    console.log(error);
  }
};

const getImageData = async () => {
  const page = await toRaw(pdfDocument.value).getPage(1);
  const viewport = page.getViewport({ scale: 1 });
  const canvas: any = pdfCanvas.value;

  canvas.height = viewport.height;
  canvas.width = viewport.width;

  canvas.style.height = `${viewport.width * window.devicePixelRatio}px`;
  canvas.style.width = `${viewport.width * window.devicePixelRatio}px`;
  const canvasContext = canvas.getContext("2d");
  const renderContext = {
    canvasContext,
    viewport,
  };
  await page.render(renderContext).promise;

  const imageDataURL = canvas.toDataURL();

  console.log(imageDataURL);

  image.value = new window.Image();
  image.value.src = imageDataURL;
};

onMounted(() => {
  loadData();

})


</script>

<template>
  <canvas ref="pdfCanvas"></canvas>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
