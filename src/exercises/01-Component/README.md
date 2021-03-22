# 01 Component

## The Component

In Svelte, an application is composed from one or more components. A component is a reusable self-contained block of code that encapsulates HTML, CSS and JavaScript that belong together, written into a `.svelte` file.

### HTML

Here's a basic svelte component:

```html
<h1>Trivia</h1>
```

As you can see, it's just plain HTML.

### `<style>`

Just like in HTML, you can add a `<style>` tag to your component. Let's add some styles to the `h1` element:

```html
<h1>Trivia</h1>

<style>
  h1 {
		color: darkblue;
		text-align: center;
	}
</style>
```

> The style rules are **scoped to the component**. The style you apply to `<h1>` only apply to `<h1>` within this component, it will not change the style of `<h1>` elsewhere in your app.

### `<script>`

A component that just renders some static markup isn't very interesting. Let's add some data.

Let's add a `<script>` tag, and declare a `selectedCateogry` variable:

```html
<script>
  let selectedCategory = 'Sports';
</script>
```

We can refer to the variable in the markup using the curly braces `{}`

```html
<div>Chosen: { selectedCategory }</div>
```

Inside the curly braces, we can put any JavaScript we want. Try changing `selectedCategory` to `selectedCategory.toUpperCase()`

## Nested Components

It would be impractical to put your entire app in a single component. Instead, we can import components from other files and include them as though we were including elements.

Let's create a new file, `'WelcomeScreen.svelte'`.

Import the component...

```html
<script>
  import WelcomeScreen from './WelcomeScreen.svelte';
</script>
```

and add it to the markup

```html
<script>
  import WelcomeScreen from './WelcomeScreen.svelte';
</script>

<WelcomeScreen />
```

- Note that the Svelte component is the `default export` of the `.svelte` file.
- Try add a `<h1 />` inside the `WelcomeScreen` component, and take a look at its style