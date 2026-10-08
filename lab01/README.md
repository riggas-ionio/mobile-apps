
# JavaScript Lab Exercises 1
## Εργαστηριακές Ασκήσεις: Βασικές αρχές σύγχρονης JavaScript (ES6+)

**Στόχος του εργαστηρίου**: Εξοικείωση με τη σύνταξη της JavaScript που θα χρησιμοποιούμε συνεχώς στο React Native:
- ✅ `const` / `let`, τύποι, `===`
- ✅ Συναρτήσεις & arrow functions
- ✅ Template strings, αποδόμηση (destructuring), spread `...`
- ✅ Μέθοδοι πινάκων: `map`, `filter`, `find`, `reduce`
- ✅ Ασύγχρονος κώδικας: Promises & `async`/`await`
- ✅ Modules: `import` / `export`

---

## Άσκηση 0: Προετοιμασία

<div class="exercise">

**Στόχος**: Εγκατάσταση Node.js και φάκελος εργασίας

**Βήματα**:
```bash
# 1. Εγκατάσταση Node.js (LTS version)
### - Download από [nodejs.org](https://nodejs.org/)
### - Απαιτείται v22.13 ή νεότερο (προτείνεται v24 LTS)
###   Το ίδιο θα χρησιμοποιήσουμε για Expo/React Native από το Lab 02

# 2. Verification (σε ΝΕΟ terminal μετά την εγκατάσταση)
node -v
npm -v

# 3. Φάκελος εργασίας
mkdir -p ~/ReactNativeLab/lab1
cd ~/ReactNativeLab/lab1
code .
```

</div>

<div class="tip">

**Ένα αρχείο ανά άσκηση**: π.χ. `ex1.js`, εκτέλεση με `node ex1.js`  
Για `import` / `export` ή `await` στο top level, χρησιμοποιήστε αρχεία **`.mjs`** (π.χ. `node ex5.mjs`)

</div>

---

## Άσκηση 1: Μεταβλητές και τύποι

<div class="exercise">

**Στόχος**: `const` / `let`, template strings, `typeof`, ισότητα

Γράψτε ένα σενάριο `ex1.js` που:
1. Δηλώνει 3 μεταβλητές (`name`, `age`, `student`) — με `const` ή `let`; Γιατί;
2. Τις εκτυπώνει χρησιμοποιώντας πρότυπες συμβολοσειρές (template strings)
3. Χρησιμοποιεί `typeof` για να δείξει κάθε τύπο
4. Συγκρίνει `age == "20"` και `age === "20"` — εξηγήστε τη διαφορά

</div>

**Αναμενόμενο αποτέλεσμα** (ενδεικτικά):
```text
Name: Eleni, Age: 20, Student: true
string number boolean
true
false
```

---

## Άσκηση 2: Πίνακες & Βρόχοι

<div class="exercise">

**Στόχος**: Βρόχος `for…of`, `.map()`, `.filter()`, αμετάβλητοι πίνακες

Δημιουργήστε (`ex2.js`) έναν πίνακα με τα 5 αγαπημένα σας τραγούδια.  
Χρησιμοποιήστε:
1. έναν βρόχο **`for…of`** για να εκτυπώσετε το καθένα
2. **`.map()`** για να δημιουργήσετε έναν πίνακα με τίτλους σε κεφαλαία ([MDN: .map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map))
3. **`.filter()`** για να κρατήσετε μόνο τους τίτλους με περισσότερους από 10 χαρακτήρες
4. Εκτυπώστε στο τέλος τον αρχικό πίνακα — βεβαιωθείτε ότι **δεν** άλλαξε

</div>

---

## Άσκηση 3: Συναρτήσεις

<div class="exercise">

**Στόχος**: Δήλωση συνάρτησης, arrow functions, ternary

Γράψτε (`ex3.js`) μια συνάρτηση `grade(score)` που:
- επιστρέφει `"Pass"` αν το σκορ είναι ≥ 50
- επιστρέφει `"Fail"` σε κάθε άλλη περίπτωση

Δοκιμάστε την με διάφορες τιμές (π.χ. `75`, `50`, `49.9`, `0`).

**Επέκταση**:
1. Ξαναγράψτε την ως **arrow function μίας γραμμής** με τον τελεστή `? :`
2. Εφαρμόστε την σε έναν πίνακα σκορ με `.map()`

</div>

---

## Άσκηση 4: Αντικείμενα & Αποδόμηση

<div class="exercise">

**Στόχος**: Object destructuring, destructuring σε παραμέτρους, spread

Ορίστε (`ex4.js`) ένα αντικείμενο `book` με `title`, `author`, `year`.  
Χρησιμοποιήστε αποδόμηση για να εκτυπώσετε:

> 📘 *title* από *author* (*year*)

**Επέκταση**:
1. Γράψτε συνάρτηση `describe({ title, author })` που αποδομεί **στις παραμέτρους**
2. Δημιουργήστε αντικείμενο `updated` με νέο `year` χρησιμοποιώντας **spread** (`...`) — το `book` να μείνει ίδιο

</div>

<div class="tip">

Θα δείτε το `function Card({ title, author })` σε **κάθε** React component: έτσι διαβάζουμε τα props.

</div>

---

## Άσκηση 5: Async Fetch

<div class="exercise">

**Στόχος**: Promises, `async` / `await`, χειρισμός σφαλμάτων

