
# React Native Lab Exercises 3
## Lab Exercises: State, Interaction & Expo Go

**Prerequisites**: [Lab 02](../lab02/README-EN.md) completed — a working Node.js/NPM setup and the project `~/ReactNativeLab/firstapp` running in the browser.

**Lab goals**:
- ✅ Run the app on a real device with Expo Go
- ✅ State management with the `useState` hook
- ✅ Handle touch events (`onPress`) and text input (`onChangeText`)
- ✅ Conditional rendering & conditional styling
- ✅ Render lists from arrays (`map` + `key`)

```bash
# We start from the Lab 02 project
cd ~/ReactNativeLab/firstapp
npx expo start
```

---

## Exercise 1: Installing the Expo Go App

<div class="exercise">

**Goal**: Test the app on a real device

**Steps**:

**For iOS**:
1. Open the App Store on your iPhone/iPad
2. Search for "Expo Go"
3. Download & install (free app)

**For Android**:
1. Open the Google Play Store
2. Search for "Expo Go"
3. Download & install

**Testing**:
1. Make sure the device and the computer are on the **same WiFi network**
2. Run `npx expo start` on the computer
3. Scan the QR code with:
   - iOS: Camera app
   - Android: Expo Go app

</div>

<div class="tip">

**University network**: If the device does not connect (common on public WiFi), try `npx expo start --tunnel`  
**SDK version**: Expo Go only supports the latest Expo SDK version. If you see an incompatibility message, update Expo Go or the project (`npx expo install expo@latest --fix`)

</div>

From now on, test every exercise **both** in the browser **and** on your phone.

---

## Exercise 1b: Theory - Expo Architecture

### How Expo works:

```
Development Machine                Mobile Device
┌─────────────────────┐            ┌────────────┐
│ Metro Bundler       │            │ Expo Go App│
│ ↓                   │   WiFi     │            │
│ JavaScript Bundle   │ ─────────> │ JavaScript │
│ ↓                   │            │ Engine     │
│ Expo Dev Server     │            │ ↓          │
│ (Port 8081)         │            │ Native     │
└─────────────────────┘            │ Components │
                                   └────────────┘
```

**Key points**:
- **Metro Bundler**: Transpiles JSX → JavaScript
- **Expo Go**: Container app with pre-built native modules
- **Over-the-air**: JavaScript bundle sent over the network
- **No compilation**: Instant updates without a rebuild

**Question**: Compare how the app looks in the browser and on the phone. What differences do you notice (fonts, status bar, sizes)?

---

## Exercise 2: Adding a Button Component

<div class="exercise">

**Goal**: Add an interactive button with state management

**Steps**:
1. Import the useState hook and TouchableOpacity
2. Create a state variable for the counter
3. Add a button that increases the counter
4. Display the counter value

</div>

**New concepts**:
- **useState**: React Hook for state management
- **TouchableOpacity**: Touchable button component
- **onPress**: Event handler for touch events

---

## Exercise 2b: Complete Code with Button

```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View, TouchableOpacity } from 'react-native';
import { useState } from 'react';

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome to React Native!</Text>
      <Text style={styles.subtitle}>Mobile App Development Lab</Text>
      
      <View style={styles.counterContainer}>
        <Text style={styles.counterText}>Counter: {count}</Text>
        <TouchableOpacity 
          style={styles.button}
          onPress={() => setCount(count + 1)}
        >
          <Text style={styles.buttonText}>Increment +</Text>
        </TouchableOpacity>
      </View>
      
      <StatusBar style="auto" />
    </View>
  );
}
```

---

## Exercise 2c: Button Styles

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
    alignItems: 'center',
    justifyContent: 'center',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2c3e50',
    marginBottom: 10,
  },
  subtitle: {
    fontSize: 16,
    color: '#7f8c8d',
    marginBottom: 30,
  },
  counterContainer: {
    marginTop: 20,
    alignItems: 'center',
  },
  counterText: {
    fontSize: 32,
    fontWeight: 'bold',
    color: '#e74c3c',
    marginBottom: 15,
  },
  button: {
    backgroundColor: '#3498db',
    paddingHorizontal: 30,
    paddingVertical: 15,
    borderRadius: 8,
  },
  buttonText: {
    color: 'white',
    fontSize: 18,
    fontWeight: 'bold',
  },
});

