# XX Store

Not all application state belongs inside your application's component hierarchy. Sometimes, you'll have values that need to be accessed by multiple unrelated components, or by a regular JavaScript module.

In Svelte, we do this with stores.

## The store contract

In Svelte, we call a variable as a store, when the variable follows **the store contract**:

1. A store **is an object,**
2. **with a `subscribe` method**
3. and **an optional `set` method**

```html
<script>
  // 1. store is an object
  const store = {
    // 2. with a subscribe method
    subscribe(fn) {},
    // 3. and an optional set method
    set(value) {},
  };
</script>
```

The `subscribe` method is **mandatory** for a store.
1. takes in 1 function as the only parameter
2. the function should be with the value of the store, whenever it's changed
3. the function should be called synchronously to set the initial value
4. returns a function to unsubscribe

```html
<script>
  const store = {
    // 1. the `subscribe` method takes in 1 function as the only parameter
    subscribe(fn) {
      // 2. calls the `fn` synchronously to set the initial value of the store
      fn(1);

      // 3. you can call `fn` at any point of time later
      // to update the value of the store
      let timeoutId = setTimeout(() => fn(42), 1000);

      // 4. you MUST return a function from the `subscribe` method
      // to unsubscribe from the store
      // NO MORE calling `fn` after that
      return function () {
        clearTimeout(timeoutId);
      };
    },
  };
</script>
```

The `set` method is optional.

1. takes in new value assigned to the store
2. notify the subscribers if the value of the store has changed

```html
<script>
  const subscribers = new Set();
  const store = {
    subscribe(fn) {
      // keep a reference of all the subscription function in a set
      subscribers.add(fn);

      return function () {
        // remove the subscription function from the set
        // so that it will no longer be referenced and called
        subscribers.delete(fn);
      };
    },
    // 1. the `set` method takes in a new value assigned to the store
    set(newValue) {
      // 2. notify the subscribers if the value of the store has changed
      subscribers.forEach(subscriber => {
        subscriber(newValue);
      });
    },
  };
</script>
```

Great thing about the "Store Contract" is that, once you have a store that follows the store contract, you can use the `$`-prefixed variable that:

- auto-subscribes to the store
- set's the value of the store by assigning a value to it

```html
<script>
  // 💡 store follows the store contract
  const store = {/* ... */}
<script>

{$store}

<!-- is equivalent to -->
<!--      👇👇👇       -->
<script>
  // 💡 store follows the store contract
  const store = {/* ... */}

  // 🔥 auto declaring the `$store` variable
  let $store;

  // 🔥 auto-subscribing to the store
  let unsubscribe = store.subscribe((value) => {
    // 🔥 which updates the value of `$store` whenever it's changed
    $value = value;
  });
  // 🔥 auto-cleanup when component is unmounted
  onDestroy(() => unsubscribe());
<script>

{$store}
```

```html
<script>
  // 💡 store follows the store contract
  const store = {/* ... */}

  $store = 42;

  $store.property = 'value';

  $store.count ++;
</script>

<!-- is equivalent to -->
<!--      👇👇👇       -->

<script>
  // 💡 store follows the store contract
  const store = {/* ... */}

  // 🔥 auto-subscribes store
  let $store;
  /* ... */

  // 🔥 assigning a value is converted to use `.set`
  $store = 42;
  store.set($store);

  // 🔥 assigning a value to a property is converted to use `.set`
  $store.property = 'value';
  store.set($store);

  // 🔥 updating a property is converted to use `.set`
  $store.count ++;
  store.set($store);
</script>
```

## Built-in methods to create a store

### writable()

- easiest way to create a store
- 1st parameter is the initial value of the store
- Provides `set` and `update` to update the store value

```html
<script>
  import { writable } from 'svelte/store';

  // 1st parameter is the initial value
  const store = writable('initial value');

  console.log($store); // 'initial value'

  // writable store provide `set` method to set a new value
  store.set('new value')
  // or
  $store = 'newer value';

  // alternatively, you could use the `update` method which takes in a function, that will be called with the current value of the store
  // what you return in the function will be the next value of the store
  store.update(value => `previous "${value}", now "xxx"`);
  console.log($store); // 'previous "newer value", now "xxx"'
</script>
```

- 2nd parameter is optional
- it is a function which takes in `set` as the only parameter
- `set` is similar as `store.set`
- the function will be called when there's a subscriber, and only call when turning from 0 to 1 subscriber

> Think of it as a setup for the subscribers
> - 0 subscriber -> nothing
> - 1 subscriber -> call the function to set up
> - 2 subscribers -> nothing
> - 3 subscribers -> nothing
> - ...

- you can return a function from the function, which will be called when turning to no subscriber

> Think of it as a cleanup for the subscribers
> - 3 subscribers -> nothing
> - 2 subscribers -> nothing
> - 1 subscriber -> nothing
> - 0 subscriber -> call the function to cleanup

