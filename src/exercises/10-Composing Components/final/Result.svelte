<script>
  import { getContext } from 'svelte';
  import { fly } from 'svelte/transition';

	const gameState = getContext('gameState');
  export let selectedAnswers;
</script>

<h1>Result</h1>

<h2 transition:fly={{ y: 50 }}>{$selectedAnswers.filter(Boolean).length} / {$selectedAnswers.length}</h2>

<button on:click={() => gameState.resetGame()}>Play Again</button>