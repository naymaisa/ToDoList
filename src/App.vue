<script setup>
import { ref, onMounted, computed, watch } from 'vue';
import axios from 'axios'; 

const showGreeting = ref(true);
const todos = ref([]);
const nome = ref('');
const input_content = ref('');
const input_category = ref(null);

const startTodoList = () => {
  showGreeting.value = false;
  localStorage.setItem('nome', nome.value);
};

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}));

const fetchTodos = async () => {
  try {
    const response = await axios.get('http://localhost:3000/todo');
    todos.value = response.data;  
  } catch (error) {
    console.error('Erro ao buscar tarefas:', error);
  }
};

const addTodo = async () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }

  const newTodo = {
    tarefa: input_content.value,  
    type: input_category.value,   
    done: false,                
    createdAt: new Date().getTime() 
  };

  try {
    await axios.post('http://localhost:3000/todo', newTodo);
    console.log('Tarefa adicionada com sucesso');
    fetchTodos();
  } catch (error) {
    console.error('Erro ao adicionar tarefa:', error);
  }
};

const removeTodo = async (todo) => {
  try {

    await axios.delete(`http://localhost:3000/todo/${todo._id}`);
    todos.value = todos.value.filter(t => t._id !== todo._id); 
  } catch (error) {
    console.error('Erro ao deletar tarefa:', error);
  }
};

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true });

watch(nome, (newVal) => {
  localStorage.setItem('nome', newVal);
});

onMounted(() => {
  nome.value = localStorage.getItem('nome') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
  fetchTodos();  
});
</script>

<template>
  <div>
    <section v-if="showGreeting" class="greeting">
      <p class="welcome">Seja bem-vindo ao seu gerenciador de tarefas! Clique no botão abaixo para começar.</p>
      <button class="start" @click="startTodoList">Iniciar Tarefas</button>
    </section>

    <main v-if="!showGreeting" class="app">
      <section class="create-todo">
        <h3>Crie uma to do list</h3>
        <form @submit.prevent="addTodo">
          <h4>O que está na sua lista de tarefas?</h4>
          <input type="text" placeholder="ex. estudar JavaScript" v-model="input_content" />

          <h4>Escolha uma categoria</h4>
          <div class="options">
            <label>
              <input type="radio" name="category" value="negocios" v-model="input_category">
              <span class="bubble negocios"></span>
              <div>Negócios</div>
            </label>

            <label>
              <input type="radio" name="category" value="pessoal" v-model="input_category">
              <span class="bubble pessoal"></span>
              <div>Pessoal</div>
            </label>

            <div>Categoria Selecionada: {{ input_category }}</div>
          </div>

          <input type="submit" value="Add todo"/>
        </form>
      </section>

      <section class="todo-list">
        <h3>TODO LIST</h3>
        <div class="list">
          <div v-for="todo in todos_asc" :key="todo._id" :class="['todo-item', todo.done && 'done']">
            <label>
              <input type="checkbox" v-model="todo.done"/>
              <span :class="['bubble', todo.type]"></span> 
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.tarefa"/> 
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>

</style>
