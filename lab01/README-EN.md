
# JavaScript Lab Exercises 1
## Lab Exercises: Modern JavaScript Fundamentals (ES6+)

**Lab goal**: Get familiar with the JavaScript syntax we will use all the time in React Native:
- ✅ `const` / `let`, types, `===`
- ✅ Functions & arrow functions
- ✅ Template strings, destructuring, spread `...`
- ✅ Array methods: `map`, `filter`, `find`, `reduce`
- ✅ Asynchronous code: Promises & `async`/`await`
- ✅ Modules: `import` / `export`

---

## Exercise 0: Setup

<div class="exercise">

**Goal**: Install Node.js and create a working folder

**Steps**:
```bash
# 1. Install Node.js (LTS version)
### - Download from [nodejs.org](https://nodejs.org/)
### - v22.13 or newer is required (v24 LTS recommended)
###   We will use the same one for Expo/React Native from Lab 02 onwards

# 2. Verification (in a NEW terminal after installation)
node -v
npm -v

# 3. Working folder
mkdir -p ~/ReactNativeLab/lab1
cd ~/ReactNativeLab/lab1
code .
```

</div>

<div class="tip">

**One file per exercise**: e.g. `ex1.js`, run with `node ex1.js`  
For `import` / `export` or top-level `await`, use **`.mjs`** files (e.g. `node ex5.mjs`)

</div>

---

## Exercise 1: Variables and types

<div class="exercise">

**Goal**: `const` / `let`, template strings, `typeof`, equality

Write a script `ex1.js` that:
1. Declares 3 variables (`name`, `age`, `student`) — with `const` or `let`? Why?
2. Prints them using template strings
3. Uses `typeof` to show each type
4. Compares `age == "20"` and `age === "20"` — explain the difference

</div>

**Expected output** (approximately):
```text
Name: Eleni, Age: 20, Student: true
string number boolean
true
false
```

---

## Exercise 2: Arrays & Loops

<div class="exercise">

**Goal**: `for…of` loop, `.map()`, `.filter()`, immutable arrays

Create (`ex2.js`) an array with your 5 favorite songs.  
Use:
1. a **`for…of`** loop to print each one
2. **`.map()`** to create an array with the titles in uppercase ([MDN: .map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map))
3. **`.filter()`** to keep only the titles longer than 10 characters
4. At the end, print the original array — make sure it has **not** changed

</div>

---

## Exercise 3: Functions

<div class="exercise">

**Goal**: Function declarations, arrow functions, ternary

Write (`ex3.js`) a function `grade(score)` that:
- returns `"Pass"` if the score is ≥ 50
- returns `"Fail"` otherwise

Test it with various values (e.g. `75`, `50`, `49.9`, `0`).

**Extension**:
1. Rewrite it as a **one-line arrow function** using the `? :` operator
2. Apply it to an array of scores with `.map()`

</div>

---

## Exercise 4: Objects & Destructuring

<div class="exercise">

**Goal**: Object destructuring, destructuring in parameters, spread

Define (`ex4.js`) a `book` object with `title`, `author`, `year`.  
Use destructuring to print:

> 📘 *title* by *author* (*year*)

**Extension**:
1. Write a function `describe({ title, author })` that destructures **in its parameters**
2. Create an `updated` object with a new `year` using **spread** (`...`) — `book` must stay unchanged

</div>

<div class="tip">

You will see `function Card({ title, author })` in **every** React component: this is how we read props.

</div>

---

## Exercise 5: Async Fetch

<div class="exercise">

**Goal**: Promises, `async` / `await`, error handling

Write (`ex5.mjs`) a function that fetches users from  
`https://jsonplaceholder.typicode.com/users`  
and prints **only their names**.

1. One version with `.then()` / `.catch()`
2. One version with `async` / `await` and `try` / `catch`
3. Change the URL to `.../userz`. What happens? Show a clear error message.

</div>

<div class="tip">

`fetch` does **not** throw an error when the server responds with 404 or 500 — check `res.ok`.

</div>

---

## Exercise 6: Immutable list updates

<div class="exercise">

**Goal**: Spread, `map`, `filter` — without changing the original array

Start (`ex6.js`) from:
```js
const todos = [
  { id: 1, text: "Install Node", done: true },
  { id: 2, text: "Learn JS",     done: false },
];
```

Write functions that **return a new array** without changing the original:
1. `addTodo(todos, text)` — adds a new todo (unique `id`, `done: false`)
2. `toggleTodo(todos, id)` — flips `done` of the todo with that `id`
3. `removeTodo(todos, id)` — removes the todo

Call them one after another and, at the end, print `todos` — it must be **unchanged**.

</div>

<details>
<summary>Hint</summary>

- Add: `[...arr, newItem]`
- Change one item: `.map(t => t.id === id ? { ...t, done: !t.done } : t)`
- Remove: `.filter(...)`
- ⚠️ No `push`, `splice`, or `t.done = ...`

</details>

<div class="tip">

This is **exactly** how we update lists in React state (Lab 03).

</div>

---

## 🚀 Mini-project: Formatting JSON data

<div class="exercise">

**Goal**: Combine everything above

Create a script `formatUsers.mjs` that:
1. Fetches users from `https://jsonplaceholder.typicode.com/users` (with `async` / `await`)
2. Extracts the name, email and city (with destructuring — the city is in `address.city`)
3. Sorts them alphabetically by name
4. Prints them nicely formatted:

```text
👤 Leanne Graham
📧 Sincere@april.biz
🏙️  Gwenborough
-----------------
```

**Bonus**: Put the formatting in a separate module (`format.mjs`) with `export`, and `import` it in `formatUsers.mjs`.

</div>

---

## Common Troubleshooting Issues

### Issue 1: `ReferenceError: require is not defined in ES module scope` or `SyntaxError: Cannot use import statement outside a module`
- You have mixed `import` and `require` in the same file — use **only** `import` / `export`
- Use `.mjs` files (or `"type": "module"` in `package.json`) to make it clear that it is an ES module

### Issue 2: `SyntaxError: Unexpected reserved word` (on `await`)
- You are using `await` inside a function that is **not** `async` — add `async` in front of the function

### Issue 3: `TypeError: Assignment to constant variable.`
- You are trying to change a `const` — use `let` **or** (better) create a new variable

### Issue 4: `TypeError: Cannot read properties of undefined (reading '...')`
- Some intermediate field does not exist — check the structure with `console.log(...)` or use `?.`

### Issue 5: `ReferenceError: X is not defined`
- A typo in the name, a variable out of scope, or use before the `const`/`let` declaration

---

## Checklist

- [ ] `node -v` shows v22.13 or newer
- [ ] Exercises 1–4 run with `node exN.js`
- [ ] Exercise 5 prints the 10 names and handles the wrong URL
- [ ] In Exercise 6 the original `todos` array stays unchanged
- [ ] The mini-project prints the users sorted
- [ ] I can explain: `const` vs `let`, `==` vs `===`, `map` vs `filter`, what `...` does

➡️ In **Lab 02** we will set up the React Native environment (NPM, Expo) on top of the same Node.js.

---
