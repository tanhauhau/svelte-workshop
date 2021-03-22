<script>
  import { getContext } from 'svelte';

	const gameState = getContext('gameState');
</script>
<h1>Result</h1>

<button on:click={() => gameState.resetGame()}>Play Again</button>