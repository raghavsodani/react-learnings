
# 🚀 EPAM Senior Frontend Interview – JavaScript & React Internals Notes

> **Author:** Raghav Sodani  
> **Purpose:** Single-point, last‑minute revision notes for Senior Frontend (JavaScript + React) interviews (EPAM-style)

---

## 1️⃣ JavaScript Execution Model (Core Foundation)

### 🔹 JavaScript is Single-Threaded
- One call stack
- One thing executes at a time
- Async behavior is achieved via **event loop**

---

### 🔹 Call Stack
- Executes synchronous code
- Runs top → bottom
- Must become empty before async callbacks execute

---

### 🔹 Event Loop (Golden Rule)
> **Microtasks always execute before Macrotasks**

---

### 🔹 Task Queues

| Type | Examples | Priority |
|---|---|---|
| **Microtasks** | `Promise.then`, `async/await` | 🔥 Highest |
| **Macrotasks** | `setTimeout`, DOM events | Lower |

---

### 🔹 Execution Order
1. Synchronous code
2. Microtask queue (fully drained)
3. Macrotask queue (one task)
4. Repeat

---

### 🔹 Why Promise runs before setTimeout?
- Promise callbacks → Microtask queue
- setTimeout → Macrotask queue

---

## 2️⃣ Closures & Stale State

### 🔹 Closure Definition
> A closure captures variables from the render in which it was created.

---

### 🔹 Why stale state happens
- Effects capture state **snapshot**
- If deps are empty, effect never re-syncs

```js
useEffect(() => {
  setCount(count + 1); // stale closure
}, []);
```

---

### 🔹 Correct Fix (Functional Updater)
```js
setCount(prev => prev + 1);
```

> Functional updater always receives **latest state**.

---

## 3️⃣ React Rendering Internals

### 🔹 Reconciliation
- React compares **previous Fiber tree** with **new Fiber tree**
- Determines minimal UI changes
- Uses **keys** to preserve identity

---

### 🔹 Fiber (WHY it exists)
> Fiber is React’s internal data structure that enables **interruptible rendering**, prioritization, and concurrency.

---

### 🔹 Fiber Enables:
- Pause rendering
- Resume later
- Discard work
- Prioritize user interactions

---

## 4️⃣ Render Phase vs Commit Phase (CRITICAL)

### 🔹 Render Phase
- Calls component functions
- Builds new Fiber tree
- Pure & side‑effect free
- Can run multiple times
- Interruptible

> **Render ≠ DOM update**

---

### 🔹 Commit Phase
- Updates real DOM
- Runs effects
- Updates refs
- Runs exactly once
- Not interruptible

---

### 🔹 Why commit must run once?
> Because it causes real‑world effects (DOM mutations, subscriptions).

---

## 5️⃣ React Execution Order

```
State Update
↓
Render Phase
↓
DOM Mutations (Commit)
↓
useLayoutEffect
↓
Browser Paint
↓
useEffect
```

---

## 6️⃣ useEffect vs useLayoutEffect

### 🔹 useEffect
- Runs **after paint**
- Non-blocking
- Used for:
  - API calls
  - Subscriptions
  - Logging

---

### 🔹 useLayoutEffect
- Runs **after DOM update, before paint**
- Synchronous (blocks paint)
- Used for:
  - DOM measurements
  - Preventing flicker
  - Layout calculations

⚠️ Use sparingly — performance cost

---

## 7️⃣ Why Side Effects Are Forbidden in Render

> Render phase can run multiple times in Concurrent React.

Side effects in render cause:
- Duplicate API calls
- Non-deterministic behavior
- Bugs in concurrent rendering

✔ Side effects belong in **commit phase** (effects)

---

## 8️⃣ React Strict Mode (DEV Only)

### 🔹 What it does
- Double invokes render
- Mounts → cleans up → mounts effects

---

### 🔹 Why it exists
> To expose unsafe side effects and non-idempotent logic early.

---

### 🔹 Key Rule
> Strict Mode simulates concurrency to surface bugs.

---

## 9️⃣ Why setState is “Async”

### 🔹 Core Truth
> `setState` schedules an update, it does not apply it immediately.

---

### 🔹 Why React batches updates
- Performance
- Fewer renders
- Enables concurrency

---

### 🔹 Why logging state after setState shows old value
> Because the current render still holds the previous snapshot.

---

## 🔟 React & Event Loop (How They Connect)

- React **schedules work**, it doesn’t execute immediately
- Uses Scheduler + browser APIs
- Breaks rendering into chunks
- Cooperates with event loop

---

## 🔑 Senior-Level One-Liners (MEMORIZE)

- Render may repeat. Commit will not.
- State updates are queued, not immediate.
- Effects capture state via closure.
- Functional updater avoids stale closures.
- Render must be pure.
- Measure before optimizing.

---

## 🎯 30‑Second EPAM Summary

> React uses Fiber to break rendering into interruptible units of work.  
> During the render phase, React reconciles Fiber trees in a pure, interruptible way.  
> Once changes are finalized, React enters the commit phase to synchronously update the DOM and run effects.  
> React schedules this work using the JavaScript event loop to ensure performance and responsiveness.

---

✅ **You are interview‑ready.**
