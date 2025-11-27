<template>
  <h1>Tuto Vue js</h1>
  <input type="text" v-model="page.title">
  Temps écoulé : {{ time }} secondes
  <button @click="reset">Reset</button>
</template>

<style scoped></style>

<script setup>
import { ref, watch, watchEffect } from 'vue'
import { useTimer } from './composable/userTimer'

const page = ref({
  title: ''
})

// !! A RETENIR
// watchEffect pour faire des effets de bords en dehors du cadre de Vue Js
// computed pour dériver une valeur à partir d'une autre valeur réactive

// Le watchEffect dans ce cas simplifie.
watchEffect(() => {
  document.title = page.value.title
})

// watch(() => page.value.title, (newValue, oldValue) => {
  // document.title = newValue
// })


// const name = ref('')

// watch(name, (newValue, oldValue) => {
//  document.title = newValue
// }, { immediate: true}) // Avec immediate se déclenche au chargement

// On en profite pour parler de la composition
// créé fonction externe on utilise le prefix use
const {time, reset} = useTimer()
</script>
