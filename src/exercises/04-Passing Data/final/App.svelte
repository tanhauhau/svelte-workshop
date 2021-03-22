<script>
	import { setContext } from 'svelte';
	import WelcomeScreen from './WelcomeScreen.svelte';
	import Quiz from './Quiz.svelte';

	let settings;

	// function onSettingsChange(event) {
	// 	console.log('settings changed:', event.detail)
	// 	settings = event.detail;
	// }

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

<h1>Trivia ({currentGameState})</h1>

<WelcomeScreen bind:settings />

<Quiz {settings} />

<style>
	h1 {
		color: darkblue;
		text-align: center;
	}
</style>