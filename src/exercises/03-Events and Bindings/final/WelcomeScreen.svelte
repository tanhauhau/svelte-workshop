<script>
	import { createEventDispatcher } from 'svelte';

	let dispatch = createEventDispatcher();

  let selectedCategory = 'Sports';
	let difficulty = 'easy';
	let numberOfQuestions = 10;

	const categories = {
		'Sports': 21,
		'Computers': 18,
		'History': 23,
	};

	$: selectedCategoryId = categories[selectedCategory];
	$: label = `${selectedCategory} (${selectedCategoryId}) - ${difficulty} - ${numberOfQuestions} question${numberOfQuestions > 1 ? 's' : ''}`;
	$: dispatch('settingsChange', {
		selectedCategory,
		selectedCategoryId,
		difficulty,
		numberOfQuestions,
	});
</script>

<div class="chosen">Chosen: { label }</div>

<div class="choices">
	<button on:click={() => { selectedCategory = 'Sports'; }}>Sports</button>
	<button on:click={() => { selectedCategory = 'Computers'; }}>Computers</button>
	<button on:click={() => { selectedCategory = 'History'; }}>History</button>
</div>

<div class="choices">
	<label>Difficulty
		<select bind:value={difficulty}>
			<option value="easy">Easy</option>
			<option value="medium">Medium</option>
			<option value="hard">Hard</option>
		</select>
	</label>
</div>

<div class="choices">
	<label>Number of questions
		<input type="number" min="5" max="15" bind:value={numberOfQuestions} />
	</label>
</div>

<style>
	.chosen {
		text-align: center;
		font-size: 22px;
	}
	.choices {
		text-align: center;
	}
</style>