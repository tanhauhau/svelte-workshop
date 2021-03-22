<script>
  export let settings = { difficulty: 'medium', selectedCategoryId: 21, numberOfQuestions: 10 };

  let questions = fetch(`https://opentdb.com/api.php?amount=${settings.numberOfQuestions}&category=${settings.selectedCategoryId}&difficulty=${settings.difficulty}&type=multiple`).then(res => res.json());

  function shuffle(correct_answer, incorrect_answers) {
    const answers = incorrect_answers.map(answer => ({ answer, correct: false }));
    const position = Math.floor(Math.random() * 4);
    answers.splice(position, 0, { answer: correct_answer, correct: true });
    return answers;
  }
</script>

{#await questions}
  Loading...
{:then { results }}
  <ol>
    {#each results as {question, correct_answer, incorrect_answers}, questionIndex}
      <li>
        <b>{question}</b>
        <ul>
          {#each shuffle(correct_answer, incorrect_answers) as { answer, correct }}
            <li><button>{answer}</button></li>
          {/each}
        </ul>
      </li>
    {/each}
  </ol>
{:catch}
  Oops!
{/await}