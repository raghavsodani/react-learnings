# 🚀 Promise.all + Concurrent API Calls Notes

## Study Mode Guide with DummyJSON API Examples

**Prepared for:** Raghav Sodani  
**Topic:** `Promise.all`, concurrent API calls, workers, `while` vs `for`, and `Array.from`  
**Updated:** 03 Jun 2026

---

## 📌 What You Will Learn

- What `Promise.all` does
- Why `Promise.all` does **not** limit concurrency
- Why tasks should be functions, not already-started promises
- How a shared `index` works
- How workers limit concurrent API calls
- Why `while` is commonly used
- How to write the same logic using `for`
- Syntax and usage of `Array.from`
- How to explain this in interviews

---

# 1. Promise Basics

A **Promise** represents a value that we may receive in the future.

```js
const promise = new Promise((resolve, reject) => {
  const success = true;

  if (success) {
    resolve("Data received");
  } else {
    reject("Something went wrong");
  }
});
```

A promise has 3 states:

| State | Meaning |
|---|---|
| `pending` | Work is still going on |
| `fulfilled` | Work completed successfully |
| `rejected` | Work failed |

Consuming a promise:

```js
promise
  .then((result) => {
    console.log(result);
  })
  .catch((error) => {
    console.log(error);
  });
```

---

# 2. What is Promise.all?

`Promise.all` takes an array of promises and returns a single promise.

It resolves when **all promises resolve**.

```js
Promise.all([promise1, promise2, promise3])
  .then((results) => {
    console.log(results);
  })
  .catch((error) => {
    console.log(error);
  });
```

## Interview Definition

> `Promise.all` is a JavaScript method that takes an array of promises and returns a single promise. It resolves when all input promises resolve, and the result is an array of resolved values in the same order as the input promises. If any promise rejects, `Promise.all` rejects immediately with that error.

---

# 3. Promise.all Success Example

```js
const p1 = Promise.resolve("A");
const p2 = Promise.resolve("B");
const p3 = Promise.resolve("C");

Promise.all([p1, p2, p3]).then((result) => {
  console.log(result);
});
```

Output:

```js
["A", "B", "C"]
```

---

# 4. Promise.all Preserves Input Order

Even if promises complete in a different order, final result follows the input order.

```js
const p1 = new Promise((resolve) => {
  setTimeout(() => resolve("A"), 2000);
});

const p2 = new Promise((resolve) => {
  setTimeout(() => resolve("B"), 1000);
});

Promise.all([p1, p2]).then((result) => {
  console.log(result);
});
```

Timeline:

```txt
0 sec  -> p1 and p2 start
1 sec  -> p2 resolves with "B"
2 sec  -> p1 resolves with "A"
2 sec  -> Promise.all completes
```

Output:

```js
["A", "B"]
```

Why?

Because input order is:

```js
[p1, p2]
```

So result order is:

```txt
p1 result first
p2 result second
```

---

# 5. Promise.all Fail-Fast Behavior

If any promise rejects, `Promise.all` rejects immediately.

```js
const p1 = new Promise((resolve) => {
  setTimeout(() => resolve("A"), 3000);
});

const p2 = new Promise((resolve, reject) => {
  setTimeout(() => reject("B failed"), 1000);
});

const p3 = new Promise((resolve) => {
  setTimeout(() => resolve("C"), 2000);
});

Promise.all([p1, p2, p3])
  .then((result) => {
    console.log("Success:", result);
  })
  .catch((error) => {
    console.log("Error:", error);
  });
```

Output after 1 second:

```txt
Error: B failed
```

Important:

> `Promise.all` rejects immediately when one promise fails, but it does not automatically cancel other promises.

---

# 6. Promise.all With Normal Values

`Promise.all` can accept normal values also.

```js
Promise.all([
  Promise.resolve("A"),
  "B",
  100,
]).then((result) => {
  console.log(result);
});
```

Output:

```js
["A", "B", 100]
```

Normal values are treated like already resolved promises.

---

# 7. Promise.all Empty Array

```js
Promise.all([]).then((result) => {
  console.log(result);
});
```

Output:

```js
[]
```

`Promise.all([])` resolves immediately with an empty array.

---

# 8. Sequential vs Parallel API Calls

## Sequential

