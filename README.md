# Todolist Vue.js - Projet d'apprentissage

Application de gestion de tâches construite avec Vue.js 3 et la Composition API. Ce projet illustre les concepts fondamentaux de Vue.js.

## 📚 Notions Vue.js utilisées

### 1. **Composition API avec `<script setup>`**
Syntaxe moderne et concise de Vue 3 qui simplifie l'écriture des composants.

```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'
// Pas besoin de return, tout est automatiquement exposé au template
</script>
```

### 2. **Réactivité avec `ref()`**
Crée des variables réactives qui déclenchent le re-rendu du composant lors de leur modification.

```js
const time = ref(0)              // Nombre réactif
const page = ref({               // Objet réactif
  title: ''
})
```

**Important** : Accéder à la valeur avec `.value` dans le script, mais pas dans le template.

### 3. **Watchers - Observer les changements**

Les watchers permettent d'exécuter du code lorsqu'une valeur réactive change.

#### `watch()`
Observe une source réactive spécifique et exécute une fonction callback lors des changements.

```js
import { watch } from 'vue'

// Observer une propriété d'un objet réactif
watch(() => page.value.title, (newValue, oldValue) => {
  document.title = newValue
})

// Observer une ref directement
watch(name, (newValue, oldValue) => {
  document.title = newValue
}, { immediate: true }) // immediate: se déclenche dès le chargement
```

#### `watchEffect()`
Détecte automatiquement les dépendances réactives et s'exécute immédiatement.

```js
import { watchEffect } from 'vue'

watchEffect(() => {
  document.title = page.value.title
  // Pas besoin de spécifier les dépendances
  // Se déclenche automatiquement au chargement
})
```

**À retenir** :
- `watchEffect` pour faire des effets de bord en dehors du cadre de Vue.js (ex: modifier le DOM natif, localStorage)
- `computed` pour dériver une valeur à partir d'une autre valeur réactive (utilisé dans le template)
- `watch` quand vous avez besoin d'accéder aux anciennes valeurs ou contrôler précisément quand le watcher s'exécute

### 4. **Hooks de cycle de vie**

Les hooks permettent d'exécuter du code à des moments précis du cycle de vie d'un composant.

#### `onMounted()`
S'exécute une fois que le composant est monté dans le DOM.

```js
import { onMounted } from 'vue'

onMounted(() => {
  intervalId = setInterval(() => {
    seconds.value++
  }, 1000)
})
```

**Utilisations courantes** :
- Démarrer des timers ou intervalles
- Initialiser des bibliothèques tierces
- Récupérer des données d'une API

#### `onUnmounted()`
S'exécute juste avant que le composant soit retiré du DOM. **Crucial pour le nettoyage** !

```js
import { onUnmounted } from 'vue'

onUnmounted(() => {
  if (intervalId) {
    clearInterval(intervalId)
  }
})
```

**Importance du nettoyage** : Sans `onUnmounted()`, les intervalles/timers continuent de s'exécuter même après la destruction du composant, causant des fuites mémoire.

### 5. **Composables - Réutiliser la logique**

Les composables sont des fonctions qui encapsulent de la logique réactive réutilisable.  
**Convention** : préfixe `use` (ex: `useTimer`, `useCounter`, `useFetch`)

```js
// src/composable/userTimer.js
import { onMounted, onUnmounted, ref } from "vue"

export function useTimer(initial = 0) {
  const time = ref(initial)
  let timer

  onMounted(() => {
    timer = setInterval(() => {
      time.value++
    }, 1000)
  })

  onUnmounted(() => {
    clearInterval(timer)
  })
  
  return {
    time,
    reset() {
      time.value = 0
    }
  }
}
```

**Utilisation dans un composant** :
```vue
<script setup>
import { useTimer } from './composable/userTimer'

const { time, reset } = useTimer()
</script>

<template>
  Temps écoulé : {{ time }} secondes
  <button @click="reset">Reset</button>
</template>
```

**Avantages** :
- Réutilisation de la logique entre plusieurs composants
- Séparation des préoccupations
- Code plus maintenable et testable
- Gestion automatique du cycle de vie

### 6. **Composants et Communication**

#### **Props (`defineProps`)**
Permet de passer des données d'un parent vers un enfant.
```js
// Dans l'enfant (Checkbox.vue)
const props = defineProps({
  label: String
})
```

#### **Événements (`defineEmits`)**
Permet à un enfant d'envoyer des signaux au parent.
```js
// Dans l'enfant (Checkbox.vue)
const emits = defineEmits(['check', 'uncheck'])
const onChange = (event) => {
  if (event.currentTarget.checked) {
    emits('check') // Comme un signal
  } else {
    emits('uncheck')
  }
}
```

#### **v-model sur les composants (`defineModel`)**
Simplifie la liaison bidirectionnelle (two-way binding) entre parent et enfant (Vue 3.4+).
```js
// Dans l'enfant (Checkbox.vue)
const modelValue = defineModel() // Convention de nommage
// Pour plusieurs v-model : defineModel('checkedValue')
```
```html
<!-- Dans le parent -->
<Checkbox v-model="task.completed" @check="..." @uncheck="..." label="Ma tâche" />
```

### 7. **Directives de template**

#### `v-model` (Liaison bidirectionnelle)
```html
<input type="text" v-model="page.title">
```

#### `@` (Gestion d'événements)
```html
<button @click="reset">Reset</button>
```

#### Interpolation `{{ }}`
```html
{{ time }} secondes
```

## 🎯 Exemple concret : Timer

Le composant `Timer.vue` illustre l'utilisation des hooks de cycle de vie :

```vue
<template>
  <div>{{ seconds }}</div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

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
```

## 🏗️ Architecture du projet

```
src/
├── App.vue                  # Composant racine avec watchers
├── composable/
│   └── userTimer.js        # Composable réutilisable pour le timer
├── Timer.vue               # Composant timer avec lifecycle hooks
├── Checkbox.vue            # Composant avec defineModel et emits
├── Button.vue              # Composant avec slot
└── Layout.vue              # Composant avec slots nommés
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