<template>
	<h1>Selectionnez un poste</h1>
	<select v-model="reponsePoste">
		<option v-for="poste in postes" :key="poste" :value="poste">{{ poste }}</option>
	</select>
	<button @click="saveQuestion">valider</button>
</template>

<script setup>
import { ref } from 'vue';
import { postesR } from '../Reponses/reponses'

const postes = ref(postesR);
const reponsePoste = ref(null);

const emit = defineEmits(['formSubmitted']);

const saveQuestion = () => {
	emit('formSubmitted', { poste: reponsePoste.value });
};
</script>