```js
const user = await fetchUser();
const orders = await fetchOrders();
const payments = await fetchPayments();
```

If each API takes 2 seconds:

```txt
Total time = 6 seconds
```

## Parallel Using Promise.all

```js
const [user, orders, payments] = await Promise.all([
  fetchUser(),
  fetchOrders(),
  fetchPayments(),
]);
```

If each API takes 2 seconds:

```txt
Total time = around 2 seconds
```

---

# 9. Important: Promise.all Does Not Limit Concurrency

This starts all APIs together:

```js
Promise.all(tasks.map((task) => task()));
```

If `tasks` has 100 items, this starts 100 API calls together.

That may cause:

- Browser/network overload
- Backend overload
- Rate-limit errors
- UI slowness
- Failed uploads/downloads

So we need **concurrency limiting**.

---

# 10. What Does “Limit Concurrent API Calls” Mean?

It means:

> Run only a fixed number of API calls at the same time.

Example:

```txt
Total API calls = 6
Limit = 2
```

Flow:

```txt
Start API 1 and API 2

When API 1 finishes -> start API 3
When API 2 finishes -> start API 4

At any time, only 2 APIs are running
```

---

# 11. Real-Life Analogy

Imagine a bank with 2 counters and 6 customers.

```txt
Counter 1 -> Customer 1
Counter 2 -> Customer 2
```

When a counter becomes free:

```txt
Counter 1 -> Customer 3
Counter 2 -> Customer 4
```

In our code:

| Real Life | Code |
|---|---|
| Customers | API tasks |
| Counters | Workers |
| Number of counters | Concurrency limit |
| Token number | Shared index |
| All counters closed | All workers finished |

---

# 12. DummyJSON API Function

We use DummyJSON user API.

```js
async function fetchUser(id) {
  console.log("Started user", id);

  const response = await fetch(`https://dummyjson.com/users/${id}`);
  const data = await response.json();

  console.log("Finished user", id);

  return {
    id: data.id,
    name: `${data.firstName} ${data.lastName}`,
    email: data.email,
  };
}
```

Usage:

```js
fetchUser(1).then(console.log);
```

---

# 13. Why Tasks Should Be Functions, Not Promises

## Wrong

```js
const promises = [
  fetchUser(1),
  fetchUser(2),
  fetchUser(3),
];
```

These API calls start immediately.

So we cannot control when they start.

## Correct

```js
const tasks = [
  () => fetchUser(1),
  () => fetchUser(2),
  () => fetchUser(3),
];
```

Now API starts only when we call:

```js
tasks[0]();
```

Important memory line:

```txt
Promise = already started work
Function returning promise = work we can start later
```

---

# 14. Create Tasks From User IDs

```js
const userIds = [1, 2, 3, 4, 5, 6];

const tasks = userIds.map((id) => {
  return () => fetchUser(id);
});
```

Now `tasks` looks like:

```js
[
  () => fetchUser(1),
  () => fetchUser(2),
  () => fetchUser(3),
  () => fetchUser(4),
  () => fetchUser(5),
  () => fetchUser(6),
]
```

No API has started yet.

---

# 15. Build the Worker Slowly

Forget concurrency for a moment.

A simple worker can run tasks one by one:

```js
async function worker() {
  await tasks[0]();
  await tasks[1]();
  await tasks[2]();
}
```

But this is hardcoded.

So we use an index:

```js
let index = 0;
```

`index` means:

```txt
Which task should run next?
```

---

# 16. Worker With Shared Index

```js
async function worker() {
  while (index < tasks.length) {
    const currentIndex = index;
    index++;

    await tasks[currentIndex]();
  }
}
```

## Why This Works

Initially:

```txt
index = 0
```

Worker picks task 0:

```js
const currentIndex = index; // 0
index++;                   // index becomes 1
await tasks[0]();
```

Next time:

```js
const currentIndex = index; // 1
index++;                   // index becomes 2
await tasks[1]();
```

---

# 17. How Multiple Workers Create Concurrency

If limit is 2, we create 2 workers:

```js
worker();
worker();
```

Both workers share the same outer `index`.

Flow:

```txt
index = 0

Worker 1 picks task 0
index becomes 1

Worker 2 picks task 1
index becomes 2

Worker 1 finishes task 0
Worker 1 picks task 2

