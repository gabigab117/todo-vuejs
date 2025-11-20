# Todolist Vue.js - Projet d'apprentissage

Application de gestion de tâches construite avec Vue.js 3 et la Composition API. Ce projet illustre les concepts fondamentaux de Vue.js.

## 📚 Notions Vue.js utilisées

### 1. **Composition API avec `<script setup>`**
Syntaxe moderne et concise de Vue 3 qui simplifie l'écriture des composants.

```vue
<script setup>
import { ref, computed } from 'vue'
// Pas besoin de return, tout est automatiquement exposé au template
</script>
```

### 2. **Réactivité avec `ref()`**
Crée des variables réactives qui déclenchent le re-rendu du composant lors de leur modification.

```js
const taskName = ref("")          // Chaîne de caractères réactive
const tasks = ref([])             // Tableau réactif
const hideCompleted = ref(false)  // Booléen réactif
```

**Important** : Accéder à la valeur avec `.value` dans le script, mais pas dans le template.

### 3. **Propriétés calculées avec `computed()`**
Valeurs dérivées qui se recalculent automatiquement quand leurs dépendances changent. Optimisées avec mise en cache.
computed s'utilise donc que pour les valeurs dérivées. Ex sortedTasks est un dérivé de tasks.

```js
const sortedTasks = computed(() => {
  // Se recalcule uniquement si tasks ou hideCompleted changent
  const sorted = tasks.value.toSorted((a, b) => a.completed - b.completed)
  return hideCompleted.value ? sorted.filter(t => !t.completed) : sorted
})
```

**Avantages** :
- Recalcul uniquement lorsque les dépendances changent
- Mise en cache pour optimiser les performances
- Plus performant que des méthodes appelées dans le template

### 4. **Directives de template**

#### `v-model` (Liaison bidirectionnelle)
```vue
<input v-model="taskName">
<!-- Équivalent à :value="taskName" @input="taskName = $event.target.value" -->

<input type="checkbox" v-model="task.completed">
```

#### `v-if` / `v-else` (Rendu conditionnel)
```vue
<div v-if="tasks.length > 0">
  <!-- Affiche si la condition est vraie -->
</div>
<div v-else>
  <!-- Affiche sinon -->
</div>
```

#### `v-for` (Boucles)
```vue
<li v-for="task in sortedTasks" :key="task.date">
  <!-- :key est obligatoire pour l'optimisation du Virtual DOM -->
</li>
```

**Important** : L'attribut `:key` doit être unique pour chaque élément.

#### `:` (Binding d'attributs)
Raccourci de `v-bind:` pour lier dynamiquement des attributs HTML.

```vue
<button :disabled="taskName == 0">
<div :style="{'text-decoration': task.completed ? 'line-through' : ''}">
<div :class="{'text-danger': task.completed == false}">
```

### 5. **Gestion d'événements avec `@`**
Raccourci de `v-on:` pour écouter les événements DOM.

```vue
<form @submit.prevent="addTask">
  <!-- @submit = v-on:submit -->
  <!-- .prevent = modificateur qui appelle preventDefault() automatiquement -->
</form>
```

### 6. **Interpolation de données `{{ }}`**
Affiche les données réactives dans le template.

```vue
{{ taskName }}
{{ task.completed ? 'Terminé': 'En cours' }}
```

### 7. **Méthodes et fonctions**
Dans `<script setup>`, les fonctions déclarées sont automatiquement disponibles dans le template.

```js
const addTask = () => {
  tasks.value.push({
    title: taskName.value,
    completed: false,
    date: Date.now()
  })
  taskName.value = ""
}
```

## 🎯 Concepts importants du projet

### Tri des tâches
Utilisation de `toSorted()` pour trier sans muter le tableau original :

```js
tasks.value.toSorted((a, b) => a.completed - b.completed)
// Retourne : nombre négatif (a avant b), 0 (égal), nombre positif (b avant a)
// Résultat : tâches non complétées en premier (false = 0, true = 1)
```

### Filtrage conditionnel
Combinaison de tri et filtrage avec `computed` :

```js
const sortedTasks = computed(() => {
  const sorted = tasks.value.toSorted((a, b) => a.completed - b.completed)
  if (hideCompleted.value === true) {
    return sorted.filter(t => t.completed === false)
  }
  return sorted
})
```

## 🚀 Lancer le projet

```bash
# Installer les dépendances
npm install

# Lancer le serveur de développement
npm run dev
```

## 📦 Technologies

- **Vue.js 3** - Framework JavaScript progressif
- **Vite** - Build tool et dev server ultra-rapide
- **Bootstrap 5** - Framework CSS pour le style