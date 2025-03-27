<script setup>
const name = "Layout";
import { ref, onMounted, computed, watch } from 'vue';

const todos =ref([])
const nome =ref('')
const insput_content =ref('')
const insput_category =ref (null)

const todos_asc =computed(()=> todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
}))

watch((nome,newVal) => {
    localStorage.setItem('nome', newVal)
})
</script>

<template>
  <main class="app">

    <section class="greeting">
        <h2 class="title">
            Como vai você, <input type="text" placeholder="escreva seu nome" v-model="nome">
        </h2>
    </section>
 </main>
</template>

<style scoped>

</style>