```

---

## Exercise 3: Extending the Counter

<div class="exercise">

**Goal**: More buttons that change the same state

**Requirements**:
1. Add a **`−`** button that decreases the counter
2. Add a **Reset** button that sets it to zero
3. The counter must **not** go negative
4. Put the three buttons on the same row (`flexDirection: 'row'`)

</div>

<div class="tip">

**Functional update**: When the new state depends on the previous one, prefer  
`setCount(prev => prev + 1)` over `setCount(count + 1)`

</div>

<details>
<summary>Sample solution</summary>

```jsx
const increment = () => setCount(prev => prev + 1);
const decrement = () => setCount(prev => Math.max(0, prev - 1));
const reset = () => setCount(0);

// ...
<View style={styles.buttonRow}>
  <TouchableOpacity style={styles.button} onPress={decrement}>
    <Text style={styles.buttonText}>−</Text>
  </TouchableOpacity>
  <TouchableOpacity style={styles.button} onPress={reset}>
    <Text style={styles.buttonText}>Reset</Text>
  </TouchableOpacity>
  <TouchableOpacity style={styles.button} onPress={increment}>
    <Text style={styles.buttonText}>+</Text>
  </TouchableOpacity>
</View>

// styles
buttonRow: {
  flexDirection: 'row',
  gap: 10,
},
```
</details>

---

## Exercise 4: Conditional Styling & Rendering

<div class="exercise">

**Goal**: The UI changes depending on the state value

**Requirements**:
1. The counter colour should be:
   - grey when `count === 0`
   - green when `count` is even
   - red when `count` is odd
2. When the counter reaches **10**, show the message "🎉 You reached the limit!" and disable the `+` button (`disabled` prop)
3. The disabled button should look different (e.g. `opacity: 0.4`)

</div>

**New concepts**:
- **Style arrays**: `style={[styles.counterText, { color }]}` — later styles win
- **Conditional rendering**: `{condition && <Text>...</Text>}` or `{condition ? <A /> : <B />}`
- **disabled** prop on `TouchableOpacity`

<details>
<summary>Sample solution</summary>

```jsx
const MAX = 10;
const atMax = count >= MAX;

const counterColor =
  count === 0 ? '#95a5a6' : count % 2 === 0 ? '#27ae60' : '#e74c3c';

// ...
<Text style={[styles.counterText, { color: counterColor }]}>
  Counter: {count}
</Text>

{atMax && <Text style={styles.limitText}>🎉 You reached the limit!</Text>}

<TouchableOpacity
  style={[styles.button, atMax && styles.buttonDisabled]}
  onPress={increment}
  disabled={atMax}
>
  <Text style={styles.buttonText}>+</Text>
</TouchableOpacity>

// styles
buttonDisabled: {
  opacity: 0.4,
},
limitText: {
  fontSize: 16,
  color: '#8e44ad',
  marginBottom: 10,
},
```
</details>

---

## Exercise 5: Advanced Features - TextInput

<div class="exercise">

**Goal**: Add a text input for user interaction

**New features**:
- TextInput component
- State management for text
- Dynamic content based on input
- Multiple state variables

**Functionality**:
Create an app that:
1. Has a text input for the user's name
2. A button that shows a personalised greeting
3. A counter of how many times the button was pressed

</div>

<details>
<summary>Sample solution</summary>

```jsx
import { StatusBar } from 'expo-status-bar';
import { 
  StyleSheet, 
  Text, 
  View, 
  TouchableOpacity,
  TextInput
} from 'react-native';
import { useState } from 'react';