Worker 2 finishes task 1
Worker 2 picks task 3
```

At any time, only 2 workers exist, so only 2 APIs can run.

---

# 18. Why We Increment index Before await

This is very important.

```js
const currentIndex = index;
index++;

await tasks[currentIndex]();
```

We increment first because we want to **reserve** the task before waiting.

If we waited first and incremented later, another worker might pick the same task.

Correct thinking:

```txt
Pick task
Mark it as taken
Then run API
```

---

# 19. Why We Use Promise.all With Workers

We do **not** do this:

```js
Promise.all(tasks.map((task) => task()));
```

That starts all APIs.

Instead, we do this:

```js
const workers = [worker(), worker()];

await Promise.all(workers);
```

Meaning:

```txt
Promise.all is not waiting for all API calls directly.
Promise.all is waiting for workers.
Workers control how many APIs run.
```

Memory line:

```txt
Promise.all(all tasks) = no limit
Promise.all(workers) = controlled limit
```

---

# 20. Complete while Loop Version

```js
async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index;
      index++;

      const result = await tasks[currentIndex]();
      results[currentIndex] = result;
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}
```

---

# 21. Full DummyJSON Example

```js
async function fetchUser(id) {
  console.log("Started user", id);

  const response = await fetch(`https://dummyjson.com/users/${id}`);
  const data = await response.json();

  console.log("Finished user", id);

  return {
    id: data.id,
    name: `${data.firstName} ${data.lastName}`,
    email: data.email,
  };
}

async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index;
      index++;

      const result = await tasks[currentIndex]();
      results[currentIndex] = result;
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}

const userIds = [1, 2, 3, 4, 5, 6];

const tasks = userIds.map((id) => {
  return () => fetchUser(id);
});

runWithConcurrency(tasks, 2).then((users) => {
  console.log("Final users:", users);
});
```

Expected behavior:

```txt
Started user 1
Started user 2

When one finishes, user 3 starts.
When one finishes, user 4 starts.

Maximum active API calls = 2
```

---

# 22. Why while Loop Is Commonly Used

We use:

```js
while (index < tasks.length)
```

because each worker should keep picking the next available task until no tasks are left.

The meaning is natural:

```txt
Keep working while tasks are left
```

---

# 23. Why Normal for Loop Is Wrong

This is wrong:

```js
async function worker() {
  for (let i = 0; i < tasks.length; i++) {
    await tasks[i]();
  }
}
```

If we create 2 workers:

```js
await Promise.all([worker(), worker()]);
```

Both workers have their own `i`.

So:

```txt
Worker 1 runs task 0, task 1, task 2...
Worker 2 also runs task 0, task 1, task 2...
```

Same API calls may run multiple times.

---

# 24. Correct for Loop Version

You can use a `for` loop, but it must use the shared outer `index`.

```js
async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    for (; index < tasks.length; ) {
      const currentIndex = index;
      index++;

      const result = await tasks[currentIndex]();
      results[currentIndex] = result;
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}
```

This works because all workers share the same `index`.

---

# 25. Cleaner for Loop Version

```js
async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    for (;;) {
      const currentIndex = index;
      index++;

      if (currentIndex >= tasks.length) {
        break;
      }

      const result = await tasks[currentIndex]();
      results[currentIndex] = result;
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}
```

## What does `for (;;)` mean?

```js
for (;;) {
  // infinite loop
}
```

It means keep running forever until we manually break.

We break here:

```js
if (currentIndex >= tasks.length) {
  break;
}
```

---

# 26. Error Handling Version

The basic version stops if one worker throws an error.

In interviews, this version is better because one failed API should not stop all remaining tasks.

```js
async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index;
      index++;

      try {
        const result = await tasks[currentIndex]();

        results[currentIndex] = {
          status: "fulfilled",
          value: result,
        };
      } catch (error) {
        results[currentIndex] = {
          status: "rejected",
          reason: error.message,
        };
      }
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}
```

This behaves like:

```txt
Promise.allSettled + concurrency limit
```

---

# 27. Full DummyJSON Error Handling Example

```js
async function fetchUser(id) {
  console.log("Started user", id);

  const response = await fetch(`https://dummyjson.com/users/${id}`);

  if (!response.ok) {
    throw new Error(`Failed to fetch user ${id}`);
  }

  const data = await response.json();

  console.log("Finished user", id);

  return {
    id: data.id,
    name: `${data.firstName} ${data.lastName}`,
    email: data.email,
  };
}

