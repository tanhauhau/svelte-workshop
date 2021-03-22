# XX Events and Bindings

## DOM Events

You can liten to any event on an element using the `on:` directive

```html
<script>
  function handleMouseMove(event) {

  }
</script>

<div on:mousemove={handleMouseMove}>
</div>

<!-- inline event handlers -->
<div on:mousemove={(event) => { /* ... */ }}>
</div>
```

### Event modifiers

You can modify the event handlers

```html
<script>
  function handleClick() {
    console.log('clicked!');
  }
</script>
<button on:click|once={handleClick}>
  Click Me
</button>
```

The full list of modifiers:

- `preventDefault` — calls `event.preventDefault()` before running the handler.
- `stopPropagation` — calls `event.stopPropagation()`, preventing the event reaching the next element
- `passive` — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
- `nonpassive` — explicitly set `passive: false`
- `capture` — fires the handler [during the capture phase instead of the bubbling phase](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#event_bubbling_and_capture)
- `once` — remove the handler after the first time it runs
- `self` — only trigger handler if `event.target` is the element itself

You can chain modifiers together, e.g. `on:click|once|capture={...}`.

## Component Events

Component can dispatch events too. You can create an event dispatcher to dispatch event from the component.

```html
<!-- Child.svelte -->
<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  function doSomething() {
    dispatch('message', {
      text: 'hello!'
    });
  }
</script>

<!-- Parent.svelte -->
<script>
  import Child from './Child.svelte';

  function handleMessage(event) {
    console.log(event.detail.text);
  }
</script>

<Child on:message={handleMessage} />
```

- `createEventDispatcher` must be called when the component is instantiated.
  - You can't do it later, such as inside a `setTimeout` callback
- You can dispatch event of any name, eg: `dispatch("any_event")`
  - to listen to it, you need to listen to the same event name, `<Child on:any_event={handleMessage} />`
- You can attach data to the event you are dispatching, `dispatch("any_event", data)`
  - you can access the data in the event handler via `event.detail`
- You can still dispatch event even though no one is listening to it.

### Forwarding event

If you have nested Component hierarchy, you are not able to listen to an event dispatched from a deeply nested component.

```html
<!-- GrandChild.svelte -->
<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  function doSomething() {
    dispatch('message', { text: 'hello' });
  }
</script>

<!-- Child.svelte -->
<script>
  import GrandChild from './GrandChild.svelte';
</script>

<GrandChild />

<!-- Parent.svelte -->
<script>
  import Child from './Child.svelte';

  function handleMessage(event) {
    console.log('message:', event.detail.text);
  }
</script>

<Child on:message={handleMessage} />
```

In the example above, when you call `doSomething` in `GrandChild.svelte`, the `message` event dispatched can't be captured in the `Parent.svelte`.

To fix this, you have to forward the event in all the intermediate components.

In this case, we have to forward the event in `Child.svelte`:

```html
<!-- Child.svelte -->
<script>
	import GrandChild from './GrandChild.svelte';
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function forward(event) {
    // forward GrandChild event up to parent
		dispatch('message', event.detail);
	}
</script>

<GrandChild on:message={forward}/>
```

Now, when you call `doSomething` in `GrandChild.svelte`,
- the `message` event dispatched, and handled by the `forward` event handler in `Child.svelte`
- `forward` calls `dispatch` to `dispatch` the `message` event with the `event.detail` received
- the event then receives and handled by the `handleMessage` in `Parent.svelte`

Adding a forward event handler can be a lot of code to write, so instead, the above code can be re-written with a shorthand, `on:message`:

```html
<script>
	import GrandChild from './GrandChild.svelte';
</script>

<GrandChild on:message/>
```

- this will listens to all `message` event dispatched from `GrandChild` and re-dispatch them to `Parent`

### Exercise

- [Exercise 1](#exercise)
- [Exercise 2](#exercise)
- [Exercise 3](#exercise)

## Binding

Most of the DOM element attributes can be bind to a variable

```html
<script>
  let text = '';
</script>

<!-- bind value of the input to variable `text` -->
<input bind:value={text} />
```

such that:
- if the value of the variable `text` changes, the value of the input is updated to the value of `text`
- if the value of the input is changed, by user input, the value of the variable is updated to the value of the input.

### Component bindings

The prop of a component can be bind to a variable too:

```html
<!-- Child.svelte -->
<script>
  export let data;
</script>

<!-- Parent.svelte -->
<script>
  import Child from './Child.svelte';

  let text;
</script>

<Child bind:data={text} />
```

### bind:this

If you want to get the reference of the DOM element, you can use `bind:this`:

```html
<script>
  import { onMount } from 'svelte';
  let divElement;

  onMount(() => {
    console.log(divElement);
  });
</script>

<div bind:this={divElement}></div>
```

### Exercise

- [Exercise 4](#exercise)
- [Exercise 5](#exercise)
- [Exercise 6](#exercise)

## Exercise

1. Add an input to allow user input how many quiz questions for the trivia. Use an event listener to listen to the changes to the input and store the value of the input to the variable `numberOfQuestions`.

```html
<label>
  Number of questions
  <input type="number" min="5" max="15" />
</label>
```

2. Add a dropdown `<select>` to allow user to select the difficulty for the trivia. Store the value to the variable `difficulty`

```html
<label>
  Difficulty
  <select>
    <option value="easy">Easy</option>
    <option value="medium">Medium</option>
    <option value="hard">Hard</option>
  </select>
</label>
```

3. Use component events to listen to quiz settings changes from `WelcomeScreen.svelte` in `App.svelte`. Store the settings in a variable named `settings`

```html
<!-- App.svelte -->

<WelcomeScreen on:settingsChange={onSettingsChange} />
```

4. Redo **Exercise 1**, this time instead of using event listener, use `bind:` to update the variable `numberOfQuestions`

5. Redo **Exercise 2**, this time instead of using event listener, use `bind:` to update the variable `difficulty`

6. _(Later)_ Redo **Exercise 3**, this time instead of using event listener, use `bind:` to update the variable `settings`