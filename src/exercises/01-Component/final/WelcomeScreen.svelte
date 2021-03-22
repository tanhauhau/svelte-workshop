<script>
  let selectedCategory = 'Sports';
</script>

<div class="chosen">Chosen: {selectedCategory}</div>

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