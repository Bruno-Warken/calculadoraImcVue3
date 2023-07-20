<template>
	<main>
		<form v-if="!mostrarHistorico" @submit.prevent="isValidInputs">
			<h2>Calculadora IMC</h2>

			<div class="inputContainer">

				<label for="nome">Nome</label>
				<div class="inputControl">
					<input
						:class="{erroOutline: isValidNome === false, sucessoOutline: isValidNome === true}"
						v-model="nome"
						type="text"
						id="nome"
						name="nome"
						autocomplete="off"
						placeholder="Ex: Bruno"
					/>
					<div class="erro">{{ nomeErroMsg }}</div>
				</div>

				<label for="altura">Altura (cm)</label>
				<div class="inputControl">
					<input
						:class="{erroOutline: isValidAltura === false, sucessoOutline: isValidAltura === true}"
						v-model.number="altura"
						type="number"
						id="altura"
						name="altura"
						autocomplete="off"
						placeholder="Ex: 183"
					/>
					<div class="erro">{{ alturaErroMsg }}</div>
				</div>

				<label for="peso">Peso (kg)</label>
				<div class="inputControl">
					<input
						:class="{erroOutline: isValidPeso === false, sucessoOutline: isValidPeso === true}"
						v-model.number="peso"
						type="number"
						id="peso"
						name="peso"
						autocomplete="off"
						placeholder="Ex: 88"
					/>
					<div class="erro">{{ pesoErroMsg }}</div>
				</div>

			</div>

			<div class="buttonControl">
				<button id="calcular">Calcular</button>
				<button id="limpar" type="button" @click="limpar">Limpar Campos</button>
				<button id="historico" type="button" @click="isArrayFilled">Histórico</button>
			</div>

			<div class="resultado">
				{{ imcClasse }}
			</div>
		</form>

		<div v-else class="historicoContainer">
			<table class="tabelaHistorico">

				<tr>
					<th>Nome</th>
					<th>Altura</th>
					<th>Peso</th>
					<th>IMC</th>
				</tr>

				<tr v-for="user in usersArray" :key="user.id">
					<td>{{ user.nome }}</td>
					<td>{{ user.altura }}</td>
					<td>{{ user.peso }}</td>
					<td>{{ user.imc }}</td>
				</tr>

			</table>

			<button id="voltar" @click="historico">Voltar</button>
		</div>
	</main>
</template>

<script>
import {ref} from 'vue'

export default {
	setup() {

		const nome = ref('');
		const altura = ref(null);
		const peso = ref(null);
		const imcClasse = ref('');
		const usersArray = ref([]);
		const mostrarHistorico = ref(false);
		const nomeErroMsg = ref('');
		const alturaErroMsg = ref('');
		const pesoErroMsg = ref('');
		const isValidNome = ref(null);
		const isValidAltura = ref(null);
		const isValidPeso = ref(null);
		let isValid = false;

		function isValidInputs() {
			if (!nome.value.trim() || !/^[A-Za-z ]+$/.test(nome.value)) {
				nomeErroMsg.value = 'Nome inválido';
				isValidNome.value = false;
				isValid = false;
			} else {
				nomeErroMsg.value = '';
				isValidNome.value = true;
				isValid = true;
			}

			if (!altura.value || isNaN(altura.value) || altura.value <= 0) {
				alturaErroMsg.value = 'Altura inválida';
				isValidAltura.value = false;
				isValid = false;
			} else {
				alturaErroMsg.value = '';
				isValidAltura.value = true;
				isValid = true;
			}

			if (!peso.value || isNaN(peso.value) || peso.value <= 0) {
				pesoErroMsg.value = 'Peso inválido';
				isValidPeso.value = false;
				isValid = false;
			} else {
				pesoErroMsg.value = '';
				isValidPeso.value = true;
				isValid = true;
			}

			if (isValid === true) {
				calcularIMC();
			}
		}

		function calcularIMC() {

			const alturaMetros = altura.value / 100;
			const imcValor = peso.value / (alturaMetros ** 2);

			if (imcValor < 18.5) {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está abaixo do peso.';
			} else if (imcValor < 24.9) {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está em seu peso ideal.'
			} else if (imcValor < 29.9) {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está com sobrepeso.'
			} else if (imcValor < 34.9) {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está com obesidade grau I.'
			} else if (imcValor < 39.9) {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está com obesidade grau II (severa).'
			} else {
				imcClasse.value = 'Seu IMC é de: ' + imcValor.toFixed(2) + '. Você está com obesidade grau III (mórbida).'
			}

			usersArray.value.push({
				id: Date.now(),
				nome: nome.value,
				altura: altura.value,
				peso: peso.value,
				imc: imcValor.toFixed(2)
			})
		}

		function isArrayFilled() {
			if (usersArray.value.length > 0) {
				historico()
			} else {
				alert('Não existem usuários registrados')
			}
		}

		function historico() {
			mostrarHistorico.value = !mostrarHistorico.value;
		}

		function limpar() {
			nome.value = '';
			altura.value = null;
			peso.value = null;
			isValidNome.value = ref(null);
			isValidAltura.value = ref(null);
			isValidPeso.value = ref(null);
		}

		return {
			nome,
			altura,
			peso,
			usersArray,
			imcClasse,
			mostrarHistorico,
			nomeErroMsg,
			alturaErroMsg,
			pesoErroMsg,
			isValid,
			isValidNome,
			isValidAltura,
			isValidPeso,
			isArrayFilled,
			isValidInputs,
			historico,
			limpar,
		}
	}
}
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');

