# XX Components Lifecycle

Every component has a lifecycle that starts when it is created, updates and ends when it is destroyed. There are a handful of functions that allow you to run code at key moments during that lifecycle.

## `onMount(fn)`

- You pass a callback function into `onMount`.
- The callback function runs when the component is created.
- If the callback function returns a function, the function will be called when the component is destroyed.

## `beforeUpdate(fn)`

- You pass a callback function into `beforeUpdate`.
- The callback function runs right before the DOM has been updated.

## `afterUpdate(fn)`

- You pass a callback function into `afterUpdate`.
- The callback function runs **once** right after the DOM has updated

## `onDestroy(fn)`

- You pass a callback function into `onDestroy`.
- The callback function runs when the component is destroyed.
