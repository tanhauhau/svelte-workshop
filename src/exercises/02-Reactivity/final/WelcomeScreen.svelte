<script>
  let selectedCategory = 'Sports';

	const categories = {
		'Sports': 21,
		'Computers': 18,
		'History': 23,
	};

	$: selectedCategoryId = categories[selectedCategory];
	$: console.log('Selected category', selectedCategory, selectedCategoryId);
</script>

<div class="chosen">Chosen: {selectedCategory} ({selectedCategoryId})</div>

<div class="choices">
	<button on:click={() => { selectedCategory = 'Sports'; }}>Sports</button>
	<button on:click={() => { selectedCategory = 'Computers'; }}>Computers</button>
	<button on:click={() => { selectedCategory = 'History'; }}>History</button>
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