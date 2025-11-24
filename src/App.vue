<template>
  <Layout>
    <template v-slot:header>En-tête</template> <!--On peut remplacer v-slot:header par #header-->
    <template v-slot:aside>aside</template>
    <template v-slot:main>main</template>
    <template v-slot:footer>footer</template>
  </Layout>
  <button @click="showTimer = !showTimer">Afficher Masquer</button>
  <Timer v-if="showTimer" />
  <div class="container mt-5">
    <h1 class="text-center mb-4 text-primary">Todolist</h1>
    <Button><strong>Mon bouton</strong></Button>
    <form @submit.prevent="addTask" class="mb-4">
      <div class="input-group">
        <input type="text" v-model="taskName" class="form-control" placeholder="Nouvelle tâche..."></input>
        <button :disabled="taskName == 0" class="btn btn-primary">Ajouter</button>
      </div>
    </form>
    <hr />
    {{ taskName }}

    <div v-if="tasks.length > 0" class="card">
      <ul class="list-group list-group-flush">
        <li v-for="task in sortedTasks" :key="task.date" :style="{'text-decoration': task.completed ? 'line-through' : ''}" class="list-group-item">{{ task.title }} - <div :class="{'text-danger': task.completed == false}">{{ task.completed ? 'Terminé': 'En cours' }}</div>
        <Checkbox :label="task.title" 
        v-model="task.completed"
        @check="console.log('coché')"
        @uncheck="console.log('décoché')"
        />
      </li>
      </ul>
      <label>
        <input type="checkbox" v-model="hideCompleted"> 
        Masquer les tâches completed
      </label>
      <p v-if="remainingTasks > 0">{{ remainingTasks }} tâche{{ remainingTasks > 1 ? 's': '' }} à faire</p>
    </div>
    <div v-else class="alert alert-info text-center">Vous n'avez pas encore de tâche.</div>
  </div>
</template>

<style scoped>
@import url('https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css');
</style>

<script setup>
import {computed, onMounted, ref} from 'vue'
import Checkbox from './Checkbox.vue'
import Button from './Button.vue'
import Layout from './Layout.vue'
import Timer from './Timer.vue'

const showTimer = ref(true)

const tasks = ref([])
onMounted(async () => {
  const response = await fetch("taches.json")
  const data = await response.json()
  tasks.value = data
})

const addTask = () => {
  tasks.value.push({
    title: taskName.value,
    completed: false,
    date: Date.now()
  })
  taskName.value = ""
}
const taskName = ref("")

const sortedTasks = computed(() => {
  // toSorted() utilise l'algorithme de tri
  // prend deux éléments du tableau en entrée
  // Les compare et retourne un nombre négatif, zéro ou positif
  // Il parcours tout le tableau et le trie en fonction des valeurs retournées
  const sortedTasks = tasks.value.toSorted((a, b) => a.completed - b.completed)
  if (hideCompleted.value === true) {
    return sortedTasks.filter(t => t.completed === false)
  }
  return sortedTasks
})
// Avantages de computed : recalcul uniquement lorsque les dépendances changent, optimisation des performances

const hideCompleted = ref(false)

const remainingTasks = computed(() => {
  return tasks.value.filter(t => t.completed === false).length
})

</script>
