
# EPAM JavaScript Coding Interview Questions 🚀

This document contains **frequently asked EPAM JavaScript coding interview questions** with **clean, interview-ready solutions**.

---

## 1. First Non-Repeating Character ⭐

```js
function firstUniqueChar(str) {
  const map = {};

  for (let ch of str) {
    map[ch] = (map[ch] || 0) + 1;
  }

  for (let ch of str) {
    if (map[ch] === 1) return ch;
  }

  return null;
}
```

---

## 2. Flatten a Nested Array ⭐⭐⭐

```js
function flatten(arr) {
  let result = [];

  for (let item of arr) {
    if (Array.isArray(item)) {
      result.push(...flatten(item));
    } else {
      result.push(item);
    }
  }

  return result;
}
```

---

## 3. Remove Duplicates from Array ⭐

```js
function removeDuplicates(arr) {
  return [...new Set(arr)];
}
```

---

## 4. Reverse a String Without reverse() ⭐

```js
function reverseString(str) {
  let res = "";

  for (let i = str.length - 1; i >= 0; i--) {
    res += str[i];
  }

  return res;
}
```

---

## 5. Check Anagram ⭐⭐

```js
function isAnagram(a, b) {
  if (a.length !== b.length) return false;
  return a.split("").sort().join("") === b.split("").sort().join("");
}
```

---

## 6. Polyfill for Array.map ⭐⭐⭐

```js
Array.prototype.myMap = function (cb) {
  let result = [];

  for (let i = 0; i < this.length; i++) {
    result.push(cb(this[i], i, this));
  }

  return result;
};
```

---

## 7. Debounce Function ⭐⭐⭐

```js
function debounce(fn, delay) {
  let timer;

  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

---

## 8. Throttle Function ⭐⭐⭐

```js
function throttle(fn, limit) {
  let flag = true;

  return function (...args) {
    if (flag) {
      fn.apply(this, args);
      flag = false;

      setTimeout(() => (flag = true), limit);
    }
  };
}
```

---

## 9. Maximum Occurring Element ⭐

```js
function maxOccurring(arr) {
  const map = {};
  let max = 0, result;

  for (let num of arr) {
    map[num] = (map[num] || 0) + 1;
    if (map[num] > max) {
      max = map[num];
      result = num;
    }
  }

  return result;
}
```

---

## 10. Convert Callback to Async/Await ⭐⭐

```js
function getData() {
  return new Promise(resolve => {
    setTimeout(() => resolve("Data received"), 1000);
  });
}

async function fetchData() {
  const data = await getData();
  console.log(data);
}
```

---

## 11. Promise.all Polyfill ⭐⭐⭐

```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    let results = [];
    let completed = 0;

    promises.forEach((p, i) => {
      Promise.resolve(p)
        .then(res => {
          results[i] = res;
          completed++;
          if (completed === promises.length) resolve(results);
        })
        .catch(reject);
    });
  });
}
```

---

## 12. Event Loop Output Question ⭐⭐

```js
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);
```

**Output**
```
1
4
3
2
```

---

## 🎯 EPAM Interview Tips

- Explain **why**, not just **what**
- Write clean, readable logic
- Handle edge cases
- Prefer ES6+ syntax
- Be ready to refactor

---

**Prepared for EPAM JavaScript Interviews**  
Happy Coding 💙
