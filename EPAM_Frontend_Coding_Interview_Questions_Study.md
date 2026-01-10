# 🚀 EPAM Frontend Coding Interview Preparation  
## JavaScript • React • DOM • SQL  
> **Study Mode | Interview-Oriented | Clean Explanations**

---

## 🧭 How to Use This File
- 📖 Read **question + intent**
- 🧠 Understand **what EPAM is testing**
- ✍️ Practice writing code on paper/editor
- 🗣️ Explain out loud (EPAM values explanation)

---

# 🔹 JavaScript — Core (Highest Priority)

---

## 1️⃣ Debounce vs Throttle
### Question
Implement **debounce** and **throttle** in JavaScript and explain the difference.

### EPAM Tests
- Closures
- `setTimeout`
- `this` & arguments
- Real-world thinking

### Key Points
- Debounce → waits for silence
- Throttle → limits frequency

---

## 2️⃣ Flatten an Array
```js
[1, [2, [3, 4]], 5] → [1, 2, 3, 4, 5]
```

### EPAM Tests
- Recursion
- Iteration
- Edge cases

---

## 3️⃣ Array → Tree Conversion
```js
[
 { id: 1, parentId: null },
 { id: 2, parentId: 1 }
]
```

### EPAM Tests
- HashMap usage
- Data modeling
- Hierarchical thinking

---

## 4️⃣ Implement `once()` Function
```js
const fn = once(() => console.log("Hi"));
fn(); // Hi
fn(); // nothing
```

### EPAM Tests
- Closures
- State preservation

---

## 5️⃣ Implement `memoize()`
```js
const memoizedFn = memoize(fn);
```

### Follow-ups
- Cache keys
- Object arguments

---

# 🔹 Strings (Very Common)

---

## 6️⃣ First Non-Repeating Character
```txt
"swiss" → w
```

### EPAM Tests
- Hash maps
- Iteration order

---

## 7️⃣ Reverse Words (Not Characters)
```txt
"I love JS" → "JS love I"
```

---

## 8️⃣ Check Anagram
```txt
listen ↔ silent
```

### Follow-up
- Without sorting

---

## 9️⃣ Longest Substring Without Repeating Characters
```txt
"abcabcbb" → 3
```

### EPAM Tests
- Sliding window
- Set / Map

---

# 🔹 Arrays & Logic

---

## 🔟 Second Largest Number
### Constraint
- Without sorting
- Single pass

---

## 1️⃣1️⃣ Move Zeros to End
```js
[0,1,0,3,12] → [1,3,12,0,0]
```

### Constraint
- In-place

---

## 1️⃣2️⃣ Remove Duplicates
```js
[1,2,2,3] → [1,2,3]
```

---

## 1️⃣3️⃣ Chunk an Array
```js
chunk([1,2,3,4], 2)
// [[1,2],[3,4]]
```

---

# 🔹 DOM / Browser (Frontend Specific)

---

## 1️⃣4️⃣ Event Delegation
### Question
Handle clicks on dynamically added list items.

### EPAM Tests
- Event bubbling
- `event.target`
- Performance

---

## 1️⃣5️⃣ Infinite Scroll (Logic)
### Question
When do you trigger API calls and how do you prevent duplicates?

### EPAM Tests
- Scroll math
- Throttle usage
- API safety

---

# 🔹 React (High Probability)

---

## 1️⃣6️⃣ Stopwatch in React
### Features
- Start
- Pause
- Reset

### EPAM Tests
- `setInterval`
- Cleanup
- State management

---

## 1️⃣7️⃣ Chessboard (8×8)
### Requirements
- `.map`
- Alternate colors

### Follow-ups
- `index % 2`
- Row / column logic

---

## 1️⃣8️⃣ Controlled vs Uncontrolled Components
### Must Explain
- Differences
- Use cases

---

## 1️⃣9️⃣ Prevent Unnecessary Re-renders
### Topics
- `React.memo`
- `useMemo`
- `useCallback`

---

# 🔹 SQL (Usually One Question)

---

## 2️⃣0️⃣ Second Highest Salary
```sql
SELECT MAX(salary)
FROM employee
WHERE salary < (SELECT MAX(salary) FROM employee);
```

### Follow-ups
- Duplicates
- `DENSE_RANK`

---

# 🧠 EPAM Interview Tips (Very Important)

- Explain before coding
- Handle edge cases
- Write readable code
- Don’t rush

---

## ✅ Final Study Checklist
- [ ] Can explain debounce vs throttle
- [ ] Can solve array → tree
- [ ] Comfortable with strings logic
- [ ] Confident in React basics
- [ ] SQL second highest salary ready

---

✨ **End of EPAM Frontend Study Notes** ✨
