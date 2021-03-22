# XX Passing Data

## Props

So far, we've dealt exclusively with internal state — that is to say, the values are only accessible within a given component.

In any real application, you'll need to pass data from one component down to its children. To do that, we need to declare properties, generally shortened to 'props'. In Svelte, we do that with the `export` keyword.

```html
<!-- Quiz.svelte -->
<script>
  export let settings;
</script>

<!-- App.svelte -->
<script>
  import Quiz from './Quiz.svelte';

  let settings;
</script>

<Quiz settings={settings} />
```

If the variable and the props is the same name, you can use the following shorthand:

```html
<Quiz {settings} />

<!-- is equivalent to -->

<Quiz settings={settings} />
```

You can specify default values for props

```html
<script>
  export let settings = { difficulty: 'medium', selectedCategoryId: 21, numberOfQuestions: 10 };
</script>
```

If you have an object of properties, you can 'spread' them onto a component instead of specifying each one:

```html
<script>
  const pkg = {
    name: 'svelte',
    version: 3,
    speed: 'blazing',
    website: 'https://svelte.dev'
	};
</script>

<Info {...pkg} />
```

## Context

If you want to pass down data to all your children and grand children component, you are looking for the Context API.

Context API allows your component to set up a connection to all its child component, passing data along the way.

To set up a context, we use `setContext(key, context)`, and any child component can retrieve the context with `const context = getContext(key)`.

The context can only be set up during component initialisation, you have to call `setContext` and `getContext` during component initialisation.

```html
<script>
  import { setContext, getContext } from 'svelte';

  // NOTE: call setContext and getContext during component initialisation
  setContext('key', {});
  const context = getContext('key');

  setTimeout(() => {
    setContext('key', {});
    // Error: Function called outside component initialization

    const context = getContext('key');
    // Error: Function called outside component initialization
  })
</script>
```

## Exercise

1. Create a new `Quiz` component, and pass the settings via props

2. Create a game state context, to allow any child component to update the game state

```js
let currentGameState = 'WELCOME';
const gameState = {
  startGame() {
    currentGameState = 'QUIZ';
  },
  endGame() {
    currentGameState = 'RESULT';
  },
  resetGame() {
    currentGameState = 'WELCOME';
  }
};
```

Add a "Start" button in the `WelcomeScreen` to start the game.