<template>
    <div>{{ seconds }}</div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const seconds = ref(0)
let intervalId = null

onMounted(() => {
    intervalId = setInterval(() => {
        seconds.value++
    }, 1000)
})

onUnmounted(() => {
    if (intervalId) {
        clearInterval(intervalId)
    }
})
</script>