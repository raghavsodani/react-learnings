
# JavaScript Polyfills – Complete Study Notes 🧠✨
*(Study Mode Edition – Learn by Understanding, not Memorizing)*

> **Author:** Raghav Sodani  
> **Purpose:** Interview‑ready mastery of JavaScript polyfills  
> **How to study:** Read → pause → explain aloud → re‑write from memory

---

## 🧭 Study Mode Rules (Follow This)
- First understand **WHY** the polyfill exists
- Then understand **HOW native JS works**
- Only then look at the code
- If you can explain it without code → you’ve learned it

---

## 1️⃣ What is a Polyfill?

A **polyfill** is:
> A fallback implementation of a native JavaScript feature.

Used when:
- Feature is missing in older environments
- Interviewers want to test **core JS fundamentals**

---

## 2️⃣ Foundation: `this` (DO NOT SKIP)

### Golden Rule 🥇
> **`this` depends on HOW a function is called, not where it is defined**

```js
fn();        // this → undefined (strict mode)
obj.fn();    // this → obj
```

🧠 **Left side of the dot decides `this`**

---

## 3️⃣ Why `call`, `apply`, `bind` Exist

They allow:
- Function reuse
- Explicit `this` binding

| Method | Executes immediately | Arguments | Returns |
|------|---------------------|----------|---------|
| call | ✅ | comma-separated | value |
| apply | ✅ | array | value |
| bind | ❌ | comma-separated | function |

---

## 4️⃣ Core Trick Used by call / apply / bind

```js
obj.temp = fn;
obj.temp();   // this === obj
delete obj.temp;
```

> **Making a function a method forces `this`**

This is the heart of all 3 polyfills.

---

## 5️⃣ Polyfill: call

### Mental Model
> Attach → Call → Delete → Return

```js
Function.prototype.myCall = function (context, ...args) {
  context = context ?? globalThis;

  // temporary property
  context.temp = this;// this === function that called myCall

  // call as a method → this === context
  const result = context.temp(...args);

  // cleanup
  delete context.temp;

  return result;
};
```

🧠 **Why Symbol?**  
Avoids key collision on the object.

---

## 6️⃣ Polyfill: apply

Only difference from `call`:
> Arguments are passed as an array

```js
Function.prototype.myApply = function (context, argsArray) {
  context = context ?? globalThis;

  context.temp = this;

  const result = context.temp(...(argsArray || []));

  delete context.temp;

  return result;
};
```

---

## 7️⃣ Polyfill: bind

### Key Insight
> `bind` does NOT execute immediately

```js
Function.prototype.myBind = function (context, ...bindArgs) {
  const originalFn = this;

  return function (...callArgs) {
    return originalFn.myCall(
      context,
      ...bindArgs,
      ...callArgs
    );
  };
};
```

🧠 **bind = call + closure**

---

## 8️⃣ Array Polyfills (MOST ASKED)

### Polyfill: map
> Transform each element → return new array

```js
Array.prototype.myMap = function (cb) {
  const res = [];
  for (let i = 0; i < this.length; i++) {
    res.push(cb(this[i], i, this));
  }
  return res;
};
```

---

### Polyfill: filter
> Keep elements where callback returns true

```js
Array.prototype.myFilter = function (cb) {
  const res = [];
  for (let i = 0; i < this.length; i++) {
    if (cb(this[i], i, this)) {
      res.push(this[i]);
    }
  }
  return res;
};
```

---

### Polyfill: reduce
> Accumulate values into ONE result

```js
Array.prototype.myReduce = function (cb, initial) {
  let acc = initial;
  let start = 0;

  if (acc === undefined) {
    acc = this[0];
    start = 1;
  }

  for (let i = start; i < this.length; i++) {
    acc = cb(acc, this[i], i, this);
  }

  return acc;
};
```

🧠 Reduce does **not** mutate array

---

## 9️⃣ Object Polyfills

### Object.create
```js
Object.myCreate = function (proto) {
  function F() {}
  F.prototype = proto;
  return new F();
};
```

---

### Object.assign
```js
Object.myAssign = function (target, ...sources) {
  sources.forEach(src => {
    for (let key in src) {
      if (src.hasOwnProperty(key)) {
        target[key] = src[key];
      }
    }
  });
  return target;
};
```

---

## 🔟 Promise Polyfills (ADVANCED – Senior Level)

### Promise.all
> Resolve when ALL promises resolve

```js
Promise.myAll = function (promises) {
  return new Promise((resolve, reject) => {
    let result = [];
    let count = 0;

    promises.forEach((p, i) => {
    // why we use Promise.resolve(p) instead of promises[i].then()
    // because it will throw error
    // Because numbers are NOT promises.
    // and Promise.resolve(x) converts ANY value into a promise.
      Promise.resolve(p).then(val => {
        result[i] = val;
        count++;
        if (count === promises.length) resolve(result);
      }).catch(reject);
    });
  });
};
```

---

### Promise.race
```js
Promise.myRace = function (promises) {
  return new Promise((resolve, reject) => {
    promises.forEach(p =>
      Promise.resolve(p).then(resolve).catch(reject)
    );
  });
};
```

---

## 1️⃣1️⃣ Interview Traps 🚨

- `bind` + `new` behaves differently
- Arrow functions ignore `this`
- `reduce` without initial value starts from index 1
- `map` always returns same length array

---

## 1️⃣2️⃣ How to Practice (IMPORTANT)

1. Write polyfill without seeing code
2. Break it intentionally
3. Debug it
4. Explain it aloud

> **If you can explain it, you own it.**

---

## ✅ Final Reminder

Polyfills are not about syntax.  
They are about:
- execution context
- closures
- prototypes
- deep JS thinking

---

🚀 Happy Studying  
