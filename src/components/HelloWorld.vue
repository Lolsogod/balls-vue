<script setup lang="ts">
//import { ref } from 'vue'
import * as tf from '@tensorflow/tfjs';



const loadModel = async () => {
  return tf.loadGraphModel("/src/assets/Balls_model_js/model.json")
}

const start = async () => {
  const model = await loadModel()
  //model.execute()
}

const preprocess = async () => {
  
}

</script>

<template>
  <img src="../assets/ball.png" width="500"/>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
