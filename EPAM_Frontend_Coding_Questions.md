
# 🚀 EPAM Senior Frontend – Coding Questions Reference (JavaScript)

> **Author:** Raghav Sodani  
> **Purpose:** Single, last‑minute reference for all EPAM‑style coding questions practiced  
> **Focus:** Clean logic, senior‑level explanations, interview‑ready code

---

## 1️⃣ `once(fn)` – Execute Function Only Once

### 🧠 Problem
Create a function that ensures another function runs **only once**, no matter how many times it’s called.

### ✅ Requirements
- Preserve `this`
- Accept any number of arguments
- Cache and return the same result

### 💡 Solution
```js
function once(fn) {
  let called = false;
  let result;

  return function (...args) {
    if (!called) {
      called = true;
      result = fn.apply(this, args);
    }
    return result;
  };
}
```

### 🧪 Example
```js
const init = once(() => {
  console.log("Initialized");
  return 42;
});

init(); // logs, returns 42
init(); // returns 42
```

### 🔑 Interview One‑Liner
> “I use closure to persist state and `apply` to preserve context.”

---

## 2️⃣ `Promise.all` Polyfill

### 🧠 Problem
Implement a custom version of `Promise.all`.

### 💡 Solution
```js
function promiseAll(iterable) {
  return new Promise((resolve, reject) => {
    if (iterable.length === 0) {
      resolve([]);
      return;
    }

    const results = [];
    let completedCount = 0;

    for (let i = 0; i < iterable.length; i++) {
      Promise.resolve(iterable[i])
        .then(value => {
          results[i] = value;
          completedCount++;

          if (completedCount === iterable.length) {
            resolve(results);
          }
        })
        .catch(error => reject(error));
    }
  });
}
```

### 🔑 Interview One‑Liner
> “Promise.all resolves by index, not by completion time.”

---

## 3️⃣ Flatten Nested Object

### 🧠 Problem
Flatten a nested object using dot notation.

### 💡 Solution
```js
function flattenObject(obj, parentKey = "") {
  let result = {};

  for (let key in obj) {
    const value = obj[key];
    const newKey = parentKey ? `${parentKey}.${key}` : key;

    if (typeof value === "object" && value !== null && !Array.isArray(value)) {
      result = { ...result, ...flattenObject(value, newKey) };
    } else {
      result[newKey] = value;
    }
  }

  return result;
}
```

### 🔑 Interview One‑Liner
> “Flattening is recursion with path accumulation.”

---

## 4️⃣ Deep Clone Object

### 🧠 Problem
Create a deep clone so no references are shared.

### 💡 Solution
```js
function deepClone(value) {
  if (typeof value !== "object" || value === null) {
    return value;
  }

  if (value instanceof Map) {
    const mapCopy = new Map();
    for (let [k, v] of value) {
      mapCopy.set(deepClone(k), deepClone(v));
    }
    return mapCopy;
  }

  if (Array.isArray(value)) {
    return value.map(item => deepClone(item));
  }

  const objCopy = {};
  for (let key in value) {
    objCopy[key] = deepClone(value[key]);
  }
  return objCopy;
}
```

### 🔑 Interview One‑Liner
> “Data is cloned, behavior is shared.”

---

## 🏁 Final Notes
- Explain approach before coding
- Start simple, then improve
- Mention trade‑offs honestly

Good luck 🚀
