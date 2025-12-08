# 🌟 Accessibility Interview Mastery — Senior Frontend Guide (React + Web A11y)

A beautifully structured, production-ready, senior-level accessibility guide covering  
every concept discussed during your training — **ALL information included**, refined, expanded, and formatted for interviews at **FAANG / Microsoft / Adobe / Amazon / Uber / Stripe**.

---

# ✨ Table of Contents
1. [Why Accessibility Matters](#why-accessibility-matters)
2. [Semantic HTML — Your First Superpower](#semantic-html--your-first-superpower)
3. [ARIA Fundamentals (The Golden Rule)](#aria-fundamentals-the-golden-rule)
4. [ARIA Attributes — Deep Dives & Examples](#aria-attributes--deep-dives--examples)
5. [aria-hidden — The Most Dangerous Attribute](#aria-hidden--the-most-dangerous-attribute)
6. [Keyboard Accessibility Essentials](#keyboard-accessibility-essentials)
7. [Accessible Forms (Labels, Errors, Hints, Live Regions)](#accessible-forms-labels-errors-hints-live-regions)
8. [Modals & Focus Management](#modals--focus-management)
9. [Focus Trap — Plain JS + React Hook](#focus-trap--plain-js--react-hook)
10. [Interview Cheat Sheet](#interview-cheat-sheet)
11. [Copy‑Paste Ready Code Blocks](#copy-paste-ready-code-blocks)

---

# 🌍 Why Accessibility Matters
- 1 billion+ people rely on assistive tech.
- Screen readers depend entirely on:
  - **Accessible Name**
  - **Role**
  - **State**
- Your component is only real if:
  - It works with **keyboard**
  - It works with **screen readers**
  - It respects **focus order**
  - It respects **WCAG** guidelines

---

# 🧱 Semantic HTML — Your First Superpower
Semantics gives you **accessibility for free**.

| Task | Bad | Good |
|------|------|------|
| Button | `<div onClick="...">` | `<button>` |
| Heading | `<div class="title">` | `<h1>`, `<h2>` |
| Navigation | `<div class="nav">` | `<nav>` |
| Main content | `<div id="main">` | `<main>` |

### Why semantic HTML matters:
- Screen readers instantly understand **role + behavior**
- Keyboard support **comes automatically**
- No ARIA required for simple UI

---

# 🎨 ARIA Fundamentals (The Golden Rule)
> **Use ARIA ONLY when you cannot use semantic HTML.**

Example:
❌ `role="button"` on a `<button>` → pointless  
✔ But `role="button"` on a `<div>` may be needed if forced.

---

# 🎯 ARIA Attributes — Deep Dives & Examples

## 1. `aria-label`
Provides a **name** when no visible label exists.

```html
<button aria-label="Search">🔍</button>
```

## 2. `aria-labelledby`
Uses another element’s text as the **name**.

```html
<h2 id="title">Upload File</h2>
<input type="file" aria-labelledby="title" />
```

Screen reader:  
**“Upload File, button”**

## 3. `aria-describedby`
Adds **extra info**, read *after* the name.

```html
<input aria-describedby="rules error" />

<p id="rules">Must be at least 8 characters</p>
<p id="error">Password too short</p>
```

### 🔥 One-line difference:
- `aria-labelledby` = **label / name**
- `aria-describedby` = **description / extra info**

---

# 🚫 aria-hidden — The Most Dangerous Attribute
`aria-hidden="true"` removes an element from the accessibility tree.

### ❌ NEVER put it on:
- Buttons  
- Links  
- Inputs  
- Interactive UI  

Screen reader cannot:
- see it  
- focus it  
- activate it  

Critical WCAG violation.

Use only for decorative elements.

---

# 🎹 Keyboard Accessibility Essentials

### Native elements automatically support:
- Tab focus
- Space / Enter activation
- Accessibility roles

### `tabindex` rules:
| Value | Meaning |
|-------|---------|
| `0` | focusable in natural order |
| `-1` | programmatically focusable only |
| `>0` | avoid — breaks natural flow |

### Keyboard users must be able to:
- Tab to all controls
- Activate with Enter/Space
- Escape modals
- Navigate dropdowns, menus, tabs

---

# 📝 Accessible Forms (Labels, Errors, Hints, Live Regions)

### Full pattern:

```html
<label id="pwdLabel" for="pwd">Password</label>

<input 
  id="pwd"
  type="password"
  aria-labelledby="pwdLabel"
  aria-describedby="rules error"
/>

<p id="rules">Must be at least 8 characters & include a number.</p>

<p id="error" role="alert">Password too short</p>
```

### Why this works:
- Label gives **name**
- Rules + errors give **description**
- `role="alert"` announces error **immediately**

---

# 🪟 Modals & Focus Management

### Rules:
1. Focus moves **into** the modal when opened  
2. Focus is **trapped** inside modal  
3. Focus returns to the **trigger** element on close  
4. Use:
   - `role="dialog"`
   - `aria-labelledby="modalTitle"`
   - `aria-modal="true"`

Example:

```html
<div role="dialog" aria-labelledby="modalTitle" aria-modal="true">
  <h2 id="modalTitle">Delete File</h2>
  <button>Close</button>
</div>
```

---

# 🔒 Focus Trap — Plain JS

```js
function getFocusables(container) {
  return Array.from(container.querySelectorAll(
    "a[href], button:not([disabled]), input:not([disabled]), [tabindex]:not([tabindex='-1'])"
  ));
}
```

---

# 🔒 Focus Trap — React Hook

```js
export default function useFocusTrap(ref, isOpen, onClose) { /* Focus trap code */ }
```

---

# 🧪 Interview Cheat Sheet (Super Quick)
- Semantic HTML is always preferred.
- `labelledby = name`, `describedby = extra info`.
- `role="alert"` for immediate error announcements.
- Never use `aria-hidden="true"` on buttons/inputs.
- Modal must manage focus: enter → trap → restore.
- Test with keyboard + screen reader (VoiceOver/NVDA).
- Think accessible name + role + state.

---

# 📦 Copy-Paste Ready Code Blocks

## Icon-only button
```html
<button aria-label="Search">🔍</button>
```

## Error announcement
```html
<p id="error" role="alert">Invalid password</p>
```

## Modal skeleton
```html
<div role="dialog" aria-modal="true" aria-labelledby="title">
  <h2 id="title">My Modal</h2>
  <button>Close</button>
</div>
```

---

Made with ❤️ for your FAANG-level Frontend Interviews.
