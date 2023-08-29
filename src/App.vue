<script setup>
import {nextTick, onBeforeUnmount, onMounted, ref} from "vue"
import {Diorama} from "@kshemaka/diorama";

import scene from "./assets/spheres.x3d?raw"

const canvas = ref(null)
let diorama = null

onMounted(() => {
  if (canvas.value.getContext) {
    const c = canvas.value
    c.width = 400
    c.height = 400
    diorama = new Diorama(c)
    diorama.loadScene(scene)

    nextTick(() => {
      window.addEventListener('keydown', onKeyDown)
      window.addEventListener('keyup', onKeyUp)
    })
  } else {
    // fallback content
  }
})

onBeforeUnmount(() => {
  window.removeEventListener('keyup', onKeyUp)
  window.removeEventListener('keydown', onKeyDown)
})

function onKeyDown(event) {
  diorama.setAction(event.key, true)
}

function onKeyUp(event) {
  diorama.setAction(event.key, false)
}
</script>

<template>
  <canvas ref="canvas">
    <!-- TODO ensure that fallback content is provided for accessibility -->
  </canvas>
</template>

<style scoped>
canvas {
  display: block;
  margin: auto;
  border: 1px solid black;
}
</style>