:root {
	--linearGradient: linear-gradient(90deg, #D884FF 0%, rgba(218, 140, 255, 0.50) 50%, rgba(198, 125, 255, 0.25) 100%);
	--lightPurple: hsl(256, 100%, 60%);
	--lightPurpleHover: hsl(256, 100%, 65%);
	--lighterPurple: hsl(256, 65%, 70%);
	--lighterPurpleHover: hsl(256, 65%, 75%);
	--lightGray: hsl(0, 0%, 60%);
	--dirtyWhite: hsl(0, 0%, 95%);
	--errorColor: hsl(0, 100%, 50%);
	--successColor: hsl(120, 86%, 67%);
}

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

body {
	font-family: 'Poppins', sans-serif;
	background: var(--linearGradient);
}

form, .historicoContainer {
	margin: 100px auto;
	border-radius: 10px;
	width: 300px;
	background-color: var(--dirtyWhite);
	box-shadow: 3px 3px 5px -1px rgba(0, 0, 0, 0.25);
	padding: 20px;
	display: flex;
	flex-direction: column;
	align-items: center;
}

h2 {
	margin-bottom: 20px;
	font-size: 20px;
	font-weight: 600;
	color: var(--lightPurple);
}

label {
	margin-top: 7px;
	font-size: 14px;
	color: var(--lightPurple);

}

input {
	width: 130px;
	height: 35px;
	border-radius: 5px;
	border: transparent;
	box-shadow: 3px 3px 5px -1px rgba(0, 0, 0, 0.25);
	padding-left: 5px;
	color: var(--lightGray);
}

.inputContainer {
	display: grid;
	grid-template-columns: repeat(2, 1fr);
	grid-template-rows: repeat(3, 1fr);
	margin: 15px;
	gap: 8px;
}

input:focus {
	outline: none;
}

.erroOutline {
	border: 1px solid var(--errorColor);
}

.sucessoOutline {
	border: 1px solid var(--successColor);
}

button {
	font-size: 12px;
	width: 100%;
	height: 35px;
	border-radius: 5px;
	border: transparent;
	background-color: var(--lightPurple);
	color: hsl(0, 0%, 100%);
}

button:hover {
	background-color: var(--lightPurpleHover);
	transition: background-color 0.3s;
	cursor: pointer;
}

button:active {
	transform: translateY(1px);
}

.buttonControl {
	width: 100%;
	display: grid;
	grid-template-rows: 1fr 1fr;
	grid-template-columns: 1fr 1fr;
	grid-template-areas: 
		"calc calc"
		"limp hist";
	gap: 10px;
}

#calcular {
	grid-area: calc;
	font-size: 14px;
}

#limpar {
	grid-area: limp;
	background-color: var(--lighterPurple);
}

#historico {
	grid-area: hist;
	background-color: var(--lighterPurple);
}

:is(#historico, #limpar):hover {
	background-color: var(--lighterPurpleHover);
}

#voltar {
	margin-top: 15px;
}

::placeholder {
	color: var(--lightGray);
	font-size: 12px;
}

.erro {
	font-size: 10px;
	color: var(--errorColor);
	height: 18px;
	padding-top: 2px;
}

.resultado {
	text-align: center;
	margin: 25px auto 0;
	color: var(--lightPurple);
}

table {
	border-collapse: collapse;
	color: var(--lightPurple);
}

td, th {

	text-align: center;
}

:is(td,th):not(:last-child) {
	border-right: 1px solid var(--lightPurple);
}

tr {
	text-align: left;
	gap: 20px;
	margin-bottom: 5px;
}

tr:not(:last-child) {
	border-bottom: 1px solid var(--lightPurple);
}

tr > * {
	padding: 10px;
}

</style>