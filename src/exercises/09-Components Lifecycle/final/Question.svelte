<script>
  import Typewriter from 'typewriter-effect/dist/core';

  export let question;
  export let correct_answer;
  export let incorrect_answers;
  export let questionIndex;

  export let selectedAnswers;

  function shuffle(correct_answer, incorrect_answers) {
    const answers = incorrect_answers.map(answer => ({ answer, correct: false }));
    const position = Math.floor(Math.random() * 4);
    answers.splice(position, 0, { answer: correct_answer, correct: true });
    return answers;
  }

  function typewriter(node, text) {
    const typewriter = new Typewriter(node, { strings: text, autoStart: true, delay: 10 });

    return {
      update(newText) {
        typewriter.deleteAll(10).typeString(newText).start();
      },
      destroy() {
        typewriter.stop();
      }
    }
  }

</script>
<b use:typewriter={question}></b>
<ul>
  {#each shuffle(correct_answer, incorrect_answers) as { answer, correct }}
    <li><button 
      on:click={() => { $selectedAnswers[questionIndex] = correct; }}
      disabled={$selectedAnswers[questionIndex] !== undefined}
      >{answer}</button>
    </li>
  {/each}
</ul>