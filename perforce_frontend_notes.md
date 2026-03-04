# Perforce Frontend Interview Notes (JavaScript & React)

Author: Raghav Sodani

------------------------------------------------------------------------

# 1. JavaScript Event Loop & Promise Execution Order

## Key Rules

1.  Synchronous code executes first.
2.  Promises (`then`, `catch`) go to the **Microtask Queue**.
3.  Microtasks execute **before Macrotasks**.
4.  Microtasks execute in **FIFO order**.

### Example

``` javascript
Promise.resolve()
  .then(function a() {
    Promise.resolve().then(function d() {})
    Promise.resolve().then(function e() {})
    throw new Error('error')
  })
  .catch(function b() {})
  .then(function c() {})
```

### Execution Order

    a → d → e → b → c

Explanation:

-   `a` runs first
-   `d` and `e` are scheduled
-   error triggers `catch`
-   `b` runs
-   chain continues to `c`

------------------------------------------------------------------------

# 2. Fetch vs Promise Execution Order

``` javascript
fetch('https://google.com').then(a)
Promise.resolve().then(b)
Promise.reject().catch(c)
```

### Order

    b → c → a

Reason:

-   `Promise.resolve` and `reject` are immediate microtasks
-   `fetch` waits for network response

------------------------------------------------------------------------

# 3. Throttle vs Debounce

## Throttle

Limits function execution **once every X ms**.

Used for:

-   Scroll events
-   Resize
-   Mouse movement

### Implementation

``` javascript
function throttle(fn, interval) {
  let flag = true;

  return function (...args) {
    if (!flag) return;

    flag = false;

    setTimeout(() => {
      fn.apply(this, args)
      flag = true
    }, interval)
  }
}
```

------------------------------------------------------------------------

## Debounce

Runs function **after user stops triggering event**.

Used for:

-   Search inputs
-   API calls
-   Autocomplete

### Implementation

``` javascript
function debounce(fn, delay) {
  let timer

  return function (...args) {
    clearTimeout(timer)

    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}
```

------------------------------------------------------------------------

# 4. Why `apply()` is Used in Debounce

`apply` preserves:

-   `this` context
-   function arguments

Example:

``` javascript
fn.apply(this, args)
```

Without this, methods relying on `this` may break.

------------------------------------------------------------------------

# 5. React `useCallback` Hook

`useCallback` memoizes a function so it doesn't change on every render.

### Syntax

``` javascript
const memoizedFn = useCallback(fn, dependencies)
```

### Problem Without `useCallback`

Every render creates a **new function reference**.

``` javascript
const handleClick = () => {}
```

Child components may re-render unnecessarily.

### Fix

``` javascript
const handleClick = useCallback(() => {
  console.log("clicked")
}, [])
```

### When To Use

-   Passing functions to `React.memo` components
-   Stable function references
-   Prevent unnecessary renders

------------------------------------------------------------------------

# 6. JavaScript Memory Management

JavaScript engines manage memory automatically.

## 3 Steps

1.  Memory Allocation
2.  Memory Usage
3.  Garbage Collection

### Allocation Example

``` javascript
let name = "Raghav"
let user = { city: "Pune" }
```

Primitives → Stack\
Objects → Heap

------------------------------------------------------------------------

# 7. Garbage Collection

JavaScript uses **Mark and Sweep Algorithm**.

### Steps

1.  Mark reachable objects
2.  Remove unreachable objects

Example:

``` javascript
let obj = { name: "Raghav" }
obj = null
```

Object becomes unreachable → GC removes it.

------------------------------------------------------------------------

# 8. Memory Leaks

Common causes:

### Global variables

``` javascript
data = new Array(1000000)
```

### Timers

``` javascript
setInterval(() => {}, 1000)
```

### Closures

Large objects captured by closures.

### Detached DOM

Removed DOM nodes still referenced in JS.

------------------------------------------------------------------------

# 9. Closures

A closure is:

> A function bundled with its lexical environment.

Example:

``` javascript
function counter() {
  let count = 0

  return function () {
    count++
    console.log(count)
  }
}

const inc = counter()

inc()
inc()
```

Output:

    1
    2

Closures allow **private variables**.

------------------------------------------------------------------------

# 10. `__proto__` vs `Object.prototype`

## Object.prototype

Base prototype object inherited by all objects.

``` javascript
let obj = {}

obj.toString()
```

`toString` comes from `Object.prototype`.

------------------------------------------------------------------------

## `__proto__`

Accessor property that points to an object's internal prototype.

``` javascript
obj.__proto__ === Object.prototype
```

TRUE

------------------------------------------------------------------------

# 11. Prototype Chain

Example:

``` javascript
let arr = []
```

Chain:

    arr
    ↓
    Array.prototype
    ↓
    Object.prototype
    ↓
    null

------------------------------------------------------------------------

# 12. Prototype Interview Questions

### 1

    obj.__proto__ === Object.prototype

TRUE

### 2

    Object.__proto__ === Function.prototype

TRUE

### 3

    Function.__proto__ === Function.prototype

TRUE

Reason:

-   `Object` is a function
-   `Function` is also a function

------------------------------------------------------------------------

# 13. Complete Prototype Hierarchy

    Function
     ↓
    Function.prototype
     ↓
    Object.prototype
     ↓
    null

Objects:

    {}
     ↓
    Object.prototype
     ↓
    null

Arrays:

    []
     ↓
    Array.prototype
     ↓
    Object.prototype
     ↓
    null

------------------------------------------------------------------------

# 14. Important Interview Tips

### Event Loop

    Sync → Microtask → Macrotask

### Microtasks

-   Promise.then
-   Promise.catch
-   queueMicrotask

### Macrotasks

-   setTimeout
-   setInterval
-   DOM events

------------------------------------------------------------------------

# 15. Key Frontend Interview Topics

Must know:

-   Event Loop
-   Closures
-   Debounce / Throttle
-   Prototype Chain
-   React Rendering
-   useCallback / useMemo
-   Memory Leaks

------------------------------------------------------------------------

# End of Notes
