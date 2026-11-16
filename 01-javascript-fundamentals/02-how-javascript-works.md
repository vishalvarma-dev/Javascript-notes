# How JavaScript Works

## Global Execution Context

When a JavaScript program runs, the engine creates a **Global Execution Context (GEC)** and pushes it onto the call stack. It is always the first context created.

This context has **two phases**:

| Phase | Also called | What happens |
|---|---|---|
| Phase 1 | Memory / Creation phase | Variables and functions are allocated in memory |
| Phase 2 | Execution phase | Code runs line by line |

---

## Phase 1 — Memory (Creation) Phase

The engine scans the entire code **before running a single line** and allocates memory:

- **`var` variables** → stored as `undefined`
- **Function declarations** → stored as the **entire function body**
- **`let` / `const` variables** → stored but placed in a **Temporal Dead Zone (TDZ)** — they exist in memory but cannot be accessed yet

No code executes during this phase.

### Example — what memory looks like after Phase 1

```javascript
var age = 25;
let city = "Delhi";
const PI = 3.14;

function greet() {
  console.log("Hello!");
}
```

After the memory phase, internally it looks like:

```
age      → undefined          (var, hoisted to undefined)
city     → <TDZ>              (let, cannot be touched yet)
PI       → <TDZ>              (const, cannot be touched yet)
greet    → function body      (whole function stored)
```

---

## Phase 2 — Execution Phase

The engine goes through the code **line by line** and runs it:

- Variables get their actual values assigned.
- Functions get called when invoked.
- `let` / `const` exit the TDZ when their line is reached.

---

## Hoisting

**Hoisting** is the behaviour where variables and functions are "lifted" to the top of their scope during the memory phase — so you can reference them before they appear in the code.

### `var` — hoisted to `undefined`

```javascript
console.log(name); // undefined  (no error!)
var name = "Alice";
console.log(name); // "Alice"
```

`name` was allocated in memory as `undefined` before line 1 ran.

### Function declarations — fully hoisted

```javascript
sayHi(); // "Hi!"  (works before the definition!)

function sayHi() {
  console.log("Hi!");
}
```

The whole function body was stored in memory during Phase 1.

### `let` / `const` — hoisted but in the TDZ

```javascript
console.log(score); // ReferenceError: Cannot access 'score' before initialization
let score = 100;
```

`score` exists in memory but is in the **Temporal Dead Zone** — accessing it before its line throws an error.

### Quick comparison

| Declaration | Hoisted? | Initial value | Accessible before line? |
|---|---|---|---|
| `var` | ✅ Yes | `undefined` | ✅ Yes (returns `undefined`) |
| `function` | ✅ Yes | Full body | ✅ Yes (fully callable) |
| `let` | ✅ Yes | TDZ | ❌ No (ReferenceError) |
| `const` | ✅ Yes | TDZ | ❌ No (ReferenceError) |

---

## The Call Stack

The **call stack** is a data structure that tracks which execution context is currently running. It works like a stack of plates — **last in, first out (LIFO)**.

- A context is **pushed** when a function is called.
- A context is **popped** when a function returns.

### Example

```javascript
function multiply(a, b) {
  return a * b;
}

function square(n) {
  return multiply(n, n);
}

var result = square(4);
console.log(result); // 16
```

The call stack step by step:

```
Step 1: [ Global ]                     ← GEC created
Step 2: [ Global → square(4) ]         ← square() called
Step 3: [ Global → square → multiply ] ← multiply() called inside square
Step 4: [ Global → square ]            ← multiply() returns 16, popped
Step 5: [ Global ]                     ← square() returns 16, popped
Step 6: []                             ← program ends, GEC popped
```

> **Stack Overflow** happens when functions call each other infinitely and the stack runs out of space.

---

## Function Execution Context

Every time a function is **called**, the engine creates a new **Function Execution Context (FEC)** and pushes it onto the call stack. It also goes through the same two phases (memory + execution), but only for that function's code.

Each FEC has three components:

| Component | Description |
|---|---|
| **Variable Environment** | The function's own local variables |
| **Lexical Environment** | Reference to the outer scope where the function was *written* |
| **`this`** | The value of `this` for that call |

### Example

```javascript
var user = "Bob";

function showUser() {
  var message = "Hello"; // local to showUser
  console.log(message + ", " + user); // "Hello, Bob"
}

showUser();
console.log(message); // ReferenceError — message is not in global scope
```

When `showUser()` is called:
1. A new FEC is pushed onto the stack.
2. Memory phase: `message → undefined`
3. Execution phase: `message → "Hello"`, then `console.log` runs.
4. FEC is popped. `message` is gone forever.

---

## Scope Chain

When the engine looks up a variable, it starts in the **current scope** and walks outward until it finds it (or hits the global scope and throws a `ReferenceError`). This chain of scopes is the **scope chain**.

### Example

```javascript
var language = "JavaScript";

function outer() {
  var framework = "React";

  function inner() {
    var tool = "Vite";
    console.log(tool);      // "Vite"      — found in own scope
    console.log(framework); // "React"     — found in outer's scope
    console.log(language);  // "JavaScript"— found in global scope
  }

  inner();
}

outer();
```

The scope chain for `inner`:

```
inner scope → outer scope → global scope
```

The engine walks up until it finds the variable. If it reaches the global scope and still can't find it → `ReferenceError`.

---

## Full Worked Example (Putting it all together)

```javascript
var name = "hello";

function printName() {
  console.log(name);
}

printName();
```

Step by step:

1. **GEC created**, pushed onto the call stack.
2. **Memory phase (GEC):**
   - `name → undefined`
   - `printName → [whole function body]`
3. **Execution phase (GEC):**
   - Line 1: `name` assigned `"hello"`.
   - Line 7: `printName()` is called.
4. **FEC for `printName` created**, pushed onto stack.
5. **Memory phase (FEC):** No local variables.
6. **Execution phase (FEC):**
   - `name` not found locally → walks up scope chain → found in GEC as `"hello"`.
   - `console.log` prints `hello`.
7. `printName` FEC is **popped** off the stack.
8. GEC continues, reaches end → **GEC popped**. Done.

---

## Summary

```
JS Engine
│
├── Parses code
└── Creates Global Execution Context
    │
    ├── Phase 1: Memory  → var=undefined, let/const=TDZ, functions=full body
    └── Phase 2: Execute → code runs line by line
                │
                └── Function called?
                    └── New Function Execution Context pushed to Call Stack
                        ├── Own variables
                        ├── Scope chain (lexical environment)
                        └── this
```
