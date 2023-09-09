<script setup>
import { computed, nextTick, onBeforeUnmount, onMounted, ref, watch } from "vue"
import { initializeDiorama } from "@kshemaka/diorama"

import scene from "./assets/spheres.x3d?raw"

const debounce = function (fn, t) {
    let timer
    return function (...args) {
        clearTimeout(timer)
        timer = setTimeout(() => fn(...args), t)
    }
}

const canvas = ref(null)

let diorama = null
const width = ref(window.innerWidth)

const height = ref(window.innerHeight)
const style = computed(() => {
    return {
        width: width.value + "px",
        height: height.value + "px"
    }
})
const dimensions = computed(() => {
    return {
        width: width.value,
        height: height.value
    }
})
watch(dimensions, () => {
    diorama.resizeCanvas(dimensions.value)
})
const updateDimensions = debounce((w, h) => {
    width.value = w
    height.value = h
}, 100)

onMounted(() => {
    if (canvas.value.getContext) {
        const c = canvas.value
        c.width = 400 // max canvas width
        c.height = 400 // max canvas height
        diorama = initializeDiorama(c)
        diorama.loadScene(scene)

        nextTick(() => {
            window.addEventListener("resize", onResize)
            window.addEventListener("keydown", onKeyDown)
            window.addEventListener("keyup", onKeyUp)
        })
    } else {
        // fallback content
    }
})

onBeforeUnmount(() => {
    window.removeEventListener("keyup", onKeyUp)
    window.removeEventListener("keydown", onKeyDown)
    window.removeEventListener("resize", onResize)
})

function onResize() {
    updateDimensions(window.innerWidth, window.innerHeight)
}

function onKeyDown(event) {
    diorama.triggerAction(event.key, true)
}

function onKeyUp(event) {
    diorama.triggerAction(event.key, false)
}
</script>

<template>
    <canvas ref="canvas" :style="style">
        <!-- <canvas ref="canvas"> -->
        <!-- TODO ensure that fallback content is provided for accessibility -->
    </canvas>
</template>

<style scoped>
canvas {
    display: block;
    width: 100%;
    height: 100%;
}
</style>
