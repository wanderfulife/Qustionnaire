<template>
	<h1>Nom Enqueteur :</h1>
	<form autocomplete="off"  @submit.prevent="saveName">
		<input autofocus type="text" v-model="name" required />
	</form>
</template>

<script setup>
import { ref } from 'vue';

const name = ref('');
const date = ref('');
const emit = defineEmits(['formSubmitted']);

const saveName = () => {
	date.value = new Date().toLocaleString("fr-FR", { timeZone: "Europe/Paris" });
	emit('formSubmitted', { name: name.value, date: date.value });
};
</script>