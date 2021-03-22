# XX Logic Blocks

HTML doesn't have a way of expressing logic, like conditionals and loops. Svelte does.

## if

To conditionally render some markup, you can wrap it in an `{#if}` block

```html
{#if condition}
  <div>some content</div>
{/if}
```

### if-else

If you wants to render some other markup, when the condition does not fulfil instead, you can use the `{:else}` block

```html
{#if condition}
  <div>some content</div>
{:else}
  <div>other content instead</div>
{/if}
```

> `{#...}` is to open a block,
> 
> `{/...}` is to close the block, and
> 
> `{:...}` is used in between to add on

### if-else-if

You can have `else if` in your logics too!

```html
{#if condition1}
  <div>some content</div>
{:else if condition2}
  <div>other content</div>
{:else}
  <div>otherwise content</div>
{/if}
```

## each

To loop over a list of data, you can use an `{#each}` block

```html
<script>
  let colors = ['red', 'green', 'blue'];
</script>

<ul>
  {#each colors as color}
    <li>{color}</div>
  {/each}
</ul>
```

You can get the current index as 2nd argument

```html
<script>
  let colors = ['red', 'green', 'blue'];
</script>

<ul>
  {#each colors as color, index}
    <li>{index + 1}: {color}</div>
  {/each}
</ul>
```

You can [destructure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) the item if you prefer:

```html
<script>
  let colors = [
    { name: 'red', hex: '#ff0000' },
    { name: 'green', hex: '#00ff00' },
    { name: 'blue', hex: '#0000ff' },
  ];
</script>

<ul>
  {#each colors as { name, hex }}
    <li>{name} ({hex})</div>
  {/each}
</ul>
```

### keyed-each

Svelte has no idea if you reorder / insert / remove an item in the list, Svelte updates the element one by one, and adds new element at the end of the element list if the list becomes longer, or removes extra elements at the end of the element list.

To let Svelte knows if the item is reordered, inserted or removed, you can add a unique identifier to each element of the `{#each}` block:

```html
<script>
  let list = [
    { id: 1, value: 'xxx' },
    { id: 2, value: 'yyy' },
    { id: 3, value: 'zzz' },
  ]
</script>
{#each list as item (item.id)}
  <div>{item.value}</div>
{/each}
```

The `(item.id)` tells Svelte how to figure out what's changed.

## await

Most web applications have to deal with asynchronous data at some point. Svelte makes it easy to await the value of [promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) directly in your markup:

```html
{#await promise}
	<p>...waiting</p>
{:then value}
	<p>{value}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

- when the promise is pending, you'll see `<p>...waiting</p>`
- as soon as the promise is resolved, `<p>...waiting</p>` will be replaced by `<p>{value}</p>` with the value being the resolved value from the promise
- if the promise is however rejected, `<p>...waiting</p>` will be replaced by `<p>{error.message}</p>` instead.

You can omit any part of the `{#await}` block:

```html
<!-- only show when waiting for promise -->
{#await promise}
	<p>...waiting</p>
{/await}

<!-- only show when promise resolved -->
{#await promise then value}
	<p>{value}</p>
{/await}

<!-- only show when promise rejected -->
{#await promise catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

## Exercise

1. Create a `Result.svelte` component. Use `{#if}` block to show the `<WelcomeScreen />`, `<Quiz />` or `<Result />` depending on the current game state.

2. Fetch the questions from the [Open Trivia Database API](https://opentdb.com/), use `{#await}` block to show a loading state

```js
const questionsPromise = fetch(`https://opentdb.com/api.php?amount=${numberOfQuestions}&category=${selectedCategoryId}&difficulty=${difficulty}&type=multiple`)
  .then(response => response.json());
```

3. Use `{#each}` to display all the questions and options.

You can use the following snippet to shuffle the correct and incorrect answers:

```js
function shuffle(correct_answer, incorrect_answers) {
  const answers = incorrect_answers.map(answer => ({ answer, correct: false }));
  const position = Math.floor(Math.random() * 4);
  answers.splice(position, 0, { answer: correct_answer, correct: true });
  return answers;
}
```

