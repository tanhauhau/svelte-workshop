<script>
  import { getContext } from 'svelte';
  import { fade, fly } from 'svelte/transition';
  import Question from "./Question.svelte";

	const gameState = getContext('gameState');

  export let settings = { difficulty: 'medium', selectedCategoryId: 21, numberOfQuestions: 10 };

  let questions = fetch(`https://opentdb.com/api.php?amount=${settings.numberOfQuestions}&category=${settings.selectedCategoryId}&difficulty=${settings.difficulty}&type=multiple`).then(res => res.json());

  export let selectedAnswers;

  let currentQuestionIndex = 0;
</script>

<div class="container">

  <div class="answers">
    {#each $selectedAnswers as selectedAnswer}
      <div class="answer">
        {#if selectedAnswer === true}
          <div transition:fly|local={{ y: 20 }}>✅</div>
        {:else if selectedAnswer === false}
          <div transition:fade|local>❌</div>
        {/if}
      </div>
    {/each}
  </div>

  {#await questions}
    Loading...
  {:then { results }}
    <Question {...results[currentQuestionIndex]} questionIndex={currentQuestionIndex} {selectedAnswers} />
    <div>
      {#if currentQuestionIndex > 0}<button on:click={() => currentQuestionIndex --}>Previous</button>{/if}
      {#if currentQuestionIndex < settings.numberOfQuestions - 1}
        <button on:click={() => currentQuestionIndex ++}>Next</button>
      {:else}
        <button on:click={() => gameState.endGame()}>End</button>
      {/if}
    </div>
  {:catch}
    Oops!
  {/await}

</div>

<style>
  .container {
    padding: 20px 40px;
    font-size: 22px;
  }
  .answers {
    display: flex;
    margin-bottom: 30px;
  }
  .answer {
    width: 50px;
    height: 50px;
    background: lightgray;
    border-radius: 50%;
    margin: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>