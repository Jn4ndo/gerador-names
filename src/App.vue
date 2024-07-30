<template>
  <div id="container" class="container text-center my-4">
    <img src="./assets/lamp.png" alt="lamp" class="img-fluid mb-3" style="width: 150px;">
    
    <div id="texts" class="mb-4">
      <h1 id="title">Está sem ideias de nomes?</h1>
      <p class="small-p">Estou aqui pra te ajudar!</p>
      <div class="container-names">
        <img v-if="loading" src="https://img1.gratispng.com/20180320/crq/kisspng-computer-icons-desktop-wallpaper-command-load-free-icon-5ab096780da054.0807956015215222960558.jpg"
             alt="loading" class="loading me-2" style="width: 25px;">
        <ul v-else id="nomes" class="list-unstyled text-end">
          <li v-for="name in generatedNames" :key="name">{{ name }}</li>
        </ul>
      </div>
    </div>

    <div class="mb-3">
      <label for="nameCount" class="form-label">Quantos nomes deseja gerar?</label>
      <select v-model="nameCount" id="nameCount" class="form-select w-auto mx-auto">
        <option v-for="n in 10" :key="n" :value="n">{{ n }}</option>
      </select>
    </div>

    <div id="container-generate" class="d-flex justify-content-center flex-wrap gap-2">
      <button id="generator-button" class="btn btn-primary d-flex align-items-center" @click="namesGenerator">
        <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-arrow-big-top me-2" width="30" height="25" viewBox="0 0 24 24" stroke-width="1.5" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
          <path stroke="none" d="M0 0h24v24H0z" fill="none" />
          <path d="M9 20v-8h-3.586a1 1 0 0 1 -.707 -1.707l6.586 -6.586a1 1 0 0 1 1.414 0l6.586 6.586a1 1 0 0 1 -.707 1.707h-3.586v8a1 1 0 0 1 -1 1h-4a1 1 0 0 1 -1 -1z" />
        </svg>
        Gerar aleatório
      </button>

      <button id="automatic-button" class="btn btn-primary d-flex align-items-center" @click="automaticGeneratorMen">
        <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-settings-automation me-2" width="30" height="25" viewBox="0 0 24 24" stroke-width="1.5" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
          <path stroke="none" d="M0 0h24v24H0z" fill="none" />
          <path d="M10.325 4.317c.426 -1.756 2.924 -1.756 3.35 0a1.724 1.724 0 0 0 2.573 1.066c1.543 -.94 3.31 .826 2.37 2.37a1.724 1.724 0 0 0 1.065 2.572c1.756 .426 1.756 2.924 0 3.35a1.724 1.724 0 0 0 -1.066 2.573c.94 1.543 -.826 3.31 -2.37 2.37a1.724 1.724 0 0 0 -2.572 1.065c-.426 1.756 -2.924 1.756 -3.35 0a1.724 1.724 0 0 0 -2.573 -1.066c-1.543 .94 -3.31 -.826 -2.37 -2.37a1.724 1.724 0 0 0 -1.065 -2.572c-1.756 -.426 -1.756 -2.924 0 -3.35a1.724 1.724 0 0 0 1.066 -2.573c-.94 -1.543 .826 -3.31 2.37 -2.37c1 .608 2.296 .07 2.572 -1.065z" />
          <path d="M10 9v6l5 -3z" />
        </svg>
        Automático M
      </button>

      <button id="automatic-F" class="btn btn-primary d-flex align-items-center" @click="automaticGeneratorWomen">
        <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-settings-automation me-2" width="30" height="25" viewBox="0 0 24 24" stroke-width="1.5" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
          <path stroke="none" d="M0 0h24v24H0z" fill="none" />
          <path d="M10.325 4.317c.426 -1.756 2.924 -1.756 3.35 0a1.724 1.724 0 0 0 2.573 1.066c1.543 -.94 3.31 .826 2.37 2.37a1.724 1.724 0 0 0 1.065 2.572c1.756 .426 1.756 2.924 0 3.35a1.724 1.724 0 0 0 -1.066 2.573c.94 1.543 -.826 3.31 -2.37 2.37a1.724 1.724 0 0 0 -2.572 1.065c-.426 1.756 -2.924 1.756 -3.35 0a1.724 1.724 0 0 0 -2.573 -1.066c-1.543 .94 -3.31 -.826 -2.37 -2.37a1.724 1.724 0 0 0 -1.065 -2.572c-1.756 -.426 -1.756 -2.924 0 -3.35a1.724 1.724 0 0 0 1.066 -2.573c-.94 -1.543 .826 -3.31 2.37 -2.37c1 .608 2.296 .07 2.572 -1.065z" />
          <path d="M10 9v6l5 -3z" />
        </svg>
        Automático F
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';


const generatedNames = ref<string[]>([]);
const loading = ref(false);
const nameCount = ref(1);

async function fetchNames(count: number, gender: string = '') {
  try {
    const genderQuery = gender ? `&gender=${gender}` : '';
    const response = await axios.get(`https://randomuser.me/api/?results=${count}&inc=name${genderQuery}`);
    return response.data.results.map((user: any) => `${user.name.first} ${user.name.last}`);
  } catch (error) {
    console.error('Error fetching names:', error);
    return [];
  }
}

async function namesGenerator() {
  generatedNames.value = [];
  loading.value = true;
  
  const names = await fetchNames(nameCount.value);
  generatedNames.value = names;
  loading.value = false;
}

async function automaticGeneratorMen() {
  generatedNames.value = [];
  loading.value = true;
  
  const names = await fetchNames(nameCount.value, 'male');
  generatedNames.value = names;
  loading.value = false;
}

async function automaticGeneratorWomen() {
  generatedNames.value = [];
  loading.value = true;
  
  const names = await fetchNames(nameCount.value, 'female');
  generatedNames.value = names;
  loading.value = false;
}
</script>

<style scoped>
.container-names {
  display: flex;
  justify-content: flex;
  align-items: center;
}

.loading {
  margin-right: 8px;
}

.form-select {
  display: inline-block;
}

.container-generate {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 8px;
}

#container-generate button {
  margin-top: 8px;
  background-color: #007bff;
  border-color: #007bff;
}
</style>
