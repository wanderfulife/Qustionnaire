<template>
	<h1>Poste</h1>
	<select v-model="reponsePoste">
		<option disabled value="">Please select one</option>
		<option v-for="poste in postes" :key="poste" :value="poste">{{ poste }}</option>
	</select>
	<button @click="saveQuestion">suivant</button>
</template>

<script setup>
import { ref } from 'vue';

const postes = ref([
	'Poste 1 (RD27) Caen vers Deauville - Giratoire RD27 x RD40',
	'Poste 2 (RD675) Caen vers Pont Audemer - Giratoire RD675 x RD228',
	'Poste 3 (RD613) Caen vers Lisieux - Giratoire RD613 x RD41',
	'Poste 4 (RD562) Le Hom vers Caen - Giratoire RD562 x RD257',
	'Poste 5 (RD7) Caen vers DLD - Giratoire RD7 x RD404',
	'Poste 6 (RD515) Caen vers Ouistreham - Giratoire RD514 x RD84',
	'Poste 7 (A84) Caen vers Rennes - Aire de repos de St - Jean des Essartiens',
	'Poste 8 (RN158) Caen vers Falaise - Aire de repos de Soulangy Ouest'
]);

const reponsePoste = ref('');

const emit = defineEmits(['formSubmitted']);

const saveQuestion = () => {
	emit('formSubmitted', { poste: reponsePoste.value });
};
</script>