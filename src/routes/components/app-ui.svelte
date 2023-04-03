<!-- App GUI -->
<script>
	import { onMount } from 'svelte';
	import axios from 'axios';
	// export let data = [{}, {}];

	const endpoint_test = 'https://jsonplaceholder.typicode.com/posts';
	const endpoint_req = 'http://localhost:3000/v1/req';
	$: posts = [{}];
	$: users = [{}];
	$: states = [{}];
    $: cities = [{}];
	$: state = '';

	const res = {};

	async function test_endpoint() {
		try {
			/* default endpoint */
			res.default = await axios.get(endpoint_test);
			/*console.log(res.data);*/
			posts = res.default.data;
			users = res.default.data.map((entry) => {
				entry.id, entry.title;
			});
		} catch (error) {
			console.error(error);
		} finally {
			console.log('successful fetched data.');
		}
	}

	async function getStates() {
		try {
			/* get states */
			res.states = await axios.get(endpoint_req);
			states = res.states.data;
			console.log(states);
		} catch (error) {
			console.log('THERE IS AN ERROR.');
			console.error(error);
		} finally {
			console.log('successful fetched data.');
		}
	}

	async function getCities(state) {
		try {
			/* get states */
			res.cities = await axios.get(endpoint_req + '?state=' + state);
			cities = res.cities.data;
			console.log(cities);
		} catch (error) {
			console.log('THERE IS AN ERROR.');
			console.error(error);
		} finally {
			console.log('successful fetched data.');
		}
	}

	onMount(async function () {
		/*
            // Default fetch API
			const res = await fetch(endpoint);
			const data = await res.json();
			posts = data;
        */
		test_endpoint();
		getStates();
	});
</script>

<!-- App GUI -->
<div class="app-container">
	<!-- LEFT PANEL: CITY LIST PER SELECTED STATE -->
	<div class="panel" id="left-panel">
		<h3>State List DropDown</h3>
		<select id="data-option-list" name="">
			{#each states as s, index (index)}
				<option id="s{index + 1}" on:click={() => getCities(s.state)}>{s.state}</option>
			{/each}
		</select>
		<h3>City List</h3>
		<select size="5" id="data-option-panel">
			{#each cities as c, index (index)}
				<option id="s{index + 1}" value={c.city}>{c.city}</option>
			{/each}
		</select>
	</div>

	<!-- RIGHT PANEL: BUSINESS LIST PER SELECTED CITY -->
	<div class="panel">
		<h3>City List DropDown</h3>
		<select id="data-option-list" name="">
			{#each Array(5) as _, index (index)}
				<option id="item0{index}" value="">City 0{index}</option>
			{/each}
		</select>
		<h3>Business List</h3>
		<div class="card">Card 4</div>
	</div>
</div>

<!-- TEST DATA: first 5 items -->
<div id="test-data" class="panel">
	{#each posts.slice(0, 5) as article}
		<div>
			<p>{JSON.stringify(article)}</p>
		</div>
	{/each}
</div>

<!-- Style Sheet -->
<style>
	.app-container {
		/*text-align: center;*/
		width: 95%;
		min-height: 95%;
		display: flex;
		margin-left: 2%;
		margin-right: 2%;
		font-size: 18px;
	}

	.panel {
		border: 5px solid gray;
		width: 1000px;
		min-height: 95%;
		margin: 5px 5px;
		padding: 2% 2%;
		flex-direction: row wrap;
		border-radius: 25px;
	}

	.card {
		width: 400px;
		height: 200px;
		border: 5px dotted red;
		margin: 10px 10px;
		padding: 10px 10px;
		border-radius: 25px;
	}

	#data-option-list {
		width: 90%;
		height: 50px;
		margin-left: 2%;
		margin-right: 2%;
		margin-bottom: 5%;
		font-size: 1rm;
	}

	#data-option-panel {
		width: 90%;
		height: 60%;
		margin-left: 2%;
		margin-right: 2%;
		margin-bottom: 5%;
		font-size: 1rm;
	}

	#left-panel {
		width: unset;
		width: 40%;
	}

	select {
		border-radius: 10px;
		font-size: 1em;
	}

	#test-data {
		display: unset;
		display: inline-block;
	}
</style>
