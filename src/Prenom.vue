<template>
	<form autocomplete="off" @submit.prevent="saveName" class="field">
		<input placeholder="Nom enquêteur :" type="input" class="form__field" v-model="name" id='name' required />
	</form>
</template>

<script setup>
import { ref } from 'vue';

const name = ref('');
const date = ref('');

const saveName = () => {
	date.value = new Date().toLocaleString("fr-FR", { timeZone: "Europe/Paris" });
	console.log('Name saved:', name.value);
	console.log('Time:', date.value);


};
</script>

<style>
.form__field {
	border: 0;
	outline: 0;
	font-size: 1.3rem;
	color: #fff;
	background: transparent;
}

.form__label {
	top: 0;
	display: block;
	font-size: 1rem;
	color: #fff;
}

input:focus::placeholder {
	color: transparent;
}

body {
	display: flex;
	justify-content: center;
	align-items: center;
	min-height: 100vh;
	font-size: 1.5rem;
	background-color: #343541;
}
</style>