async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index;
      index++;

      try {
        const result = await tasks[currentIndex]();

        results[currentIndex] = {
          status: "fulfilled",
          value: result,
        };
      } catch (error) {
        results[currentIndex] = {
          status: "rejected",
          reason: error.message,
        };
      }
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}

const userIds = [1, 2, 3, 4, 5, 6];

const tasks = userIds.map((id) => {
  return () => fetchUser(id);
});

runWithConcurrency(tasks, 2).then((results) => {
  console.log("Final results:", results);
});
```

---

# 28. Array.from Syntax

`Array.from` creates a new array from:

1. An array-like object
2. An iterable object
3. A length-based object

## Basic Syntax

```js
Array.from(arrayLike, mapFunction, thisArg)
```

| Part | Meaning |
|---|---|
| `arrayLike` | Object to convert into array |
| `mapFunction` | Optional function applied to each item |
| `thisArg` | Optional value used as `this` inside map function |

---

# 29. Array.from With String

```js
Array.from("Raghav");
```

Output:

```js
["R", "a", "g", "h", "a", "v"]
```

---

# 30. Array.from With Set

```js
const set = new Set([1, 2, 3]);

const arr = Array.from(set);

console.log(arr);
```

Output:

```js
[1, 2, 3]
```

---

# 31. Array.from With length

This is the syntax we used:

```js
Array.from({ length: limit }, () => worker());
```

Example:

```js
Array.from({ length: 3 }, () => "worker");
```

Output:

```js
["worker", "worker", "worker"]
```

So:

```js
Array.from({ length: limit }, () => worker());
```

If `limit = 3`, it becomes:

```js
[
  worker(),
  worker(),
  worker()
]
```

Each `worker()` returns a promise.

---

# 32. Array.from With Index

```js
Array.from({ length: 5 }, (_, index) => index);
```

Output:

```js
[0, 1, 2, 3, 4]
```

Another example:

```js
Array.from({ length: 5 }, (_, index) => index + 1);
```

Output:

```js
[1, 2, 3, 4, 5]
```

---

# 33. Why We Use Array.from For Workers

Instead of manually writing:

```js
const workers = [worker(), worker(), worker()];
```

We write:

```js
const workers = Array.from({ length: limit }, () => worker());
```

So if:

```js
limit = 2
```

It creates:

```js
[worker(), worker()]
```

If:

```js
limit = 5
```

It creates:

```js
[worker(), worker(), worker(), worker(), worker()]
```

This makes code dynamic.

---

# 34. Complete Interview Answer

> To limit concurrent API calls, I would not start all requests directly using `Promise.all`, because that starts all API calls together. Instead, I would create an array of task functions like `() => fetchUser(id)`, so each API starts only when the function is called. Then I would create a shared `index` that points to the next pending task. Based on the concurrency limit, I create that many workers using `Array.from`. Each worker picks the current index, increments it immediately to reserve the task, awaits the API call, stores the result in the same index, and then picks the next task. Finally, I use `Promise.all` only to wait for all workers to finish. This ensures only N API calls run at the same time.

---

# 35. Key Memory Points

```txt
Promise.all(all API calls) = starts all calls together

Task function = API does not start until function is called

Shared index = next task token number

Worker = one API runner

Limit = number of workers

Promise.all(workers) = wait for all workers to finish

