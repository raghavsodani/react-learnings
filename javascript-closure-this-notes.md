
# 🧠 JavaScript: Closure & `this`

> Clean, logical, interview-ready notes

---

## 📌 1. What is a Closure?

A **closure** is created when a function remembers variables from its outer (lexical) scope, even after the outer function has finished execution.

**Closure = Function + Remembered variables**

---

## 📌 2. Basic Closure Example

```js
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

- `outer()` runs once
- `count` is preserved in memory
- `inner` closes over `count`

---

## 📌 3. Each Closure Has Its Own Memory

```js
const c1 = outer();
const c2 = outer();

c1(); // 1
c1(); // 2
c2(); // 1
```

Each call creates a **new closure**.

---

## 📌 4. What Closure Remembers

### ✔ Remembers
- Variables
- Parameters
- Lexical scope

### ❌ Does NOT remember
- `this`
- Call-time context

---

## 📌 5. Understanding `this`

### Golden Rule
**`this` depends on how a function is called, not where it is written.**

---

## 📌 6. Common `this` Scenarios

```js
const obj = {
  name: "Raghav",
  sayHi() {
    console.log(this.name);
  }
};

obj.sayHi(); // Raghav
```

```js
const hi = obj.sayHi;
hi(); // undefined
```

---

## 📌 7. `this` Inside Callbacks

```js
setTimeout(function () {
  console.log(this.name); // undefined
}, 1000);
```

Reason: normal function → `this` lost.

---

## 📌 8. Arrow Functions Fix `this`

```js
setTimeout(() => {
  console.log(this.name);
}, 1000);
```

Arrow functions **inherit `this` from parent scope**.

---

## 📌 9. Closure vs `this`

| Feature | Closure | this |
|------|--------|------|
| Based on | Lexical scope | Call site |
| Remembers | Variables | ❌ |
| Changes | ❌ | ✅ |

---

## 📌 10. Interview Trap

```js
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

---

## 📌 11. `var` Closure Trap

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 3 3 3
```

Fix:

```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 0 1 2
```

---

## 📌 12. Final Interview One-Liners

- Closure remembers variables, not `this`
- `this` is decided at call time
- Arrow functions lexically bind `this`

---

## 🎯 Final Mental Model

```
Closure  → lexical variables
this     → call-site based
Arrow    → lexical this
```

---

✅ End of Notes
