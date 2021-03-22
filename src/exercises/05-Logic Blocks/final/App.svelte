<script>
	import { setContext } from 'svelte';
	import WelcomeScreen from './WelcomeScreen.svelte';
	import Quiz from './Quiz.svelte';
	import Result from './Result.svelte';

	let settings;

	function onSettingsChange(event) {
		console.log('settings changed:', event.detail)
		settings = event.detail;
	}

	let currentGameState = 'WELCOME';
	setContext('gameState', {
		startGame() {
			currentGameState = 'QUIZ';
		},
		endGame() {
			currentGameState = 'RESULT';
		},
		resetGame() {
			currentGameState = 'WELCOME';
		}
	});
</script>

<h1>Trivia</h1>

{#if currentGameState === 'WELCOME'}
	<WelcomeScreen bind:settings />
{:else if currentGameState === 'QUIZ'}
	<Quiz {settings} />
{:else if currentGameState === 'RESULT'}
	<Result />
{/if}

<style>
	h1 {
		color: darkblue;
		text-align: center;
	}
</style>