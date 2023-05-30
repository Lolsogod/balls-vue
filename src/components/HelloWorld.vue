<script setup lang="ts">
import { ref } from 'vue'
import * as tf from '@tensorflow/tfjs';

const imageUrl = ref('');
const imageElement = ref<HTMLImageElement>();
const result = ref<string>('');
const confidence = ref<string>();
const color = ref<string>('');

const labels = ['Мяч для американского футбола', 'Бейсбольный мяч', 'Баскетбольный мяч', 'Бильярдный шар',
 'Шар для боулинга', 'Мяч для крикета', 'Футбольный мяч', 'Мяч для гольфа', 'Хокейная шайба',
 'Мяч для регби', 'Воланчик', 'Мяч для настольного тенниса', 'Тенисный мяч', 'Волейбольный мяч']

const loadModel = async () => {
  return tf.loadGraphModel("/src/assets/Balls_model_js/model.json")
}

const start = async () => {
  const model = await loadModel()
  let prediction = await model.predict(preprocess());
  let predArray =  extractData(prediction.toString())
  const conf= Math.max(...predArray)
  chooseColor(conf)
  const predicted = predArray.indexOf(conf);
  confidence.value = toPercent(conf)
  result.value = labels[predicted]
}

const chooseColor = async (conf: number) =>{
  if (conf > 0.75)
    color.value = '#34d94a'
  else if (conf > 0.5)
    color.value = '#d9d134'
  else
    color.value = '#d93434'
}

function extractData(prediction: string) {
  const startIndex = prediction.indexOf('[');
  const cleanStr = prediction.slice(startIndex).replace(/\[|\]|,/g, '');
  const substrings = cleanStr.split(' ');
  const floats = substrings.map(substring => parseFloat(substring));

  return floats;
}

const preprocess = () => {
  const imgTensor = tf.browser.fromPixels(imageElement.value!);
  const imgResized = tf.image.resizeBilinear(imgTensor, [224, 224]).div(tf.scalar(255.0))
  const imgExpanded = imgResized.expandDims();
  return imgExpanded
}
function handleFileUpload(event:any) {
  result.value = ""
  confidence.value = ""
  imageUrl.value = ""
  const file = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e: any) => {
    imageUrl.value = e.target.result;
  };
  reader.readAsDataURL(file);
  start()
}

function toPercent(probability: number) {
  const percent = (probability * 100).toFixed(2);
  return `Уверенность: ${percent}%`
}
</script>

<template>
  <h1>{{ result }}</h1>
  <h3 :style="{'color': color}">{{ confidence }}</h3>
  <img ref="imageElement" :src="imageUrl" height="500" v-if="imageUrl"/>
  <br>
  <label for="upload" class="custom-file-upload">
    Загрузить картинку
  </label>
  <input id="upload" type="file" @change="handleFileUpload" accept="image/*" />
</template>

<style scoped>
img{
  border-radius: 1rem;
  margin-bottom: 2rem ;
}
input[type="file"] {
    display: none;
}
label {
  color: white;
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
}
.green{
 color: green
}
label:hover {
  border-color: #646cff;
}
label:focus,
label:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}
</style>
