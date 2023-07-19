<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])

const weightChartEl = ref(null)

const weightChart = shallowRef(null)

const weightInput = ref(0)

const currentWeight = computed(() => {
	return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})

const addWeight = () => {
	weights.value.push({
		weight: weightInput.value,
		date: new Date().getTime()
	})
}

watch(weights, (newWeights) => {
	const ws = [...newWeights]

	if (weightChart.value) {
		weightChart.value.data.labels = ws
			.sort((a, b) => a.date - b.date)
			.map(weight => new Date(weight.date).toLocaleDateString() )
			.slice(-7)

		weightChart.value.data.datasets[0].data = ws
			.sort((a, b) => a.date - b.date)
			.map(weight => weight.weight)
			.slice(-7)

		weightChart.value.update()
		return
	}

	nextTick(() => {
		weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
			type: 'line',
			data: {
				labels: ws
					.sort((a, b) => a.date - b.date)
					.map(weight => new Date(weight.date).toLocaleDateString()),
				datasets: [
					{
						label: 'Weight',
						data: ws
							.sort((a, b) => a.date - b.date)
							.map(weight => weight.weight),
						backgroundColor: 'rgba(255, 105, 180, 0.2)',
						borderColor: 'rgba(255, 105, 180, 1)',
						borderWidth: 1,
						fill: true
					}
				]
			},
			options: {
				responsive: true,
				maintainAspectRatio: false
			}
		})
	})
}, { deep: true })


</script>

<template>
	<main>
		<div>
			<img class="golden-icon" src="./assets/weight-app-icon.png" alt="">
		</div>
		<h1>Your weight in gold!</h1>
		


		<div class="current">
			<span>{{ currentWeight.weight }}</span>
			<small>Current Weight (kg)</small>
		</div>

		<form class="weightSquare" @submit.prevent="addWeight">
			<input 
				type="number"
				step="0.1"
				v-model="weightInput" />

			<input	
				type="submit"
				value="Add weight" />
		</form>

		<div v-if="weights && weights.length > 0">

			<h2>
				Last 7 days
			</h2>

			<div class="canvas-box">
				<canvas ref="weightChartEl"></canvas>
			</div>

			<div class="weight-history">
				<h2>Weight History</h2>
				<ul>
					<li v-for="weight in weights">
						<span>{{ weight.weight }}kg</span>
						<small>
							{{ new Date(weight.date).toLocaleDateString() }}
						</small>
					</li>
				</ul>
			</div>

		</div>

	</main>
</template>

<style>

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'montserrat', sans-serif;
}

body {
	background-color: #f8f8f6;
	max-width: 45rem;
	margin-right: auto;
	margin-left: auto;
	
	
}
.golden-icon{
	max-height: 5rem;
	margin-top: 1rem;
}

main {
	/* padding: 0rem; */
	background-color: #fceb8c;
	margin-left: auto;
	margin-right: auto;
	max-height: fit-content;
	padding-bottom: 0.1rem;
}

h1 {
	font-size: 2rem;
	text-align: center;
	margin-bottom: 1rem;
	margin-top: 1rem;
	border: solid #FFD700;
	max-width: fit-content;
	margin-left: auto;
	margin-right: auto;
	background-color: white;
	padding: 0.5rem;

}

h2 {
	margin: 1rem 0;
	padding: 0.5rem;
	color: #0a0a0a;
	font-weight: 400;
	text-align: center;
	border: solid #FFD700;
	max-width: fit-content;
	margin-left: auto;
	margin-right: auto;
	background-color: azure;
}
.window .wrapper[data-v-e091cbd0] {
    padding-left: 0;
    /* direction: inherit;
    flex: 1 1 0%; */
}
.current {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;

	width: 200px;
	height: 200px;
	
	text-align: center;
	background-color: white;
	border-radius: 999px;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	border: 5px solid #FFD700;
	
	margin: 0 auto 2rem;
}
.weightSquare{
	margin-right: auto;
	margin-left: auto;
	max-width: fit-content;
	min-width: fit-content;
}

.current span {
	display: block;
	font-size: 2em;
	font-weight: bold;
	margin-bottom: 0.5rem;
}

.current small {
	color: #458353;
	font-style: italic;
}

form {
	display: flex;
	margin-bottom: 2rem;
	border: 1px solid #AAA;
	border-radius: 0.5rem;
	overflow: hidden;
	transition: 200ms linear;
}

form:focus-within,
form:hover {
	border-color: #302f2c;
	border-width: 2px;
}

form input[type="number"] {
	appearance: none;
	outline: none;
	border: none;
	background-color: white;

	flex: 1 1 0%;
	padding: 1rem 1.5rem;
	font-size: 1.25rem;
}

form input[type="submit"] {
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	background-color: #e1ec42;
  max-width: fit-content;

	padding: 0.5rem 1rem;

	color: rgb(9, 9, 9);
	font-size: 1.25rem;
	font-weight: 700;
	transition: 200ms linear;
	border-left: 3px solid transparent;
}

form input[type="submit"]:hover {
	background-color: rgb(222, 241, 219);
	color: rgb(8, 8, 8);
	border-left-color: #db52b4;
}

.canvas-box {
	width: 100%;
	max-width: 20rem;
	background-color: azure;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	margin-bottom: 2rem;
	margin-left: auto;
	margin-right: auto;
}
.weight-history{
	margin-bottom: 2rem;
}
.weight-history ul {
	border: solid#FFD700;
	list-style: none;
	/* padding: 0rem 25rem; */
	/* margin: 0 25rem; */
}

.weight-history ul li {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 0.5rem;
	cursor: pointer;
	background-color: azure;
}

.weight-history ul li:nth-child(even) {
	background-color: #dfdfdf;
}

.weight-history ul li:hover {
	background-color: #f8f8f8;
}

.weight-history ul li:last-of-type {
	border-bottom: none;
}

.weight-history ul li span {
	display: block;
	font-size: 1.25rem;
	font-weight: 700;
	margin-right: 1rem;
}

.weight-history ul li small {
	color: #888;
	font-style: italic;
}


@media only screen and (max-width: 600px) {

#app{max-width: 23rem;}
input{
	max-width: 7rem;
}

}
</style>
