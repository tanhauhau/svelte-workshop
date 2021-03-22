<script>
	import WelcomeScreen from './WelcomeScreen.svelte';

	let settings;

	function onSettingsChange(event) {
		console.log('settings changed:', event.detail)
		settings = event.detail;
	}
</script>

<h1>Trivia</h1>

<WelcomeScreen on:settingsChange={onSettingsChange} />

<style>
	h1 {
		color: darkblue;
		text-align: center;
	}
</style>