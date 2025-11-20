<template>
    <label>
        <input type="checkbox" v-model="modelValue" @change="onChange">
        <!-- onChange délenche une fonction locale-->
        {{ label }}
    </label>
    
</template>

<script setup>
const modelValue = defineModel() // modelValue n'est pas un nom built-in, c'est juste une convention
// Si j'avais plusieurs v-model, je pourrais faire const checkedValue = defineModel('checkedValue') par exemple
const props = defineProps({ // Propriétés, on reçoit du parent
    label: String
})
const emits = defineEmits(['check', 'uncheck']) // envoi vers le parent
const onChange = (event) => {
    if (event.currentTarget.checked) {
        emits('check') // Comme un signal
    } else {
        emits('uncheck')
    }
}
</script>