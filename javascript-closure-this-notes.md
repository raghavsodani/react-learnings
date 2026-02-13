# 🧠 JavaScript: Closure & `this`

> Clean, logical, interview-ready notes

------------------------------------------------------------------------

## 📌 1. What is a Closure?

A **closure** is created when a function remembers variables from its
outer (lexical) scope, even after the outer function has finished
execution.

**Closure = Function + Remembered variables**

------------------------------------------------------------------------

## 📌 2. Basic Closure Example

``` js
function outer() {
  let count = 0;

  return function inner() {
    count++;
    console.log(count);
  };
}

const fn = outer();
fn(); // 1
fn(); // 2
```

-   `outer()` runs once
-   `count` is preserved in memory
-   `inner` closes over `count`

------------------------------------------------------------------------

## 📌 3. Each Closure Has Its Own Memory

``` js
const c1 = outer();
const c2 = outer();

c1(); // 1
c1(); // 2
c2(); // 1
```

Each call creates a **new closure**.

------------------------------------------------------------------------

## 📌 4. What Closure Remembers

### ✔ Remembers

-   Variables
-   Parameters
-   Lexical scope

### ❌ Does NOT remember

-   `this`
-   Call-time context

------------------------------------------------------------------------

## 📌 5. Understanding `this`

### Golden Rule

**`this` depends on how a function is called, not where it is written.**

------------------------------------------------------------------------

## 📌 6. Common `this` Scenarios

``` js
const obj = {
  name: "Raghav",
  sayHi() {
    console.log(this.name);
  }
};

obj.sayHi(); // Raghav
```

``` js
const hi = obj.sayHi;
hi(); // undefined
```

------------------------------------------------------------------------

## 📌 7. `this` Inside Callbacks

``` js
setTimeout(function () {
  console.log(this.name); // undefined
}, 1000);
```

Reason: normal function → `this` lost.

------------------------------------------------------------------------

## 📌 8. Arrow Functions Fix `this`

``` js
setTimeout(() => {
  console.log(this.name);
}, 1000);
```

Arrow functions **inherit `this` from parent scope**.

------------------------------------------------------------------------

## 📌 9. Closure vs `this`

  Feature     Closure         this
  ----------- --------------- -----------
  Based on    Lexical scope   Call site
  Remembers   Variables       ❌
  Changes     ❌              ✅

------------------------------------------------------------------------

## 📌 10. Interview Trap

``` js
const obj = {
  x: 10,
  foo() {
    return function () {
      console.log(this.x);
    };
  }
};

obj.foo()(); // undefined
```

Fix with arrow function.

------------------------------------------------------------------------

## 📌 11. `var` Closure Trap

``` js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 3 3 3
```

Fix:

``` js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 0 1 2
```

------------------------------------------------------------------------

## 📌 12. Final Interview One-Liners

-   Closure remembers variables, not `this`
-   `this` is decided at call time
-   Arrow functions lexically bind `this`

------------------------------------------------------------------------

## 🎯 Final Mental Model

    Closure  → lexical variables
    this     → call-site based
    Arrow    → lexical this

------------------------------------------------------------------------

✅ End of Notes

------------------------------------------------------------------------

# 🔥 Advanced `this` Behavior (Browser + Strict Mode)

## 📌 Global Context (Browser + "use strict")

``` js
"use strict";
var name = "mmt";
console.log(this.name);
```

### ✅ Key Rules

-   In browser scripts (NOT modules), global `this` === `window`
-   `var` at global scope attaches to `window`
-   Strict mode does NOT change global `this`
-   Strict mode DOES change `this` inside regular functions

------------------------------------------------------------------------

## 📌 Object Literal Trap

``` js
var name = "mmt";

var myObject = {
  name: "myObject",
  property: this.name
};
```

### 🚨 Important

Object literals do NOT create their own `this`.

During object creation: - `this` is still global - So
`property = window.name` - Result → `"mmt"`

------------------------------------------------------------------------

## 📌 Regular Function in Strict Mode

``` js
function test() {
  return this.name;
}

test();
```

### ❌ In strict mode:

-   `this === undefined`
-   Accessing `this.name` throws TypeError

### ✅ When called as method:

``` js
obj.test();
```

-   `this === obj`

------------------------------------------------------------------------

## 📌 call / apply Behavior

``` js
test.call(window);
```

-   Explicitly sets `this`
-   Works normally

------------------------------------------------------------------------

## 📌 Arrow Function Behavior

``` js
const arrow = () => this.name;
```

### ✅ Arrow functions:

-   Do NOT have their own `this`
-   Capture lexical `this`
-   `.call()` / `.apply()` / `.bind()` do NOT change it

------------------------------------------------------------------------

## 📌 Strict Mode Summary Table

  Scenario                  `this` value
  ------------------------- ---------------------------
  Global (browser script)   `window`
  Regular function call     `undefined`
  Method call               calling object
  call/apply                explicitly provided value
  Arrow function            lexical `this`

------------------------------------------------------------------------

## 🎯 Senior Interview Mental Model

-   Object literal does NOT bind `this`
-   Regular function → call-site decides `this`
-   Strict mode → plain function call gives `undefined`
-   Arrow function → lexical `this`
-   Closure remembers variables, NOT `this`

------------------------------------------------------------------------

✅ End of Strict Mode Notes
