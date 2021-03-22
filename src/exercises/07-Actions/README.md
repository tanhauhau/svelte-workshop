# XX Actions

Actions are essentially element-level lifecycle functions.

## The `use:` directive

Actions are applied to an element via the `use:` directive

```html
<div use:action></div>
```

You can pass parameter to actions:

```html
<div use:action={parameter}></div>
```

## Actions are just functions

```html
<script>
  function action(node, param) {

  }
</script>

<div use:action={"hello"}></div>
```

- You get to name the function however you want.
  - To apply the action function to your element, use the `use:` directive with the same action name
- The function takes in 2 parameters, the `node` and `param`
  - The `node` is the reference to the element the action is applied on, in this case, the `<div>` element
  - The `param` receives the parameter passed into the action, in this case, the `"hello'` string
- The action function will be called as soon as the element is inserted into the DOM.

### `update` and `destroy`

You can return an object with 2 optional methods,
- `update` which will be called when the parameter changes
- `destroy` which will be called when the element is removed from the DOM.

```html
<script>
  let value = "hello";

  function action(node, param) {
    return {
      update(newParam) {

      },
      destroy() {

      }
    };
  }
</script>

<div use:action={value}></div>
```

- when the value of `value` changes, the `update` method will be called with the new value of `value`
- when the `<div>` element is removed from the DOM, the destroy method will be called.

> **Tips:** `destroy` is a great place to clean up for your action!

## Usage

Actions are useful in a lot of ways, here are some of our favourite use cases of actions

### 1. Integrating with UI Library

Often times, UI library takes in a reference to a DOM element and enhance it.

For example, the [flatpickr](https://flatpickr.js.org/) library takes in an element and convert it into a date / time picker:

```js
import flatpickr from 'flatpickr';

const calendar = flatpickr(element, {
  enableTime: true,
  dateFormat: "Y-m-d H:i",
});
```

However, it involves more work when using it with a framework. For example, we need to remember to call `calendar.destroy()` to remove event listeners when the framework removes the element.

Action provides a nice way to do it, as the `destroy` method of the action will be called when the element is removed from the DOM:

```html
<script>
  import flatpickr from 'flatpickr';

  function flatpickrAction(node) {
    const calendar = flatpickr(node, {
      enableTime: true,
      dataeFormat: 'Y-m-d H:i',
    });

    return {
      destroy() {
        calendar.destroy();
      }
    };
  }
</script>

<button use:flatpickrAction>Select a date</button>
```

### 2. Allow reuse of event listeners

Often times, it takes a few event listeners to achieve 1 thing.

For example, it takes `'mousedown'`, `'mousemove'`, and `'mouseup'` event to achieve a pannable element.

```html
<script>
  let startDragging = false;
  let x;
  let y;

  function onMouseDown(event) {
    x = event.clientX;
    y = event.clientY;
    startDragging = true;
  }
  function onMouseMove(event) {
    if (!startDragging) return;

    const dx = event.clientX - x;
		const dy = event.clientY - y;
		x = event.clientX;
		y = event.clientY;

    // TODO: move element by dx, dy
  }

  function onMouseUp(event) {
    x = event.clientX;
		y = event.clientY;
    startDragging = false;
  }
</script>

<div
  on:mousedown={onMouseDown}
  on:mousemove={onMouseMove}
  on:mouseup={onMouseUp}
>Drag Me</div>
```

It is hard to reuse the pannable logic this way, as we have to recreate all the event listeners.

Actions make it easier to encapsulate all the event listeners to reuse them:

```html
<script>

  function pannable(element) {
    let startDragging = false;
    let x;
    let y;
  
    function onMouseDown(event) {
      x = event.clientX;
      y = event.clientY;
      startDragging = true;
    }
    function onMouseMove(event) {
      if (!startDragging) return;
  
      const dx = event.clientX - x;
      const dy = event.clientY - y;
      x = event.clientX;
      y = event.clientY;
  
      // TODO: move element by dx, dy
    }
  
    function onMouseUp(event) {
      x = event.clientX;
      y = event.clientY;
      startDragging = false;
    }

    element.addEventListener('mousedown', onMouseDown);
    element.addEventListener('mousemove', onMouseMove);
    element.addEventListener('mouseup', onMouseUp);

    return {
      destroy() {
        element.removeEventListener('mousedown', onMouseDown);
        element.removeEventListener('mousemove', onMouseMove);
        element.removeEventListener('mouseup', onMouseUp);
      }
    }
  }
</script>

<div use:pannable>Drag Me</div>
<div use:pannable>Drag Me Too</div>
<div use:pannable>Drag Me Three!</div>
```

## Exercise

1. use [typewriter-effect](https://www.npmjs.com/package/typewriter-effect) to add a typewriter effect to the questions

```js
// create a typewriter instance
const typewriter = new Typewriter(element, { strings: text, autoStart: true, delay: 10 });

// removes and type new text
typewriter.deleteAll(10).typeString(text).start();

// stops the typewriter effect
typewriter.stop();
```