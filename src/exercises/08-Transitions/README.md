# XX Transitions

You can add transition for an element when it is added into / removed from the DOM.

- You do that with the `transition:` directive
- Here is a list of ways you can add or remove element from the DOM:

```html
{#if condition}
  <div transition:fade></div>
{:else}
  <div transition:fade></div>
{/if}

{#each array as item}
  <div transition:fade></div>
{:else}
  <div transition:fade></div>
{/each}

{#await promise}
  <div transition:fade></div>
{:then}
  <div transition:fade></div>
{:catch}
  <div transition:fade></div>
{/await}
```

You pass the transition next to the `transition:` directive.

- a transition is nothing but a function
- which means you can define them, reassign them as you like
- `"svelte/transition"` is where you can import built-in svelte transitions

```html
<script>
  // built-in transition
  import { fade, blur } from 'svelte/transition';

  // custom transition
  function hello() {}

  // transition is nothing but a function variable
  let custom = condition ? fade : blur;
</script>

<div transition:fade></div>
<div transition:hello></div>
<div transition:custom></div>
```

The `transition:` directive is bidirectional.

- the same transition will be applied when transition in and out
- you can use `in:` and `out:` directive to have finer control on the in and out transition

```html
<script>
  import { fade, blur } from 'svelte/transition';
</script>

<!-- fade in and fade out -->
<div transition:fade></div>

<!-- fade in and blur out -->
<div in:fade out:blur></div>

<!-- fade in only -->
<div in:fade></div>

<!-- blur out only -->
<div out:blur></div>
```

You can customise your transition by passing in parameters!

- Different transitions take in different parameters
- You can find the list of parameters available for each transitions in the docs https://svelte.dev/docs#svelte_transition

```html
<script>
  import { fade, fly } from 'svelte/transition';
</script>

<!-- delay 300ms before fade in for 1s -->
<div transition:fade={{ delay: 300, duration: 1000 }}></div>

<!-- fly in from 200px below in 2s -->
<div transition:fly={{ y: 200, duration: 2000 }}></div>
```

Transition plays whenever the element is added / removed.

## Local transitions

With a `local` modifier, the transition ONLY plays if the element is added / removed due to the nearest logical block

```html
<script>
  import { fade } from 'svelte/transition';
</script>

{#if condition1}
  <!-- toggling condition1 will hsow all the <div>, 
       but will only play transition without the local modifier -->
  {#if condition2}
    <!-- play transition when added / removed due to 
         {#if condition1} or {#if condition2} -->
    <div transition:fade></div>

    <!-- ONLY play transition when added / removed by
         {#if condition2} -->
    <div transition:fade|local></div>
  {/if}

  
  {#each list as item}
    <!-- play transition when added / removed due to 
         {#if condition1} or {#each} -->
    <div transition:fade></div>

    <!-- ONLY play transition when added / removed by
         {#each} -->
    <div transition:fade|local></div>
  {/each}
{/if}
```

## Transition events

if you want to know when intro transition or outro transition starts playing or ends, you can listen to the following events:

- `introstart` - starts transition in
- `introend` - in transition ended
- `outrostart` - starts transition out
- `outroend` - out transition ended

```html
<script>
  import { fade } from 'svelte/transition';
  let disabled = false;
</script>

<div transition:fade
  on:introstart={() => { disabled = true;  }}
  on:introend  ={() => { disabled = false; }}
  on:outrostart={() => { disabled = true;  }}
  on:outroend  ={() => { disabled = false; }}
  >
  <!-- disabling the <button /> when the <div/> is transitioning -->
  <button {disabled}>Click Me</button>
</div>
```

## Easing

the same transition can have different feel if you use a different easing to control the rate of change of the transition.

- accelerate? decelerate? or both?
- bounce or elastic?

```html
<script>
  import { slide } from 'svelte/transition';
  import { linear, cubicIn, cubicOut, bounceInOut, elasticInOut } from 'svelte/easing';
</script>

<!-- the default, no accelearate, no decelerate -->
<div transition:slide={{ easing: linear }}></div>

<!-- start slow and accelerate -->
<div transition:slide={{ easing: cubicIn }}></div>

<!-- start fast and decelerate -->
<div transition:slide={{ easing: cubicOut }}></div>

<!-- bouncy slide -->
<div transition:slide={{ easing: linear }}></div>

<!-- elastic slide, overshoot and snaps back -->
<div transition:slide={{ easing: elasticInOut }}></div>
```

The easing function is "just another" function, which we can implement a custom one ourselves

- takes in 1 parameter, t, range from 0 to 1
- return a value, range from 0 to 1

```html
<script>
  import { fly } from 'svelte/transition';

  function customEasing1(t) {
    // using Math continuous function
    return Math.abs(Math.cos(t * Math.PI));
  }

  function customEasing2(t) {
    // piecewise function using if
    if (t < 0.3) return 0;
    if (t < 0.6) return 0.5;
    return 1;
  }
</script>

<div transition:fly={{ easing: customEasing1 }}></div>
<div transition:fly={{ easing: customEasing2 }}></div>
```

## Custom Transitions

You can implement your own transition too

```html
<script>
  function customTransition(node, param) {
    return {
      delay: param?.delay ?? 0,
      duration: param?.duration ?? 1000,
      easing: param?.easing,
      css: (t, u) => {
        return '';
      },
      tick: (t, u) => {

      }
    };
  }
</script>

<div transition:customTransition></div>

<div transition:customTransition={{ duration: 2000 }}></div>
```

- you can define your own transition function, and use it with the `transition:` directive
- the transition function takes in 2 parameters
  - the node the transition is applied to
  - parameter passed to the transition
    - from the example, the 1st `customTransition` receives `undefined` for the param, whereas the 2nd `customTransition` receives `{ duration: 2000 }`
- the transition function returns an object with all the properties optional:
  - `delay` - delay before playing the transition in millisecond
  - `duration` - amount of time taken to play the transition in millisecond
  - `easing` - the easing function
  - `css` - function that returns a css string to be applied to the element
  - `tick` - function to be called on every frame during the transition

The `css` and `tick` function takes in 2 parameter, `t` and `u`.

- the 1st parameter goes from 0 to 1 when the element transition in, and goes from 1 to 0 when the element transition out
- the 2nd parameter goes from 1 to 0 when the element transition in, and goes from 0 to 1 when the element transition out

## Exercise

1. Add transition to show whether the selected answers are correct or incorrect.

2. Add transition to show the result screen