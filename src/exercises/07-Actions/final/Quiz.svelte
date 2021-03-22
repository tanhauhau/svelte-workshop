<script>
  import { writable } from 'svelte/store';
  import Question from "./Question.svelte";

  export let settings = { difficulty: 'medium', selectedCategoryId: 21, numberOfQuestions: 10 };

  let questions = fetch(`https://opentdb.com/api.php?amount=${settings.numberOfQuestions}&category=${settings.selectedCategoryId}&difficulty=${settings.difficulty}&type=multiple`).then(res => res.json());

  let selectedAnswers = writable(new Array(settings.numberOfQuestions).fill(undefined));

  let currentQuestionIndex = 0;
</script>

<div class="container">

  <div>
    {JSON.stringify($selectedAnswers)}
  </div>

  {#await questions}
    Loading...
  {:then { results }}
    <Question {...results[currentQuestionIndex]} questionIndex={currentQuestionIndex} {selectedAnswers} />
    <div>
      {#if currentQuestionIndex > 0}<button on:click={() => currentQuestionIndex --}>Previous</button>{/if}
      <button on:click={() => currentQuestionIndex ++}>Next</button>
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
</style>