results[currentIndex] = preserve original order
```

---

# 36. Most Common Interview Follow-Ups

## Q1. Why not use normal Promise.all?

Because it starts all API calls together.

```js
Promise.all(tasks.map((task) => task()));
```

If tasks has 100 items, 100 APIs start together.

---

## Q2. Why tasks are functions?

Because promises start immediately.

```js
fetchUser(1)
```

starts now.

But:

```js
() => fetchUser(1)
```

starts later when called.

---

## Q3. Why shared index?

Because multiple workers need one common source of truth for the next pending task.

---

## Q4. Why increment index before await?

To reserve the task before the API starts.

---

## Q5. Why results[currentIndex]?

To preserve output order even if APIs finish in different order.

---

## Q6. Can we use for loop?

Yes, but not with local `let i = 0`.

Wrong:

```js
for (let i = 0; i < tasks.length; i++)
```

Correct:

```js
for (; index < tasks.length; )
```

or:

```js
for (;;) {
  const currentIndex = index;
  index++;

  if (currentIndex >= tasks.length) break;

  await tasks[currentIndex]();
}
```

---

# 37. Quick Practice

Given:

```js
const userIds = [1, 2, 3, 4, 5, 6];
runWithConcurrency(tasks, 3);
```

First users to start:

```txt
1, 2, 3
```

Because limit is 3.

When one finishes, next user starts:

```txt
4
```

Then:

```txt
5
6
```

---

# 38. Best Code To Remember

```js
async function runWithConcurrency(tasks, limit) {
  const results = [];
  let index = 0;

  async function worker() {
    while (index < tasks.length) {
      const currentIndex = index;
      index++;

      try {
        const result = await tasks[currentIndex]();

        results[currentIndex] = {
          status: "fulfilled",
          value: result,
        };
      } catch (error) {
        results[currentIndex] = {
          status: "rejected",
          reason: error.message,
        };
      }
    }
  }

  const workers = Array.from({ length: limit }, () => worker());

  await Promise.all(workers);

  return results;
}
```

---

# 39. One-Line Summary

> `Promise.all` runs everything together, but a concurrency limiter runs only a fixed number of workers, and each worker keeps picking the next task from a shared index.

---

---

# 40. Why We Use Promise.all(workers)

We use:

```js
await Promise.all(workers);
```

because **each worker is an async function**, and every async function returns a **Promise**.

So when we call:

```js
const workers = Array.from({ length: limit }, () => worker());
```

If:

```js
limit = 2
```

It becomes:

```js
const workers = [
  worker(),
  worker()
];
```

Each `worker()` returns a promise:

```js
[
  Promise, // worker 1 promise
  Promise  // worker 2 promise
]
```

So:

```js
await Promise.all(workers);
```

means:

```txt
Wait until all workers finish their assigned tasks.
```

---

## Important Difference

We are **not** doing this:

```js
Promise.all(tasks.map((task) => task()));
```

That would start **all API calls together**.

This is wrong when we want concurrency limit.

Instead, we do this:

```js
Promise.all(workers);
```

This starts only a limited number of workers.

Example:

```js
limit = 2
```

So only 2 workers are created:

```txt
Worker 1
Worker 2
```

Each worker keeps picking tasks one by one.

---

## Visual Example

Suppose:

```js
tasks = [user1, user2, user3, user4, user5, user6];
limit = 2;
```

Then:

```js
const workers = [worker(), worker()];
await Promise.all(workers);
```

Flow:

```txt
Worker 1 starts user1
Worker 2 starts user2

Worker 1 finishes user1
Worker 1 starts user3

Worker 2 finishes user2
Worker 2 starts user4

Worker 1 finishes user3
Worker 1 starts user5

Worker 2 finishes user4
Worker 2 starts user6

Worker 1 finishes
Worker 2 finishes

Promise.all(workers) completes
```

So `Promise.all(workers)` waits until:

```txt
Worker 1 has no more tasks
Worker 2 has no more tasks
```

---

## What Happens If We Do Not Use await Promise.all(workers)?

Suppose we write:

```js
const workers = Array.from({ length: limit }, () => worker());

return results;
```

Problem:

```txt
Workers start
But function immediately returns results
API calls may still be running
results may be empty or incomplete
```

So this is wrong.

We need:

```js
await Promise.all(workers);
return results;
```

Now it means:

```txt
Start workers
Wait for all workers to finish
Then return final results
```

---

## Simple Analogy

Think of 2 waiters serving 6 tables.

```txt
workers = waiters
tasks = tables
Promise.all(workers) = wait until all waiters finish their work
```

Without `Promise.all(workers)`, the manager returns home before waiters finish their tables.

---

## Interview Line

> I use `Promise.all(workers)` because each worker is an async function and returns a promise. `Promise.all` waits until all workers complete. The workers control the concurrency by picking tasks one by one from the shared index. So `Promise.all` is not used to start all API calls directly; it is only used to wait for the limited number of workers to finish.

---

## Key Memory Line

```txt
Promise.all(tasks) = no concurrency limit
Promise.all(workers) = wait for limited workers
```

---

# ✅ End of Notes
