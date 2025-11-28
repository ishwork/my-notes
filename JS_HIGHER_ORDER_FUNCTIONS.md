# JavaScript Built-in Higher Order Functions

A higher order function is a function that does at least one of the following:
- Takes one or more functions as arguments
- Returns a function as its result

JavaScript provides several built-in higher order functions for arrays, objects, functions, and timers. Grouping them by use makes it easier to find the right tool for the job.

---

## Table of Contents

- [Array Methods](#array-methods)
  - [forEach](#foreach)
  - [map](#map)
  - [filter](#filter)
  - [reduce](#reduce)
  - [find](#find)
  - [findIndex](#findindex)
  - [some](#some)
  - [every](#every)
  - [sort](#sort)
  - [flatMap](#flatmap)
  - [Array.from](#arrayfrom)
  - [Array.of](#arrayof)
- [Object Methods](#object-methods)
  - [Object.keys, Object.values, Object.entries](#objectkeys-objectvalues-objectentries)
- [Function Methods](#function-methods)
  - [Function.bind](#functionbind)
  - [Custom Example](#custom-example)
- [Timer Functions](#timer-functions)
  - [setTimeout / setInterval](#settimeout--setinterval)
- [Utility Functions](#utility-functions)
  - [Debound](#debounce)
  - [Throttle](#throttle)
  - [Importing from Lodash](#importing-from-lodash)

---
## Array Methods

### forEach
Iterates over each element in an array and executes a provided function.
```javascript
const numbers = [1, 2, 3];
numbers.forEach(function(num, idx) {
  console.log(`Index ${idx}: ${num}`);
});
// Output:
// Index 0: 1
// Index 1: 2
// Index 2: 3
```

### map
Creates a new array by applying a function to each element.
```javascript
const numbers = [1, 2, 3];
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6]
```

### filter
Creates a new array with elements that pass a test function.
```javascript
const numbers = [1, 2, 3, 4, 5];
const even = numbers.filter(num => num % 2 === 0);
console.log(even); // [2, 4]
```


### reduce
Reduces an array to a single value by applying a function cumulatively.

**Sum of numbers:**
```javascript
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 10
```


**Example: Summing a property in an array of objects**
```javascript
const cart = [
  { name: 'Apple', price: 2, quantity: 3 },
  { name: 'Banana', price: 1, quantity: 5 },
  { name: 'Orange', price: 3, quantity: 2 }
];
// Calculate total price
const total = cart.reduce((acc, item) => acc + item.price * item.quantity, 0);
// Optional parameters: (acc, item, index, array), and initialValue as the second argument
console.log(total); // 2*3 + 1*5 + 3*2 = 6 + 5 + 6 = 17
```

**How does `reduce` work here?**

- `acc` (the accumulator) starts at 0 (the initial value).
- On each iteration, `item` is the current object in the array.
- The function adds `item.price * item.quantity` to `acc` and returns the new value for the next iteration.

**Step by step:**
1. First iteration: acc = 0, item = { name: 'Apple', price: 2, quantity: 3 }
  - acc + item.price * item.quantity = 0 + 2*3 = 6
2. Second iteration: acc = 6, item = { name: 'Banana', price: 1, quantity: 5 }
  - acc + item.price * item.quantity = 6 + 1*5 = 11
3. Third iteration: acc = 11, item = { name: 'Orange', price: 3, quantity: 2 }
  - acc + item.price * item.quantity = 11 + 3*2 = 17
4. Done! The final result is 17.

### find
Returns the first element that satisfies a test function.
```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
];
const user = users.find(u => u.name === 'Bob');
console.log(user); // { id: 2, name: 'Bob' }
```

### findIndex
Returns the index of the first element that satisfies a test function.
```javascript
const numbers = [5, 12, 8, 130, 44];
const idx = numbers.findIndex(num => num > 10);
console.log(idx); // 1 (12 is the first > 10)
```

### some
Checks if at least one element passes a test function.
```javascript
const numbers = [1, 2, 3, 4];
const hasEven = numbers.some(num => num % 2 === 0);
console.log(hasEven); // true
```

### every
Checks if all elements pass a test function.
```javascript
const numbers = [2, 4, 6];
const allEven = numbers.every(num => num % 2 === 0);
console.log(allEven); // true
```

### sort
Sorts the elements of an array in place using a compare function.
```javascript
const numbers = [4, 2, 5, 1, 3];
numbers.sort((a, b) => a - b);
console.log(numbers); // [1, 2, 3, 4, 5]
```

### flatMap
Maps each element and flattens the result into a new array.
```javascript
const arr = [1, 2, 3];
const result = arr.flatMap(x => [x, x * 2]);
console.log(result); // [1, 2, 2, 4, 3, 6]
```

### Array.from
Creates a new array from an array-like or iterable object, optionally mapping each element.
```javascript
const str = 'hello';
const chars = Array.from(str);
console.log(chars); // ['h', 'e', 'l', 'l', 'o']

const nums = Array.from([1, 2, 3], x => x * 2);
console.log(nums); // [2, 4, 6]
```

### Array.of
Creates a new array from a variable number of arguments.
```javascript
const arr = Array.of(1, 2, 3);
console.log(arr); // [1, 2, 3]
```

---

## Object Methods

### Object.keys, Object.values, Object.entries
These methods return arrays of keys, values, or [key, value] pairs, and can be combined with array higher order functions.
```javascript
const obj = { a: 1, b: 2, c: 3 };
const keys = Object.keys(obj); // ['a', 'b', 'c']
const values = Object.values(obj); // [1, 2, 3]
const entries = Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]]

// Example: double all values
const doubled = Object.fromEntries(
  Object.entries(obj).map(([k, v]) => [k, v * 2])
);
console.log(doubled); // { a: 2, b: 4, c: 6 }
```

---

## Function Methods

### Function.bind
Creates a new function with a specific `this` value and initial arguments.
```javascript
function greet(greeting, name) {
  return `${greeting}, ${name}!`;
}
const sayHelloTo = greet.bind(null, 'Hello');
console.log(sayHelloTo('Alice')); // 'Hello, Alice!'
```

### Custom Example
```javascript
function withLogging(fn) {
  return function(...args) {
    console.log('Calling function with', args);
    return fn(...args);
  };
}

const add = (a, b) => a + b;
const loggedAdd = withLogging(add);
console.log(loggedAdd(2, 3)); // Logs: Calling function with [2, 3] then 5
```

---

## Timer Functions

### setTimeout / setInterval
Both accept a function as an argument (higher order usage).
```javascript
setTimeout(() => {
  console.log('Runs after 1 second');
}, 1000);

let count = 0;
const intervalId = setInterval(() => {
  count++;
  console.log(count);
  if (count === 3) clearInterval(intervalId);
}, 500);
```

## Utility Functions

### Debounce 

Debouncing in JavaScript is a technique used to ensure that a function is not called too frequently. It is commonly used in scenarios where events are triggered rapidly, such as typing in an input field or resizing a window. Without debouncing, functions might be executed many times in quick succession, causing performance issues or unwanted behaviour.

### What is Debouncing in JavaScript?
Debouncing can be defined as the technique that limits the number of times a function gets executed. It is useful when an event is frequently triggered in a short interval of time, like typing, scrolling, and resizing.

**Benefits of Debouncing:**
- **Limit Function Calls:** Prevents frequent function calls during rapid events.
- **Delays Execution:** Executes the function only after a specific delay, ensuring no rapid consecutive calls.
- **Prevents Overload:** Efficiently manages high-frequency triggers to prevent overloading.

---

### Example: Debounce Function
```js
// Debounce function
function debounce(func, delay) {
    let timeout;
    return function (...args) {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
            func.apply(this, args);
        }, delay);
    };
}

// Function to be debounced
function search(query) {
    console.log('Searching for:', query);
}

// Create a debounced version of the search function
const dSearch = debounce(search, 100);

// Simulate typing with multiple calls to the debounced function
dSearch('Hello');
dSearch('Hello, ');
dSearch('Hello, World!');  // Only this call will trigger after 100ms
```
**Output:**
```
Searching for: Hello, World!
```

---

### How Does Debouncing Work?
- The `debounce()` function is a higher-order function that takes a function (`func`) and a delay (`delay`) as arguments. It returns a new function that waits for the specified delay before calling the original function.
- `clearTimeout()` clears any previous timeout, so if the event is triggered repeatedly, the function call does not happen too quickly.
- `setTimeout()` sets the timeout after clearing previous timeouts.
- The `search` function is a placeholder for the function we want to debounce.

**Debouncing Workflow:**
1. The debounce function waits for a specific period to run the function; it doesn't run the function immediately.
2. If the event is triggered again before the wait time is over, the previous function call is canceled and the timer resets.
3. Once the timer completes without any further event triggers, the function is executed.
4. This ensures the function is executed only after the event stops occurring for a specific period.

---

### Throttle

Throttling in JavaScript is a technique used to limit the number of times a function is called over time. Unlike debouncing, which delays execution until events stop firing, throttling ensures a function is called at most once in a specified interval, even if events continue to occur.

### What is Throttling in JavaScript?
Throttling is useful for scenarios where we want to ensure a function executes at regular intervals, regardless of how many times an event is triggered. Common use cases include window resizing, scrolling, and mouse movement events.

**Benefits of Throttling:**
- **Limit Execution Rate:** Ensures a function is not called more than once per interval.
- **Improves Performance:** Reduces the number of executions during high-frequency events.
- **Predictable Updates:** Useful for UI updates that should happen at a steady rate.

---

### Example: Throttle Function
```js
// Throttle function
function throttle(func, interval) {
    let lastCall = 0;
    return function (...args) {
        const now = Date.now();
        if (now - lastCall >= interval) {
            lastCall = now;
            func.apply(this, args);
        }
    };
}

// Function to be throttled
function logScroll() {
    console.log('Scroll event:', Date.now());
}

// Create a throttled version of the logScroll function
const throttledScroll = throttle(logScroll, 500);

// Simulate rapid scroll events
throttledScroll(); // Will run
setTimeout(throttledScroll, 100); // Ignored
setTimeout(throttledScroll, 600); // Will run
```
**Output:**
```
Scroll event: <timestamp>
Scroll event: <timestamp>
```

---

### How Does Throttling Work?
- The `throttle()` function is a higher-order function that takes a function (`func`) and an interval (`interval`) as arguments. It returns a new function that only allows execution if the specified interval has passed since the last call.
- The throttled function checks the time since the last execution and only runs if enough time has passed.
- This is useful for limiting the rate of function calls during continuous events.

**Throttling Workflow:**
1. The throttle function allows the first call immediately.
2. Subsequent calls within the interval are ignored.
3. After the interval passes, the next call is allowed.
4. This ensures the function is executed at most once per interval, regardless of event frequency.

### Importing from Lodash

Can use ready-made debounce and throttle functions from the popular lodash library:

```js
import debounce from 'lodash/debounce';
import throttle from 'lodash/throttle';

// Usage
const debouncedFn = debounce(() => {
  console.log('Debounced!');
}, 300);

const throttledFn = throttle(() => {
  console.log('Throttled!');
}, 300);
```
---

## Summary Table

| Function/Method         | Category      | Purpose                                 |
|------------------------|--------------|-----------------------------------------|
| forEach                | Array        | Iterate over array                      |
| map                    | Array        | Transform array elements                |
| filter                 | Array        | Filter array elements                   |
| reduce                 | Array        | Accumulate array to single value        |
| find                   | Array        | Find first matching element             |
| findIndex              | Array        | Find index of first matching element    |
| some                   | Array        | Check if any element matches            |
| every                  | Array        | Check if all elements match             |
| sort                   | Array        | Sort array elements                     |
| flatMap                | Array        | Map and flatten array                   |
| Array.from             | Array        | Create array from iterable              |
| Array.of               | Array        | Create array from arguments             |
| Object.keys/values/entries | Object   | Get keys, values, or entries as arrays  |
| Function.bind          | Function     | Bind function context/args              |
| setTimeout             | Timer        | Run function after delay                |
| setInterval            | Timer        | Run function repeatedly                 |
| debounce               | Utility      | Delay execution until events stop       |
| throttle               | Utility      | Limit execution rate to interval        |
