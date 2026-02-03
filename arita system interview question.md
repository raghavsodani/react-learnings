
# 🧠 JavaScript Core Concepts – Interview Notes (Complete)

> These notes are **built directly from the exact questions you asked**, explained in an interview-friendly and memorable way.

---

## 1️⃣ `this` inside callbacks (`forEach` problem)

### ❓ Question
```js
const user = {
  name: 'Charlie',
  friends: ['Alice', 'Bob'],
  printFriends: function () {
    this.friends.forEach(function (friend) {
      console.log(this.name + ' knows ' + friend);
    });
  }
};

user.printFriends();
```

### ❌ Problem
- `function(friend){}` is a **regular function**
- Regular functions **lose `this`**
- Here `this !== user`

---

### ✅ Solutions

#### ✔️ Solution 1: Arrow function (BEST)
```js
this.friends.forEach(friend => {
  console.log(this.name + ' knows ' + friend);
});
```
🧠 Arrow functions **inherit `this` from parent**

---

#### ✔️ Solution 2: `bind`
```js
this.friends.forEach(function (friend) {
  console.log(this.name + ' knows ' + friend);
}.bind(this));
```

🧠 `bind(this)` → creates a new function with `this` locked

---

#### ✔️ Solution 3: `forEach` second argument
```js
this.friends.forEach(function (friend) {
  console.log(this.name + ' knows ' + friend);
}, this);
```

🧠 Internally uses `call`

---

### 📌 `call` vs `bind`
```js
fn.call(user, arg1, arg2);   // executes immediately
fn.bind(user, arg1);        // returns new function
```

---

## 2️⃣ `hasOwnProperty` vs `in` (Prototype chain)

### ❓ Question
```js
function Person(name) {
  this.name = name;
}

Person.prototype.age = 30;

const john = new Person('John');

john.hasOwnProperty('name'); // true
john.hasOwnProperty('age');  // false
'name' in john;              // true
'age' in john;               // true
```

### 🧠 Explanation

Structure:
```
john
 ├─ name (own)
 └─ age (prototype)
```

| Check | Own | Prototype |
|-----|-----|-----------|
| hasOwnProperty | ✅ | ❌ |
| in | ✅ | ✅ |

🧠 **Rule**:
- Own only → `hasOwnProperty`
- Anywhere → `in`

⚠️ Common trap:
```js
'toString' in john // true
john.hasOwnProperty('toString') // false
```

---

## 3️⃣ `async / await` + Event Loop (Output question)

### ❓ Question
```js
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}

async function async2() {
  console.log('async2');
}

console.log('script start');

setTimeout(() => {
  console.log('setTimeout');
}, 0);

async1();

new Promise((resolve) => {
  console.log('promise1');
  resolve();
}).then(() => {
  console.log('promise2');
});

console.log('script end');
```

### ✅ Correct Output
```
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

---

### 🧠 Why this order?

1. **Synchronous**
   - `script start`
   - `async1 start`
   - `async2`
   - `promise1`
   - `script end`

2. **Microtasks**
   - `async1 end` (await)
   - `promise2` (.then)

3. **Macrotasks**
   - `setTimeout`

🧠 **Rule**:
```
Sync → Microtasks → Macrotasks
```

---

## 4️⃣ `Array.prototype.flat` & Polyfill

### Native behavior
```js
arr.flat();        // depth = 1
arr.flat(2);
arr.flat(Infinity);
```

---

### ❌ Bug in common polyfill
```js
this[i].customFlat(--depth) // ❌ mutates depth
```

Why wrong?
- Depth must **not change across siblings**
- Recursion must receive a **new depth value**

---

### ✅ Correct Polyfill
```js
Array.prototype.customFlat = function (depth = 1) {
  let result = [];

  for (let el of this) {
    if (Array.isArray(el) && depth > 0) {
      result.push(...el.customFlat(depth - 1));
    } else {
      result.push(el);
    }
  }
  return result;
};
```

---

### Test cases
```js
arr3.flat(0);           // [1,2,[3,4,[5,6]]]
arr4.flat(Infinity);   // fully flattened
```

🧠 **Rule**:
> Never mutate recursion parameters

---

## 🏁 Final Interview Cheat Sheet

- Arrow functions don’t lose `this`
- `bind` locks `this`, `call` executes immediately
- `hasOwnProperty` ≠ `in`
- `async` always returns a Promise
- `await` pauses function, not JS
- Microtasks run before macrotasks
- Never mutate recursion variables

---

🚀 **These notes now EXACTLY match your questions.**
