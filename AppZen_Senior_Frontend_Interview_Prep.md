# AppZen Senior Frontend Engineer Interview Prep

> Focus areas: **JavaScript Core**, **Promises**, **Callbacks**, **Async/Await**, **Fetch API**, **React Table Coding**, **Currying**, **URL Shortener Design**, **Load Balancing**, **Authentication Flow**, and **CDNs**.

---

## Table of Contents

1. [Interview Context](#1-interview-context)
2. [Most Likely Interview Areas](#2-most-likely-interview-areas)
3. [JavaScript Core Questions](#3-javascript-core-questions)
4. [Promise, Callback, Async/Await](#4-promise-callback-asyncawait)
5. [Fetch API](#5-fetch-api)
6. [React Coding: Fetch Data and Render Table](#6-react-coding-fetch-data-and-render-table)
7. [Promise-Based React Question](#7-promise-based-react-question)
8. [Advanced Currying for Sum](#8-advanced-currying-for-sum)
9. [URL Shortener System Design](#9-url-shortener-system-design)
10. [Load Balancing](#10-load-balancing)
11. [User Authentication Flow](#11-user-authentication-flow)
12. [CDN Explanation](#12-cdn-explanation)
13. [React Questions for Senior Frontend](#13-react-questions-for-senior-frontend)
14. [Promise Programs to Practice](#14-promise-programs-to-practice)
15. [Questions to Ask Interviewer](#15-questions-to-ask-interviewer)
16. [2-Day Preparation Plan](#16-2-day-preparation-plan)
17. [Very Likely Direct Questions](#17-very-likely-direct-questions)

---

# 1. Interview Context

For AppZen Senior Frontend Engineer, expect questions around:

- Data-heavy enterprise UI
- Tables and filters
- API integration
- React performance
- JavaScript fundamentals
- Promise-based programming
- System design basics
- Authentication/security
- Scalable frontend architecture

AppZen's domain is finance automation, so examples may be based on:

- Invoices
- Expenses
- Vendors
- Audit status
- Risk score
- Approval workflows
- Finance dashboards

Use these domain examples when explaining your solutions.

---

# 2. Most Likely Interview Areas

| Area | What to Prepare |
|---|---|
| JavaScript Core | Closures, hoisting, event loop, scope, prototypes |
| Promises | Promise chaining, Promise.all, error handling |
| Async/Await | Sequential vs parallel calls, try/catch |
| Fetch API | GET/POST, headers, response.ok, AbortController |
| React | useState, useEffect, useMemo, table rendering |
| React Performance | Memoization, virtualization, debouncing |
| Coding | Table with fetch, filter, sort, pagination |
| JS Programs | Currying, debounce, Promise.all polyfill |
| System Design | URL shortener, load balancer, CDN |
| Security | Authentication flow, token refresh, cookies |

---

# 3. JavaScript Core Questions

## Q1. What is closure?

A closure is when a function remembers variables from its outer lexical scope even after the outer function has finished execution.

```js
function outer() {
  let count = 0;

  return function inner() {
    count++;
    return count;
  };
}

const counter = outer();

console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

### Interview Explanation

> Closure allows a function to remember variables from its parent scope. It is useful for data privacy, currying, memoization, and function factories.

---

## Q2. What is hoisting?

Hoisting means JavaScript moves declarations to the top of their scope during compilation.

```js
console.log(a); // undefined
var a = 10;
```

But with `let` and `const`:

```js
console.log(b); // ReferenceError
let b = 20;
```

### Key Point

| Keyword | Hoisted? | Initial Value |
|---|---|---|
| var | Yes | undefined |
| let | Yes | Temporal Dead Zone |
| const | Yes | Temporal Dead Zone |
| function declaration | Yes | Full function |

---

## Q3. Event loop output

```js
console.log("A");

setTimeout(() => console.log("B"), 0);

Promise.resolve().then(() => console.log("C"));

console.log("D");
```

### Output

```txt
A
D
C
B
```

### Why?

1. Synchronous code runs first.
2. Promise callbacks go to the microtask queue.
3. `setTimeout` goes to the macrotask queue.
4. Microtasks run before macrotasks.

---

## Q4. Difference between `var`, `let`, and `const`

| Feature | var | let | const |
|---|---|---|---|
| Scope | Function | Block | Block |
| Re-declare | Yes | No | No |
| Re-assign | Yes | Yes | No |
| Hoisted | Yes | Yes | Yes |
| Temporal Dead Zone | No | Yes | Yes |

---

## Q5. Difference between `==` and `===`

`==` compares after type conversion.

```js
console.log(5 == "5"); // true
```

`===` compares value and type.

```js
console.log(5 === "5"); // false
```

### Interview Line

> Prefer `===` because it avoids unexpected type coercion.

---

# 4. Promise, Callback, Async/Await

## Callback

```js
function getUser(id, callback) {
  setTimeout(() => {
    callback({ id, name: "Raghav" });
  }, 1000);
}

getUser(1, function(user) {
  console.log(user);
});
```

### Problem

Callbacks can lead to nested code and poor error handling.

---

## Promise

```js
function getUser(id) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ id, name: "Raghav" });
    }, 1000);
  });
}

getUser(1)
  .then((user) => console.log(user))
  .catch((error) => console.error(error));
```

---

## Async/Await

```js
async function loadUser() {
  try {
    const user = await getUser(1);
    console.log(user);
  } catch (error) {
    console.error(error);
  }
}

loadUser();
```

### Interview Line

> Async/await is syntactic sugar over promises. It makes asynchronous code look synchronous and easier to read.

---

## Sequential vs Parallel Calls

### Sequential

```js
const user = await fetchUser();
const orders = await fetchOrders();
```

Second request waits for first request.

### Parallel

```js
const [user, orders] = await Promise.all([
  fetchUser(),
  fetchOrders(),
]);
```

Both requests start together.

### Interview Line

> If API calls are independent, I prefer `Promise.all` for better performance.

---

# 5. Fetch API

## Important Point

`fetch` does not reject automatically for HTTP errors like 404 or 500. It rejects mainly for network failures. So we should check `response.ok`.

```js
async function fetchInvoices() {
  try {
    const response = await fetch("/api/invoices");

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Failed to fetch invoices", error);
    throw error;
  }
}
```

---

## POST Request

```js
async function createInvoice(invoice) {
  const response = await fetch("/api/invoices", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: "Bearer token_here",
    },
    body: JSON.stringify(invoice),
  });

  if (!response.ok) {
    throw new Error("Failed to create invoice");
  }

  return response.json();
}
```

---

## Abort API Request

```js
const controller = new AbortController();

fetch("/api/invoices", {
  signal: controller.signal,
});

controller.abort();
```

Useful in React cleanup when the component unmounts.

---

# 6. React Coding: Fetch Data and Render Table

## Requirement

Build a React component that:

1. Fetches data from API.
2. Shows loading state.
3. Shows error state.
4. Renders data in a table.
5. Filters by search text.
6. Filters by status.
7. Uses best practices.

---

## Complete React Solution

```jsx
import { useEffect, useMemo, useState } from "react";

const API_URL = "https://jsonplaceholder.typicode.com/users";

export default function InvoiceTable() {
  const [rows, setRows] = useState([]);
  const [searchText, setSearchText] = useState("");
  const [statusFilter, setStatusFilter] = useState("ALL");

  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  useEffect(() => {
    const controller = new AbortController();

    async function fetchData() {
      try {
        setLoading(true);
        setError("");

        const response = await fetch(API_URL, {
          signal: controller.signal,
        });

        if (!response.ok) {
          throw new Error(`Failed with status ${response.status}`);
        }

        const data = await response.json();

        const formattedData = data.map((item, index) => ({
          id: item.id,
          vendor: item.company.name,
          email: item.email,
          amount: (index + 1) * 1000,
          status: index % 2 === 0 ? "Approved" : "Pending",
          riskScore: index * 10,
        }));

        setRows(formattedData);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message || "Something went wrong");
        }
      } finally {
        setLoading(false);
      }
    }

    fetchData();

    return () => {
      controller.abort();
    };
  }, []);

  const filteredRows = useMemo(() => {
    return rows.filter((row) => {
      const matchesSearch =
        row.vendor.toLowerCase().includes(searchText.toLowerCase()) ||
        row.email.toLowerCase().includes(searchText.toLowerCase());

      const matchesStatus =
        statusFilter === "ALL" || row.status === statusFilter;

      return matchesSearch && matchesStatus;
    });
  }, [rows, searchText, statusFilter]);

  if (loading) {
    return <p>Loading invoices...</p>;
  }

  if (error) {
    return <p style={{ color: "red" }}>Error: {error}</p>;
  }

  return (
    <div>
      <h2>Invoice Table</h2>

      <div style={{ marginBottom: "12px" }}>
        <input
          type="text"
          placeholder="Search by vendor or email"
          value={searchText}
          onChange={(e) => setSearchText(e.target.value)}
        />

        <select
          value={statusFilter}
          onChange={(e) => setStatusFilter(e.target.value)}
          style={{ marginLeft: "8px" }}
        >
          <option value="ALL">All</option>
          <option value="Approved">Approved</option>
          <option value="Pending">Pending</option>
        </select>
      </div>

      {filteredRows.length === 0 ? (
        <p>No records found.</p>
      ) : (
        <table border="1" cellPadding="8" cellSpacing="0">
          <thead>
            <tr>
              <th>ID</th>
              <th>Vendor</th>
              <th>Email</th>
              <th>Amount</th>
              <th>Status</th>
              <th>Risk Score</th>
            </tr>
          </thead>

          <tbody>
            {filteredRows.map((row) => (
              <tr key={row.id}>
                <td>{row.id}</td>
                <td>{row.vendor}</td>
                <td>{row.email}</td>
                <td>₹{row.amount}</td>
                <td>{row.status}</td>
                <td>{row.riskScore}</td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </div>
  );
}
```

---

## Best Practices to Mention

During interview, say:

> I am using `useEffect` for API calls, `AbortController` to cancel the request on unmount, loading/error states for UX, and `useMemo` so filtering is recalculated only when the data or filter values change.

Also say:

> For large enterprise tables, I would add server-side pagination, server-side filtering, server-side sorting, and virtualization using `react-window`.

---

## Possible Enhancements

| Enhancement | Why |
|---|---|
| Debounce search | Avoid filtering or API call on every keystroke |
| Pagination | Handle large data |
| Sorting | Improve usability |
| Virtualization | Improve rendering performance |
| Skeleton loader | Better UX |
| Error boundary | Better failure handling |
| Accessibility | Keyboard/screen reader support |

---

# 7. Promise-Based React Question

## Requirement

Fetch posts based on selected user ID.

```jsx
import { useEffect, useState } from "react";

function UserPosts({ userId }) {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!userId) return;

    const controller = new AbortController();

    async function loadPosts() {
      try {
        setLoading(true);

        const response = await fetch(
          `https://jsonplaceholder.typicode.com/posts?userId=${userId}`,
          { signal: controller.signal }
        );

        if (!response.ok) {
          throw new Error("Failed to fetch posts");
        }

        const data = await response.json();
        setPosts(data);
      } catch (error) {
        if (error.name !== "AbortError") {
          console.error(error);
        }
      } finally {
        setLoading(false);
      }
    }

    loadPosts();

    return () => controller.abort();
  }, [userId]);

  if (loading) return <p>Loading posts...</p>;

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

---

# 8. Advanced Currying for Sum

## Requirement

```js
sum(1)(2)(3)(); // 6
sum(1, 2)(3)(4, 5)(); // 15
```

## Solution

```js
function sum(...args) {
  let total = args.reduce((acc, num) => acc + num, 0);

  function inner(...nextArgs) {
    if (nextArgs.length === 0) {
      return total;
    }

    total += nextArgs.reduce((acc, num) => acc + num, 0);
    return inner;
  }

  return inner;
}

console.log(sum(1)(2)(3)()); // 6
console.log(sum(1, 2)(3)(4, 5)()); // 15
```

### Explanation

> Every function call returns another function. The inner function remembers the previous values using closure.

---

## Advanced Version Without Empty Call

```js
function sum(...args) {
  const numbers = [...args];

  function inner(...nextArgs) {
    numbers.push(...nextArgs);
    return inner;
  }

  inner.valueOf = function () {
    return numbers.reduce((acc, num) => acc + num, 0);
  };

  inner.toString = function () {
    return String(numbers.reduce((acc, num) => acc + num, 0));
  };

  return inner;
}

console.log(+sum(1)(2)(3)); // 6
console.log(Number(sum(1, 2)(3)(4))); // 10
```

---

# 9. URL Shortener System Design

## Functional Requirements

1. User enters long URL.
2. System returns short URL.
3. Short URL redirects to original URL.
4. Track analytics.
5. Support expiry date.
6. Optional custom alias.

---

## Non-Functional Requirements

1. Low latency redirect.
2. High availability.
3. Scalable reads.
4. Collision-free short codes.
5. Protection against abuse/malicious URLs.

---

## APIs

### Create Short URL

```txt
POST /api/shorten

Request:
{
  "longUrl": "https://example.com/some/large/path"
}

Response:
{
  "shortUrl": "https://app.ly/aB12xY"
}
```

### Redirect

```txt
GET /:shortCode

Response:
302 Redirect to long URL
```

---

## DB Schema

```txt
urls
----
id
short_code
long_url
user_id
created_at
expires_at
click_count
```

```txt
analytics
---------
id
short_code
ip
user_agent
referrer
created_at
country
```

---

## Short Code Generation

### Option 1: Base62 Encoding

Use characters:

```txt
a-z A-Z 0-9
```

Generate unique numeric ID, then convert it to Base62.

```txt
125789 -> bA91x
```

### Option 2: Random String

Generate a random 6-8 character code and check collision in DB.

---

## Redirect Flow

```txt
User clicks short URL
        ↓
Load balancer
        ↓
App server
        ↓
Check Redis cache
        ↓
If found, redirect
        ↓
If not found, DB lookup
        ↓
Store in cache
        ↓
Redirect to long URL
```

---

## 301 vs 302

| Status | Meaning | When to Use |
|---|---|---|
| 301 | Permanent redirect | When destination will never change |
| 302 | Temporary redirect | Better for analytics and editable destination |

### Interview Line

> I would use 302 initially because analytics and destination may change. 301 can be cached by browsers and may make analytics inaccurate.

---

## Scaling

Use:

1. Redis for hot short URLs.
2. DB index on `short_code`.
3. CDN or edge caching for popular URLs.
4. Rate limiting to prevent abuse.
5. Background queue for analytics writes.
6. Read replicas for database reads.

---

# 10. Load Balancing

## Definition

Load balancing distributes incoming traffic across multiple servers so no single server is overloaded.

It improves:

- Availability
- Scalability
- Fault tolerance
- Performance

---

## Types

| Type | Meaning |
|---|---|
| L4 Load Balancer | Works at TCP/UDP level |
| L7 Load Balancer | Works at HTTP level and can route by path/header |

---

## Algorithms

| Algorithm | Meaning |
|---|---|
| Round Robin | Sends requests one by one to servers |
| Least Connections | Sends request to server with fewer active connections |
| IP Hash | Same user IP goes to same server |
| Weighted Round Robin | Sends more traffic to powerful servers |

---

## Frontend Connection

> In frontend apps, API calls from React may hit different backend instances. So authentication/session should not depend on memory of one server. Use stateless JWT or shared session store like Redis.

---

# 11. User Authentication Flow

## Login Flow

```txt
User enters email/password
        ↓
Frontend sends POST /login
        ↓
Backend validates credentials
        ↓
Backend returns access token + refresh token
        ↓
Frontend stores token safely
        ↓
Frontend sends access token for protected APIs
```

---

## Secure Token Strategy

> For better security, store the refresh token in an HttpOnly secure cookie so JavaScript cannot access it. Keep the access token short-lived.

---

## Refresh Token Flow

```txt
Access token expires
        ↓
API returns 401
        ↓
Frontend calls /refresh-token
        ↓
Backend validates refresh token cookie
        ↓
New access token generated
        ↓
Original API request retried
```

---

## Logout Flow

```txt
Frontend calls /logout
        ↓
Backend clears refresh token cookie
        ↓
Frontend clears user state
        ↓
Redirect to login
```

---

## Security Points

1. Use HTTPS.
2. Store refresh token in HttpOnly secure cookie.
3. Keep access token short-lived.
4. Add CSRF protection if using cookies.
5. Prevent XSS.
6. Implement role-based access control.
7. Do not store highly sensitive tokens in plain localStorage.

---

# 12. CDN Explanation

## Definition

CDN means Content Delivery Network.

It serves static assets like:

- JavaScript
- CSS
- Images
- Fonts
- Videos

from edge servers close to the user.

---

## For React Apps

React production build creates files like:

```txt
main.abc123.js
style.def456.css
logo.xyz789.png
```

These files can be cached on CDN.

---

## Best Practices

1. Use hashed filenames.
2. Cache static assets for long duration.
3. Keep `index.html` cache short.
4. Use Brotli/Gzip compression.
5. Use lazy loading.
6. Use code splitting.
7. Invalidate CDN cache after deployment if needed.

---

## Interview Line

> For React apps, CDN improves initial load time by serving bundle assets from the nearest edge location. With hashed filenames, we can safely cache JS/CSS aggressively.

---

# 13. React Questions for Senior Frontend

## Q1. Why use `key` in list rendering?

`key` helps React identify which items changed, added, or removed.

```jsx
items.map((item) => <Row key={item.id} />)
```

Avoid using index if list can be reordered.

---

## Q2. `useMemo` vs `useCallback`

### useMemo

Memoizes a calculated value.

```js
const filteredRows = useMemo(() => {
  return rows.filter((row) => row.status === status);
}, [rows, status]);
```

### useCallback

Memoizes a function reference.

```js
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

---

## Q3. Controlled vs Uncontrolled Component

### Controlled

```jsx
<input value={name} onChange={(e) => setName(e.target.value)} />
```

React controls value.

### Uncontrolled

```jsx
<input ref={inputRef} />
```

DOM controls value.

---

## Q4. How to optimize a large table?

1. Use pagination.
2. Use server-side filtering.
3. Use server-side sorting.
4. Use virtualization using `react-window`.
5. Memoize filtered and sorted data.
6. Avoid unnecessary re-renders.
7. Debounce search input.
8. Memoize row component with `React.memo`.

---

# 14. Promise Programs to Practice

## Promise Delay

```js
function delay(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

delay(1000).then(() => console.log("Done after 1 sec"));
```

---

## Retry API Call

```js
async function retry(fn, retries = 3) {
  let lastError;

  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error;
    }
  }

  throw lastError;
}

retry(() => fetch("/api/data"), 3)
  .then((res) => console.log(res))
  .catch((err) => console.error(err));
```

---

## Promise.all Polyfill

```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    const results = [];
    let completed = 0;

    if (promises.length === 0) {
      resolve([]);
      return;
    }

    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((value) => {
          results[index] = value;
          completed++;

          if (completed === promises.length) {
            resolve(results);
          }
        })
        .catch(reject);
    });
  });
}
```

---

## Debounce

```js
function debounce(fn, delay) {
  let timerId;

  return function (...args) {
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}

const search = debounce((value) => {
  console.log("Searching:", value);
}, 500);

search("a");
search("ap");
search("app");
```

---

# 15. Questions to Ask Interviewer

Ask these at the end:

1. What kind of frontend problems does the team solve most often — dashboards, workflows, forms, or data-heavy tables?
2. How large are the datasets shown in the UI?
3. Do you use server-side filtering, pagination, or virtualization?
4. What is the frontend stack — React, TypeScript, Redux, React Query?
5. How do frontend engineers collaborate with design and backend teams?
6. Are there performance goals for dashboards, like load time or table rendering speed?
7. What are the expectations from a Senior Frontend Engineer in the first 3 months?

---

# 16. 2-Day Preparation Plan

## Day 1: Coding

Practice:

- React fetch + table + filter
- Add sorting
- Add pagination
- Currying sum
- Promise.all polyfill
- Debounce
- Fetch API error handling
- AbortController in React

---

## Day 2: System Design and Verbal Answers

Practice:

- URL shortener
- Authentication flow
- CDN
- Load balancing
- Event loop
- Promise vs callback vs async/await
- React optimization
- Large table design

---

# 17. Very Likely Direct Questions

Prepare these exactly:

1. Explain event loop.
2. Difference between Promise and async/await.
3. Difference between callback and promise.
4. What happens if fetch returns 404?
5. How to cancel API call in React?
6. Why use `useMemo` in table filtering?
7. How to avoid unnecessary re-rendering?
8. Implement `sum(1)(2)(3)()`.
9. Implement debounce.
10. Implement Promise.all polyfill.
11. Fetch data and show in table.
12. Filter table by one or more columns.
13. Design URL shortener.
14. Explain load balancer.
15. Explain CDN.
16. Explain login/auth flow.
17. How would you handle token expiry in frontend?
18. How would you optimize a large React table?
19. Difference between client-side and server-side filtering.
20. How would you handle API failure in UI?

---

# Senior Frontend Keywords to Use

Use these words naturally in answers:

- Error handling
- Loading state
- Empty state
- Cancellation
- AbortController
- Memoization
- Debouncing
- Pagination
- Virtualization
- Server-side filtering
- Server-side sorting
- Accessibility
- Secure token handling
- Role-based access
- CDN caching
- Performance optimization
- Scalable UI
- Enterprise dashboard

---

# Final Advice

For Senior Frontend Engineer interviews, do not only write working code.

Also explain:

1. Why this approach is correct.
2. How it handles edge cases.
3. How it scales for large data.
4. How it improves user experience.
5. How it handles security/performance.

A strong answer sounds like this:

> This solution works for small data on the client side. For production-scale enterprise data, I would move filtering, sorting, and pagination to the server, debounce the search input, add request cancellation, cache results with React Query, and use virtualization for large tables.

---