Γράψτε (`ex5.mjs`) μια συνάρτηση που ανακτά χρήστες από  
`https://jsonplaceholder.typicode.com/users`  
και εκτυπώνει **μόνο τα ονόματά** τους.

1. Μία έκδοση με `.then()` / `.catch()`
2. Μία έκδοση με `async` / `await` και `try` / `catch`
3. Αλλάξτε το URL σε `.../userz`. Τι συμβαίνει; Εμφανίστε κατανοητό μήνυμα σφάλματος.

</div>

<div class="tip">

Το `fetch` **δεν** πετάει error όταν ο server απαντά 404 ή 500 — ελέγξτε το `res.ok`.

</div>

---

## Άσκηση 6: Immutable ενημερώσεις λίστας

<div class="exercise">

**Στόχος**: Spread, `map`, `filter` — χωρίς αλλαγή του αρχικού πίνακα

Ξεκινήστε (`ex6.js`) από:
```js
const todos = [
  { id: 1, text: "Install Node", done: true },
  { id: 2, text: "Learn JS",     done: false },
];
```

Γράψτε συναρτήσεις που **επιστρέφουν νέο πίνακα** χωρίς να αλλάζουν τον αρχικό:
1. `addTodo(todos, text)` — προσθέτει νέο todo (μοναδικό `id`, `done: false`)
2. `toggleTodo(todos, id)` — αντιστρέφει το `done` του todo με αυτό το `id`
3. `removeTodo(todos, id)` — αφαιρεί το todo

Καλέστε τις διαδοχικά και, στο τέλος, εκτυπώστε το `todos` — πρέπει να είναι **αμετάβλητο**.

</div>

<details>
<summary>Hint</summary>

- Προσθήκη: `[...arr, newItem]`
- Αλλαγή ενός στοιχείου: `.map(t => t.id === id ? { ...t, done: !t.done } : t)`
- Αφαίρεση: `.filter(...)`
- ⚠️ Όχι `push`, `splice`, ή `t.done = ...`

</details>

<div class="tip">

Αυτός είναι **ακριβώς** ο τρόπος που ενημερώνουμε λίστες στο state του React (Lab 03).

</div>

---

## 🚀 Mini-project: Μορφοποίηση δεδομένων JSON

<div class="exercise">

**Στόχος**: Συνδυασμός όλων των παραπάνω

Δημιουργήστε ένα σενάριο `formatUsers.mjs` που:
1. Ανακτά χρήστες από το `https://jsonplaceholder.typicode.com/users` (με `async` / `await`)
2. Εξάγει το όνομα, το email και την πόλη (με αποδόμηση — η πόλη βρίσκεται στο `address.city`)
3. Τα ταξινομεί αλφαβητικά κατά όνομα
4. Τα εκτυπώνει όμορφα μορφοποιημένα:

```text
👤 Leanne Graham
📧 Sincere@april.biz
🏙️  Gwenborough
-----------------
```

**Bonus**: Βάλτε τη μορφοποίηση σε ξεχωριστό module (`format.mjs`) με `export` και κάντε `import` στο `formatUsers.mjs`.

</div>

---

## Common Troubleshooting Issues

### Issue 1: `ReferenceError: require is not defined in ES module scope` ή `SyntaxError: Cannot use import statement outside a module`
- Έχετε αναμείξει `import` και `require` στο ίδιο αρχείο — χρησιμοποιήστε **μόνο** `import` / `export`
- Χρησιμοποιήστε αρχεία `.mjs` (ή `"type": "module"` στο `package.json`) για να είναι σαφές ότι πρόκειται για ES module

### Issue 2: `SyntaxError: Unexpected reserved word` (στο `await`)
- Χρησιμοποιείτε `await` μέσα σε συνάρτηση που **δεν** είναι `async` — προσθέστε `async` μπροστά από τη συνάρτηση

### Issue 3: `TypeError: Assignment to constant variable.`
- Προσπαθείτε να αλλάξετε μια `const` — χρησιμοποιήστε `let` **ή** (καλύτερα) δημιουργήστε νέα μεταβλητή

### Issue 4: `TypeError: Cannot read properties of undefined (reading '...')`
- Κάποιο ενδιάμεσο πεδίο δεν υπάρχει — ελέγξτε τη δομή με `console.log(...)` ή χρησιμοποιήστε `?.`

### Issue 5: `ReferenceError: X is not defined`
- Ορθογραφικό λάθος στο όνομα, ή μεταβλητή εκτός εμβέλειας (scope), ή χρήση πριν από τη δήλωση `const`/`let`

---

## Checklist

- [ ] `node -v` δείχνει v22.13 ή νεότερο
- [ ] Ασκήσεις 1–4 τρέχουν με `node exN.js`
- [ ] Η Άσκηση 5 εκτυπώνει τα 10 ονόματα και χειρίζεται το λάθος URL
- [ ] Στην Άσκηση 6 ο αρχικός πίνακας `todos` μένει αμετάβλητος
- [ ] Το mini-project εκτυπώνει τους χρήστες ταξινομημένους
- [ ] Μπορώ να εξηγήσω: `const` vs `let`, `==` vs `===`, `map` vs `filter`, τι κάνει το `...`

➡️ Στο **Lab 02** θα στήσουμε το περιβάλλον για React Native (NPM, Expo) πάνω στο ίδιο Node.js.

---
