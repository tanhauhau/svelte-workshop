# XX Composing Components

## Slot

Just like elements can have children...

```html
<div>
	<p>I'm a child of the div</p>
</div>
```

...so can components. Before a component can accept children, though, it needs to know where to put them. We do this with the `<slot>` element.

```html
<!-- Component.svelte -->
<div class="component">
  <slot></slot>
</div>

<!-- App.svelte -->
<script>
  import Component from './Component.svelte';
</script>

<Component>
  <div>Content</div>
</Component>
```

You'll see the following in the DOM:

```html
<div class="component">
  <div>Content</div>
</div>
```

### slots fallback

Slots can have fallback, if they are left empty.

You can specify the fallback by putting the fallback content inside the `<slot>` element

```html
<!-- Component.svelte -->
<div class="component">
  <slot>Fallback</slot>
</div>

<!-- App.svelte -->
<script>
  import Component from './Component.svelte';
</script>

<!-- with content -->
<Component>
  <div>Content</div>
</Component>

<!-- without content -->
<Component></Component>
```

You'll see the following in the DOM:

```html
<!-- with content -->
<div class="component">
  <div>Content</div>
</div>
<!-- without content -->
<div class="component">
  Fallback
</div>
```

### named slots

Slot has names. By default, unspecified slot name are named `"default"`.

You can have multiple slots by specifying different names:

```html
<!-- Component.svelte -->
<div class="container-a">
  <slot name="a"></slot>
</div>

<div class="container-b">
  <slot name="b"></slot>
</div>

<!-- App.svelte -->
<script>
  import Component from './Component.svelte';
</script>

<Component>
  <div slot="a">Content a</div>
  <div slot="b">Content b</div>
</Component>
```

You'll see the following in the DOM:

```html
<div class="component-a">
  <div>Content a</div>
</div>

<div class="component-b">
  <div>Content b</div>
</div>
```

### slot props

You can pass data from the Component into the slotted content.

```html
<!-- User.svelte -->
<script>
  export let userID;

  $: user = getUserById(userID);
</script>

<slot user={user}></slot>

<!-- App.svelte -->
<script>
  import User from './User.svelte';
</script>

<User userID="123" let:user>
  <div>{user.name}</div>
  <div>{user.email}</div>
</User>
```

- The `User.svelte` component passes the `user` into the `<slot>`.
- For the slot content to receives the `user` slot props, we add `let:user` in the `App.svelte` to signify getting `user` from the slot props

## Exercise

1. Create a `Layout.svelte` which creates a layout for the question that has 2 `<slot>`s, `<slot name="question">` and `<slot name="options">`.

2. Create different layouts for different category of questions, use `<svelte:component>` to switch between different layouts.