export default function App() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  const [greeting, setGreeting] = useState('');

  const handleGreeting = () => {
    setCount(count + 1);
    if (name.trim()) {
      setGreeting(`Hello, ${name}! (Click #${count + 1})`);
    } else {
      setGreeting('Please enter your name!');
    }
  };
  return (
    <View style={styles.container}>
      <Text style={styles.title}>React Native Lab Exercise</Text>
      
      <View style={styles.inputContainer}>
        <Text style={styles.label}>Enter your name:</Text>
        <TextInput
          style={styles.input}
          placeholder="Your name..."
          value={name}
          onChangeText={setName}
        />
      </View>
      
      <TouchableOpacity style={styles.button} onPress={handleGreeting}>
        <Text style={styles.buttonText}>Greet</Text>
      </TouchableOpacity>
      
      {greeting ? (
        <View style={styles.greetingContainer}>
          <Text style={styles.greetingText}>{greeting}</Text>
        </View>
      ) : null}
      
      <Text style={styles.counterText}>Total Clicks: {count}</Text>
      <StatusBar style="auto" />
    </View>
  );
} 

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
    alignItems: 'center',
    justifyContent: 'center',
    padding: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#2c3e50',
    marginBottom: 30,
  },
  inputContainer: {
    width: '100%',
    maxWidth: 400,
    marginBottom: 20,
  },
  label: {
    fontSize: 16,
    color: '#34495e',
    marginBottom: 8,
    fontWeight: '600',
  },
  input: {
    backgroundColor: 'white',
    borderWidth: 2,
    borderColor: '#3498db',
    borderRadius: 8,
    padding: 15,
    fontSize: 16,
  },
  button: {
    backgroundColor: '#3498db',
    paddingHorizontal: 40,
    paddingVertical: 15,
    borderRadius: 8,
    marginTop: 10,
  },
  buttonText: {
    color: 'white',
    fontSize: 18,
    fontWeight: 'bold',
  },
  greetingContainer: {
    marginTop: 30,
    padding: 20,
    backgroundColor: '#2ecc71',
    borderRadius: 10,
  },
  greetingText: {
    fontSize: 20,
    color: 'white',
    fontWeight: 'bold',
    textAlign: 'center',
  },
  counterText: {
    marginTop: 20,
    fontSize: 16,
    color: '#7f8c8d',
  },
});  
```
</details>

**Try it on your phone**: What happens with the keyboard? Try the props `autoCapitalize="words"`, `returnKeyType="done"` and `onSubmitEditing={handleGreeting}` on the `TextInput`.

### Exercise 5b: State as an Object

<div class="exercise">

**Goal**: One state object for a form with several fields — immutable update with spread

**Requirements**:
1. Add a second `TextInput` for **email** (`keyboardType="email-address"`, `autoCapitalize="none"`)
2. Replace the `name` state with **one** object: `const [form, setForm] = useState({ name: '', email: '' })`
3. Each `TextInput` updates **only** its own field, without losing the other one
4. The greeting becomes: `Hello, Eleni (eleni@ionio.gr)!` — if the email is missing, just the name

</div>

<details>
<summary>Sample solution</summary>

```jsx
const [form, setForm] = useState({ name: '', email: '' });

const updateField = (field, value) =>
  setForm(prev => ({ ...prev, [field]: value }));

// ...
<TextInput
  style={styles.input}
  placeholder="Your name..."
  value={form.name}
  onChangeText={text => updateField('name', text)}
/>
<TextInput
  style={styles.input}
  placeholder="Email"
  value={form.email}
  onChangeText={text => updateField('email', text)}
  keyboardType="email-address"
  autoCapitalize="none"
/>

// in handleGreeting:
const name = form.name.trim();
const email = form.email.trim();
setGreeting(email ? `Hello, ${name} (${email})!` : `Hello, ${name}!`);
```
</details>

**Question**: What would happen with `setForm({ email: text })` (without `...prev`)? Try it.

<div class="tip">

In Exercise 6 you can continue either with the `name` state of Exercise 5 or with `form` — the Exercise 6 solution uses `name`.

</div>

---

## Theory: React Hooks in Depth

### useState Hook:
```jsx
const [state, setState] = useState(initialValue);
```

**How it works**:
1. React keeps the state between re-renders
2. When `setState` is called, React re-renders the component
3. The new state value is used in the next render

### Example with multiple states:
```jsx
const [name, setName] = useState('John');
const [age, setAge] = useState(25);
const [isActive, setIsActive] = useState(true);

// Update states
setName('Maria');
setAge(prevAge => prevAge + 1);  // Functional update
setIsActive(!isActive);           // Toggle
```

<div class="tip">

**Best Practice**: Use separate state variables for different data

</div>

---

### State Update Flow:
```
User Action (onPress, onChangeText)
  ↓
Event Handler Function
  ↓
setState() called
  ↓
React schedules re-render
  ↓
Component function runs again
  ↓
New JSX with updated state
  ↓
Virtual DOM diffing
  ↓
Update Real DOM/Native Views
```

**Question**: In Exercise 5, why do we write `Click #${count + 1}` and not `Click #${count}` right after `setCount(count + 1)`?

---

## Exercise 6: A List from State (Arrays)

<div class="exercise">

**Goal**: Store many values in state and show them as a list

**Requirements** (building on the app of Exercise 5):
1. Every time "Greet" is pressed with a non-empty name, the name is added to a `history` array
2. Below the greeting, show the list of all names (newest first)
3. After adding, the `TextInput` is cleared
4. A "Clear" button empties the list (shown only when the list is not empty)

