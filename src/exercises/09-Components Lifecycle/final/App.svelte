<script>
	import { setContext } from 'svelte';
	import WelcomeScreen from './WelcomeScreen.svelte';
	import Quiz from './Quiz.svelte';
	import Result from './Result.svelte';
	import { writable } from 'svelte/store';

	let settings;
  let selectedAnswers;

	function onSettingsChange(event) {
		console.log('settings changed:', event.detail)
		settings = event.detail;
	}

	let currentGameState = 'WELCOME';
	setContext('gameState', {
		startGame() {
			currentGameState = 'QUIZ';
			selectedAnswers = writable(new Array(settings.numberOfQuestions).fill(undefined));
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
	<Quiz {settings} {selectedAnswers} />
{:else if currentGameState === 'RESULT'}
	<Result {selectedAnswers} />
{/if}

<style>
	h1 {
		color: darkblue;
		text-align: center;
	}
</style>