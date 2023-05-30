<script setup lang="ts">
import { onMounted, ref } from 'vue'
import * as tf from '@tensorflow/tfjs';
import * as fu from "./FileUpload"

const file = ref<File | null>();
const imageElement = ref<HTMLImageElement>();

const loadModel = async () => {
  return tf.loadGraphModel("/src/assets/Balls_model_js/model.json")
}

const start = async () => {
  const model = await loadModel()
  let prediction = model.predict(preprocess())
  console.log(Object.values(prediction))
}

const preprocess = () => {
  const imgTensor = tf.browser.fromPixels(imageElement.value!);
  const resizedImg = tf.cast(imgTensor, 'float32')
    .resizeBilinear([224, 224])
    .div(tf.scalar(255.0));
  const imgExpanded = tf.expandDims(resizedImg, 0);

  return imgExpanded
}

/*function onFileChanged($event: Event) {
            const target = $event.target as HTMLInputElement;
            if (target && target.files) {
                file.value = target.files[0];
            }
        }
*/
</script>

<template>
  <img ref="imageElement" src="../assets/Baseball.png" width="500"/>
  <button @click="start">test</button>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
