# XX Reactivity

When you update a variable, it'll be reflected on the DOM immediately.

```html
<script>
  let count = 0;

  function handleClick() {
    count += 1;
  }
</script>

<button on:click={handleClick}>
  {count}
</button>
```

- You'll see `<button>0</button>` initially.
- As you click on the button, you'll see the button text turns into `'1'`, then `'2'`, `'3'`, ...
- You can modify any variable within the `<script>` tag directly, and its updated value will be reflected on the DOM immediately.

Ways to update a variable:
- assign a new value, eg: `count = 3`
- update the value, eg: `count++`, `count += 1`
- update a property of an object, eg: `obj.property = 1`, `obj.property++`

### Updating arrays

Svelte's reactivity is triggered by assignments and updates, meaning, if you call array methods, such as `array.push` or `array.splice`, Svelte won't know the array has updated.

There's few ways to workaround this:

1. reassign the array to itself:

```js
function addNumber() {
	numbers.push(numbers.length + 1);
	numbers = numbers;
}
```

The assignments afterwards hints Svelte that the array, `numbers` has updated.

2. using spread to create a new array:

```js
function addNumber() {
	numbers = [...numbers, numbers.length + 1];
}
```

Again, with assignment, Svelte knows that the array has updated.

## Reactive Declaration

If you have a component state that is derived from other states, for example, `fullname` derived from `firstname` and `lastname`, every time, when you are updating `firstname` or `lastname`, you would want to recompute `fullname`.

Svelte provides a simple way to express this, using **reactive declarations**:

```js
let firstname = 'tan';
let lastname = 'li hau';

$: fullname = firstname + ' ' + lastname;
```

- whenever `firstname` or `lastname` change, the assignment will be re-evaluated, and recompute `fullname`.

### Reactive Statement

The `$: ` does not limit to assignment statement, in fact, you can use it for any statements:

```js
let count = 0;

$: console.log('count:', count);
```

- whenever `count` changes, the statement `console.log('count:', count)` will be re-evaluated, and you'll see a new entry in the console log

### The dependencies of the reactive statement

The dependencies of the reactive statement are variables that, when the values of the variables change, the reactive statement will be re-evaluated.

For reactive declaration, all the variables on the right of the assignment are the dependencies of the statement.

For reactive statement, any variables are the dependencies of the statement.

Here are some examples:

- `$: fullname = firstname + ' ' + lastname;` - depends on `fistname`, `lastname`
- `$: sum = a + b + c;` - depends on `a`, `b`, `c`
- `$: result = calculate(num1, num2)` - depends on `calculate`, `num1`, `num2`
- `$: console.log('count:', count)` - depends on `console`, `count`
- `$: doSomething(a, b)` - depends on `doSomething`, `a`, `b`

### The statements are evaluated in dependency order

All the reactive declarations and reactive statements will be re-ordered in their dependency order.

For example, if you have 

1. `$: double = count * 2`, and
2. `$: console.log(double)`

The 2. statement will be evaluated after the 1. statement, irrelevant of the order of declaring them:

```html
<script>
  let count = 0;

  $: double = count * 2;
  $: console.log(double);
</script>
```

and 

```html
<script>
  let count = 0;

  $: console.log(double);
  $: double = count * 2;
</script>
```

are equivalent.

### The reactive statements are evaluated asynchronously

The statements are not evaluated immediately whenever the dependencies change, rather, they are evaluated right before Svelte updates the DOM.

```html
<script>
  let count = 0;

  $: double = count * 2;

  function increment() {
    count ++;
    console.log('double:', double);
  }
</script>

{count} * 2 = {double}
```

- when you call `increment`, `count` is incremented to `1`
- `$: double = count * 2` is not evaluated immediately, thus, you will see `double: 0` in the log
- right before Svelte updates the DOM, since `count` has changed, Svelte evaluates `$: double = count * 2`
- You see `1 * 2 = 2` on the screen.

## Exercise

1. Each category have its own category id, reactively declare a variable `selectedCategoryId` to store the id of the selected category.

```js
const categories = {
  'Sports': 21,
  'Computers': 18,
  'History': 23,
};
```

2. Use reactive statements to console out the selected category whenever the `selectedCategory` changes
