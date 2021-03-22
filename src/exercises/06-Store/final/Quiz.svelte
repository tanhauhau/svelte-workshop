<script>
  import { writable } from 'svelte/store';
  import Question from "./Question.svelte";

  export let settings = { difficulty: 'medium', selectedCategoryId: 21, numberOfQuestions: 10 };

  let questions = fetch(`https://opentdb.com/api.php?amount=${settings.numberOfQuestions}&category=${settings.selectedCategoryId}&difficulty=${settings.difficulty}&type=multiple`).then(res => res.json());

  let selectedAnswers = writable(new Array(settings.numberOfQuestions).fill(undefined));
</script>

<div class="container">
  <div>{JSON.stringify($selectedAnswers)}</div>

  {#await questions}
    Loading...
  {:then { results }}
    <ol>
      {#each results as question, questionIndex}
        <li>
          <Question {...question} {questionIndex} {selectedAnswers} />
        </li>
      {/each}
    </ol>
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