```html
<script>
  import { writable } from 'svelte/store';

  const store = writable(42, (set) => {
    console.log('setup');

    return () => {
      console.log('cleanup');
    }
  });

  const unsub1 = store.subscribe(...);
  // console logs "setup"
  const unsub2 = store.subscribe(...);
  const unsub3 = store.subscribe(...);

  unsub1();
  unsub2();
  unsub3();
  // console logs "cleanup"
</script>
```

#### Example

```html
<script>
  import { writable } from 'svelte/store';
  const store = writable(0, set => {
    let value = 0;
    let intervalId = setInterval(() => {
      set(++value);
    }, 1000);

    return () => {
      clearInterval(intervalId);
    }
  });
</script>
```

- if there's no subscriber, there'll be no setInterval.
- no matter how many subscribers, there'll only be 1 setInterval at any point of time

### readable()

- similar to writable(), except it has no `set` or `update` method.
- which means the 2nd parameter is essential, or else, there's no way to update the value of the store

```html
<script>
  import { readable } from 'svelte/store';

  const store = readable(0, (set) => {...});

  store.set(1);
  // Error: store.set is undefined
</script>
```

### derived()

- derived() enable us to derive a new store out of an existing one
- 1st parameter is the store to be derived from
- 2nd parameter is a function to calculate the derived value, it will be called every time when the value of the store in the 1st parameter changes

```html
<script>
  import { writable, derived } from 'svelte/store';
  
  const count = writable(0);
  const double = derived(count, $count => $count * 2);

  console.log(`$count: ${$count}, $double: ${$double}`);
  // $count: 0, $double: 0

  count.set(3);

  console.log(`$count: ${$count}, $double: ${$double}`);
  // $count: 3, $double: 6
</script>
```

for the 2nd parameter of `derived()`,

- it's 1st parameter is the value of the store
- it's a variable name, it can be named anything, convention is to use `$`-prefix to denote value of a store
- it should return a value, as the new value of the derived store

You can derive from

- 1 store
  - passing 1 store in the 1st parameter
  - function in 2nd parameter will be called with the value of the store
- multiple stores
  - passing array of stores in the 1st parameter
  - function will be called with an array of values of the stores

```html
<script>
  import { writable, derived } from 'svelte/store';

  const num1 = writable(0);
  const num2 = writable(0);

  // derive from 1 store
  const double = derived(num1, $num1 => $num1 * 2);

  // derive from multiple stores
  const sum = derived([num1, num2] => ([$num1, $num2]) => $num1 + $num2);

  console.log(`$double: ${$double}, $sum: ${$sum}`);
  // $double: 0, $sum: 0

  $num1 = 3;
  $num2 = 5;

  console.log(`$double: ${$double}, $sum: ${$sum}`);
  // $double: 6, $sum: 8
</script>
```

You can derive the value

- synchronously
  - define only 1 parameter for the function
  - return the new value of the derived store

- asynchronously
  - define 2 parameters for the function, the value and the `set`
  - manually call `set` to set the new value of the derived store

```html
<script>
  import { writable, derived } from 'svelte/store';
  
  const count = writable(0);
  const synchronousDouble = derived(count, $count => {
    // 1 parameter
    // return value as the new value of the store
    return $count * 2
  });

  const asynchronousDouble = derived(count, ($count, set) => {
    // 2 parameters
    // manually set the new value fo the store
    set($count * 2);
  });
</script>
```

Asynchronous derived store allow you to set the derived value later on, such as

- after a timeout
- from a network request

```html
<script>
  import { writable, derived } from 'svelte/store';

  const count = writable(0);

  const delayedDouble = derived(count, ($count, set) => {
    setTimeout(() => {
      set($count * 2);
    }, 1000);
  });

  const apiDouble = derived(count, ($count, set) => {
    fetch(`/api/double/${$count}`)
      .then(response => response.text())
      .then(value => set(value));
  });
</script>
```

The key difference between synchronous and asynchronous `derived()` is the additional 2nd parameter in the function

Now, the return value of the function means differently

It becomes the cleanup function for your `derived()` function

it'll be called when the store value changes

```html
<script>
  import { writable, derived } from 'svelte/store';

  const count = writable(0);

  const delayedDouble = derived(count, ($count, set) => {
    const timeoutId = setTimeout(() => {
      set($count * 2);
    }, 1000);

    return () => {
      clearTimeout(timeoutId);
    }
  });

  const apiDouble = derived(count, ($count, set) => {
    const controller = new AbortController();
    const signal = controller.signal;

    fetch(`/api/double/${$count}`, { signal })
      .then(response => response.text())
      .then(value => set(value));

    return () => {
      controller.abort();
    }
  });
</script>
```

In summary, you could have 4 kinds of derived store

1. derive from 1 store synchronously 
2. derive from multiple stores synchronously 
3. derive from 1 store asynchronously 
4. derive from multiple stores asynchronously