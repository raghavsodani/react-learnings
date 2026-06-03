# TypeScript + React Interview Notes

## Q1. Type vs Interface

| Interface | Type |
|------------|------|
| Best for object shapes | Supports everything |
| Supports `extends` | Supports `&` intersections |
| Supports declaration merging | No declaration merging |
| Common for props/models | Common for unions/utilities |

### Interface

```ts
interface User {
  id: number;
  name: string;
}
```

### Type

```ts
type Status = "success" | "failed" | "pending";
```

### Memory Trick

```text
Interface = Objects
Type = Everything
```

---

## Q2. Generics

### Why?

Avoid creating separate functions for every type.

```ts
function identity<T>(value: T): T {
  return value;
}
```

### Usage

```ts
identity<string>("Raghav");
identity<number>(10);
```

### Memory Trick

```text
<T> = Fill the type later
```

---

## Q3. React.Dispatch<React.SetStateAction<T>>

```ts
const [count, setCount] = useState(0);
```

Setter Type:

```ts
React.Dispatch<
  React.SetStateAction<number>
>
```

Accepts:

```ts
setCount(10);

setCount(prev => prev + 1);
```

### Memory Trick

```text
Setter Function
Value OR Callback
```

---

## Q4. Discriminated Union

```ts
type Success = {
  status: "success";
  data: string[];
};

type Error = {
  status: "error";
  message: string;
};

type Response = Success | Error;
```

### Memory Trick

```text
status
state
type
kind
```

Used as discriminators.

---

## Q5. React Query Typing

```ts
interface User {
  id: number;
  name: string;
}

const fetchUser = async (): Promise<User> => {
  return response.data;
};
```

```ts
useQuery<User>({
  queryKey: ["user"],
  queryFn: fetchUser
});
```

---

## Q6. Generic Reusable Table

```ts
type Column<T> = {
  key: keyof T;
  header: string;
};
```

```ts
type TableProps<T> = {
  data: T[];
  columns: Column<T>[];
};
```

Benefits:

- Reusable
- Type-safe
- Compile-time validation

---

# React Types Cheat Sheet

| Type | Meaning |
|--------|---------|
| React.ReactNode | Anything React can render |
| JSX.Element | One JSX element |
| React.FC | Functional component type |
| PropsWithChildren | Adds children prop |
| Dispatch<SetStateAction<T>> | useState setter |

---

# Event Types

| Event | Type |
|---------|--------|
| Input Change | React.ChangeEvent<HTMLInputElement> |
| Textarea Change | React.ChangeEvent<HTMLTextAreaElement> |
| Select Change | React.ChangeEvent<HTMLSelectElement> |
| Button Click | React.MouseEvent<HTMLButtonElement> |
| Form Submit | React.FormEvent<HTMLFormElement> |
| Keyboard | React.KeyboardEvent<HTMLInputElement> |
| Focus | React.FocusEvent<HTMLInputElement> |

---

# useRef Types

```ts
useRef<HTMLInputElement>(null);
useRef<HTMLDivElement>(null);
useRef<HTMLButtonElement>(null);
useRef<HTMLFormElement>(null);
```

---

# Utility Types

## Partial<T>

```ts
Partial<User>
```

Makes every property optional.

---

## Pick<T>

```ts
Pick<User, "name" | "email">
```

Select specific fields.

---

## Omit<T>

```ts
Omit<User, "id">
```

Remove fields.

---

## Record<K, V>

```ts
Record<string, number>
```

Dictionary / Map structure.

---

# keyof

```ts
interface User {
  id: number;
  name: string;
}
```

```ts
keyof User
```

Result:

```ts
"id" | "name"
```

---

# Component Props Extraction

```ts
type ButtonProps =
  React.ComponentProps<
    typeof Button
  >;
```

---

# Top 15 Must-Memorize Types

| Priority | Type |
|-----------|--------|
| ⭐⭐⭐⭐⭐ | React.ReactNode |
| ⭐⭐⭐⭐⭐ | JSX.Element |
| ⭐⭐⭐⭐⭐ | React.Dispatch<React.SetStateAction<T>> |
| ⭐⭐⭐⭐⭐ | React.ChangeEvent<HTMLInputElement> |
| ⭐⭐⭐⭐⭐ | React.MouseEvent<HTMLButtonElement> |
| ⭐⭐⭐⭐⭐ | React.FormEvent<HTMLFormElement> |
| ⭐⭐⭐⭐⭐ | useRef<HTMLInputElement> |
| ⭐⭐⭐⭐⭐ | keyof |
| ⭐⭐⭐⭐⭐ | Partial<T> |
| ⭐⭐⭐⭐⭐ | Pick<T> |
| ⭐⭐⭐⭐⭐ | Omit<T> |
| ⭐⭐⭐⭐⭐ | Record<K,V> |
| ⭐⭐⭐⭐⭐ | Promise<T> |
| ⭐⭐⭐⭐⭐ | ComponentProps<typeof X> |
| ⭐⭐⭐⭐⭐ | Generic <T> |

---

# 30-Second Revision

```text
ReactNode     → Anything React can render
JSX.Element   → One JSX element
Dispatch      → useState setter
ChangeEvent   → Input change event
MouseEvent    → Click event
FormEvent     → Submit event
useRef<T>     → DOM reference
keyof         → Object keys
Partial       → Optional fields
Pick          → Select fields
Omit          → Remove fields
Record        → Key-value map
Promise<T>    → Async result
ComponentProps→ Extract component props
<T>           → Generic placeholder
```
