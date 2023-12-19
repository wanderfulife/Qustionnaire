<script setup>
import { ref } from 'vue';
import Prenom from './Components/Prenom.vue';
import Question1 from './Components/Question1.vue';


const prenom = ref(null);
const date = ref(null);
const poste = ref(null)

const onFormSubmitted = (data) => {
  prenom.value = data.name;
  date.value = data.date;
};

const onPostSubmitted = (data) => {
  poste.value = data.poste;
  alert(poste.value + date.value + prenom.value)
}

</script>

<template>
  <div class="container">
    <div v-if="!prenom">
      <Prenom @formSubmitted="onFormSubmitted" />
    </div>
    <div v-else-if="!poste">
      <Question1 @formSubmitted="onPostSubmitted"/>
    </div>
    <div v-else>
      <h1>question2</h1>
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  font-family:  Arial;
  background-color: #343541;
  font-size: 1.1em;
  color: beige;
  font-weight: bold;
}

input {
  border: 0;
  outline: 0;
	color: grey;
}
.container {
  display: flex;
}

</style>