</div>

<div class="tip">

**Immutability**: Never `history.push(...)`! We create a **new** array:  
`setHistory(prev => [newItem, ...prev])`  
**key**: Every list item needs a unique `key` prop

</div>

<details>
<summary>Sample solution</summary>

```jsx
const [history, setHistory] = useState([]);

const handleGreeting = () => {
  const trimmed = name.trim();
  if (!trimmed) {
    setGreeting('Please enter your name!');
    return;
  }
  setGreeting(`Hello, ${trimmed}!`);
  setHistory(prev => [{ id: Date.now().toString(), name: trimmed }, ...prev]);
  setName('');
};

// ...
{history.length > 0 && (
  <View style={styles.historyContainer}>
    <Text style={styles.label}>History ({history.length}):</Text>
    {history.map(item => (
      <Text key={item.id} style={styles.historyItem}>• {item.name}</Text>
    ))}
    <TouchableOpacity onPress={() => setHistory([])}>
      <Text style={styles.clearText}>Clear</Text>
    </TouchableOpacity>
  </View>
)}

// styles
historyContainer: {
  marginTop: 20,
  width: '100%',
  maxWidth: 400,
},
historyItem: {
  fontSize: 16,
  color: '#2c3e50',
  paddingVertical: 4,
},
clearText: {
  color: '#e74c3c',
  marginTop: 10,
  fontWeight: '600',
},
```
</details>

**Question**: What happens when the list grows so long that it does not fit on screen? (We will solve this with `ScrollView` / `FlatList` in the next lab.)

---

## 🎯 Bonus Challenge: Multiple Components

<div class="exercise">

**Advanced Exercise**: Refactoring into components

**Goal**: Split the app into reusable components

**Components to create**:
1. `GreetingInput` - TextInput with a label
2. `GreetingButton` - Custom styled button
3. `GreetingDisplay` - Display area for the greeting
4. `Counter` - Counter display component

**Concept**: Component composition & passing props

</div>

**Hint**: Each component is a function that takes props:
```jsx
function GreetingInput({ value, onChangeText }) {
  return (/* JSX */);
}
```

---

## Bonus Challenge: Component Structure

```
App
├── GreetingInput (props: value, onChangeText, label)
│   ├── Text (label)
│   └── TextInput
│
├── GreetingButton (props: onPress, title)
│   └── TouchableOpacity
│       └── Text
│
├── GreetingDisplay (props: greeting, visible)
│   └── View (conditional render)
│       └── Text
│
└── Counter (props: count)
    └── Text
```

**Benefits**:
- ✓ Reusability
- ✓ Separation of concerns
- ✓ Easier testing
- ✓ Better organization

**Note**: The state stays in `App` — the children receive values and callbacks through props.

---

## Common Troubleshooting Issues

### Issue 1: Cannot connect Expo Go to dev server
- Make sure you are on the same WiFi network
- Disable VPN
- Check firewall settings
- Try tunnel mode: `npx expo start --tunnel`

### Issue 2: "Project is incompatible with this version of Expo Go"
- Update Expo Go from the App Store / Play Store
- or update the project: `npx expo install expo@latest --fix`

### Issue 3: Slow performance on Expo Go
- Expected in debug mode
- Production builds are much faster
- Use: `npx expo start --no-dev --minify`

### Issue 4: "Text strings must be rendered within a <Text> component"
- Some text (or a space/semicolon) sits directly inside a `<View>`
- Common cause: `{count && <Text>...</Text>}` when `count === 0` — use `{count > 0 && ...}`

### Issue 5: "Each child in a list should have a unique key prop"
- Add a unique `key` to the element returned by `map()`

---

## Checklist

**Mobile Testing**:
- [ ] App loads on Expo Go
- [ ] Touch interactions work
- [ ] Keyboard shows/hides properly
- [ ] Layout looks good on device

**Functionality**:
- [ ] Buttons respond to clicks (`+`, `−`, Reset)
- [ ] The counter never goes negative or above the limit
- [ ] Input accepts text (including accented / non-Latin characters)
- [ ] The form with a state object (Exercise 5b) does not lose fields
- [ ] State updates reflect in UI
- [ ] The history list is added to & cleared correctly

**Code Quality**:
- [ ] No console errors / warnings
- [ ] No unused variables
- [ ] Proper indentation
- [ ] Meaningful variable names

---
