
# React Native Lab Exercises 3
## Εργαστηριακές Ασκήσεις: State, Interaction & Expo Go

**Προαπαιτούμενα**: Ολοκληρωμένο το [Lab 02](../lab02/README.md) — λειτουργικό Node.js/NPM και το project `~/ReactNativeLab/firstapp` να τρέχει στον browser.

**Στόχοι του εργαστηρίου**:
- ✅ Εκτέλεση της εφαρμογής σε πραγματική συσκευή με Expo Go
- ✅ State management με το `useState` hook
- ✅ Χειρισμός touch events (`onPress`) και text input (`onChangeText`)
- ✅ Conditional rendering & conditional styling
- ✅ Rendering λιστών από arrays (`map` + `key`)

```bash
# Ξεκινάμε από το project του Lab 02
cd ~/ReactNativeLab/firstapp
npx expo start
```

---

## Άσκηση 1: Εγκατάσταση Expo Go App

<div class="exercise">

**Στόχος**: Δοκιμή της εφαρμογής σε πραγματική συσκευή

**Βήματα**:

**Για iOS**:
1. Άνοιγμα App Store στο iPhone/iPad
2. Αναζήτηση "Expo Go"
3. Download & εγκατάσταση (free app)

**Για Android**:
1. Άνοιγμα Google Play Store
2. Αναζήτηση "Expo Go"
3. Download & εγκατάσταση

**Testing**:
1. Βεβαιωθείτε ότι η συσκευή και ο υπολογιστής είναι στο **ίδιο WiFi network**
2. Run `npx expo start` στον υπολογιστή
3. Scan το QR code με:
   - iOS: Camera app
   - Android: Expo Go app

</div>

<div class="tip">

**Δίκτυο πανεπιστημίου**: Αν η συσκευή δεν συνδέεται (συχνό σε δημόσια WiFi), δοκιμάστε `npx expo start --tunnel`  
**Έκδοση SDK**: Το Expo Go υποστηρίζει μόνο την τελευταία έκδοση του Expo SDK. Αν δείτε μήνυμα ασυμβατότητας, ενημερώστε το Expo Go ή το project (`npx expo install expo@latest --fix`)

</div>

Από εδώ και πέρα, δοκιμάζετε κάθε άσκηση **και** στον browser **και** στο κινητό.

---

## Άσκηση 1b: Θεωρία - Expo Architecture

### Πώς λειτουργεί το Expo:

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
- **Expo Go**: Container app με pre-built native modules
- **Over-the-air**: JavaScript bundle sent over network
- **No compilation**: Instant updates without rebuild

**Ερώτηση**: Συγκρίνετε την εμφάνιση της εφαρμογής στον browser και στο κινητό. Τι διαφορές παρατηρείτε (fonts, status bar, μεγέθη);

---

## Άσκηση 2: Προσθήκη Button Component

<div class="exercise">

**Στόχος**: Προσθήκη interactive button με state management

**Βήματα**:
1. Import useState hook και TouchableOpacity
2. Δημιουργία state variable για counter
3. Προσθήκη button που αυξάνει το counter
4. Display του counter value

</div>

**Νέα concepts**:
- **useState**: React Hook για state management
- **TouchableOpacity**: Touchable button component
- **onPress**: Event handler για touch events

---

## Άσκηση 2b: Complete Code με Button

```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View, TouchableOpacity } from 'react-native';
import { useState } from 'react';

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Καλώς ήρθατε στο React Native!</Text>
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

## Άσκηση 2c: Styles για Button

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

## Άσκηση 3: Επέκταση του Counter

<div class="exercise">

**Στόχος**: Περισσότερα buttons που αλλάζουν το ίδιο state

**Ζητούμενα**:
1. Προσθέστε button **`−`** που μειώνει το counter
2. Προσθέστε button **Reset** που το μηδενίζει
3. Ο counter **δεν** πρέπει να γίνεται αρνητικός
4. Τοποθετήστε τα τρία buttons στην ίδια γραμμή (`flexDirection: 'row'`)

</div>

<div class="tip">

**Functional update**: Όταν το νέο state εξαρτάται από το προηγούμενο, προτιμήστε  
`setCount(prev => prev + 1)` αντί για `setCount(count + 1)`

</div>

<details>
<summary>Ενδεικτική λύση</summary>

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

## Άσκηση 4: Conditional Styling & Rendering

<div class="exercise">

**Στόχος**: Το UI αλλάζει ανάλογα με την τιμή του state

**Ζητούμενα**:
1. Το χρώμα του counter να είναι:
   - γκρι όταν `count === 0`
   - πράσινο όταν `count` είναι ζυγός
   - κόκκινο όταν `count` είναι μονός
2. Όταν ο counter φτάσει το **10**, εμφανίστε το μήνυμα "🎉 Φτάσατε το όριο!" και απενεργοποιήστε το `+` (prop `disabled`)
3. Το απενεργοποιημένο button να φαίνεται διαφορετικά (π.χ. `opacity: 0.4`)

</div>

**Νέα concepts**:
- **Style arrays**: `style={[styles.counterText, { color }]}` — τα επόμενα styles υπερισχύουν
- **Conditional rendering**: `{condition && <Text>...</Text>}` ή `{condition ? <A /> : <B />}`
- **disabled** prop στο `TouchableOpacity`

<details>
<summary>Ενδεικτική λύση</summary>

```jsx
const MAX = 10;
const atMax = count >= MAX;

const counterColor =
  count === 0 ? '#95a5a6' : count % 2 === 0 ? '#27ae60' : '#e74c3c';

// ...
<Text style={[styles.counterText, { color: counterColor }]}>
  Counter: {count}
</Text>

{atMax && <Text style={styles.limitText}>🎉 Φτάσατε το όριο!</Text>}

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

## Άσκηση 5: Advanced Features - TextInput

<div class="exercise">

**Στόχος**: Προσθήκη text input για user interaction

**Νέα features**:
- TextInput component
- State management για text
- Dynamic content based on input
- Multiple state variables

**Functionality**:
Δημιουργήστε μια εφαρμογή που:
1. Έχει text input για το όνομα του χρήστη
2. Button που εμφανίζει personalized greeting
3. Counter που μετράει πόσες φορές πατήθηκε το button

</div>

<details>
<summary>Ενδεικτική λύση</summary>

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
      setGreeting(`Γεια σου, ${name}! (Click #${count + 1})`);
    } else {
      setGreeting('Παρακαλώ εισάγετε το όνομά σας!');
    }
  };
  return (
    <View style={styles.container}>
      <Text style={styles.title}>React Native Lab Exercise</Text>
      
      <View style={styles.inputContainer}>
        <Text style={styles.label}>Εισάγετε το όνομά σας:</Text>
        <TextInput
          style={styles.input}
          placeholder="Το όνομά σας..."
          value={name}
          onChangeText={setName}
        />
      </View>
      
      <TouchableOpacity style={styles.button} onPress={handleGreeting}>
        <Text style={styles.buttonText}>Χαιρετισμός</Text>
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

**Δοκιμάστε στο κινητό**: Τι συμβαίνει με το πληκτρολόγιο; Δοκιμάστε τα props `autoCapitalize="words"`, `returnKeyType="done"` και `onSubmitEditing={handleGreeting}` στο `TextInput`.

---

## Θεωρία: React Hooks Εμβάθυνση

### useState Hook:
```jsx
const [state, setState] = useState(initialValue);
```

**Πώς λειτουργεί**:
1. React κρατάει το state μεταξύ re-renders
2. Όταν καλείται `setState`, React re-renders το component
3. Το νέο state value χρησιμοποιείται στο επόμενο render

### Παράδειγμα με πολλαπλά states:
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

**Best Practice**: Χρησιμοποιήστε separate state variables για διαφορετικά data

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

**Ερώτηση**: Στην Άσκηση 5, γιατί γράφουμε `Click #${count + 1}` και όχι `Click #${count}` αμέσως μετά το `setCount(count + 1)`;

---

## Άσκηση 6: Λίστα από State (Arrays)

<div class="exercise">

**Στόχος**: Αποθήκευση πολλών τιμών σε state και εμφάνισή τους ως λίστα

**Ζητούμενα** (πάνω στην εφαρμογή της Άσκησης 5):
1. Κάθε φορά που πατιέται το "Χαιρετισμός" με μη κενό όνομα, το όνομα προστίθεται σε ένα array `history`
2. Κάτω από το greeting εμφανίζεται η λίστα με όλα τα ονόματα (νεότερο πρώτο)
3. Μετά την προσθήκη, το `TextInput` αδειάζει
4. Button "Καθαρισμός" που αδειάζει τη λίστα (εμφανίζεται μόνο όταν η λίστα δεν είναι κενή)

</div>

<div class="tip">

**Immutability**: Ποτέ `history.push(...)`! Δημιουργούμε **νέο** array:  
`setHistory(prev => [newItem, ...prev])`  
**key**: Κάθε στοιχείο λίστας χρειάζεται μοναδικό `key` prop

</div>

<details>
<summary>Ενδεικτική λύση</summary>

```jsx
const [history, setHistory] = useState([]);

const handleGreeting = () => {
  const trimmed = name.trim();
  if (!trimmed) {
    setGreeting('Παρακαλώ εισάγετε το όνομά σας!');
    return;
  }
  setGreeting(`Γεια σου, ${trimmed}!`);
  setHistory(prev => [{ id: Date.now().toString(), name: trimmed }, ...prev]);
  setName('');
};

// ...
{history.length > 0 && (
  <View style={styles.historyContainer}>
    <Text style={styles.label}>Ιστορικό ({history.length}):</Text>
    {history.map(item => (
      <Text key={item.id} style={styles.historyItem}>• {item.name}</Text>
    ))}
    <TouchableOpacity onPress={() => setHistory([])}>
      <Text style={styles.clearText}>Καθαρισμός</Text>
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

**Ερώτηση**: Τι γίνεται όταν η λίστα μεγαλώσει πολύ και δεν χωράει στην οθόνη; (Θα το λύσουμε με `ScrollView` / `FlatList` στο επόμενο εργαστήριο.)

---

## 🎯 Bonus Challenge: Multiple Components

<div class="exercise">

**Προχωρημένη Άσκηση**: Refactoring σε components

**Στόχος**: Διαίρεση της εφαρμογής σε reusable components

**Components προς δημιουργία**:
1. `GreetingInput` - TextInput με label
2. `GreetingButton` - Custom styled button
3. `GreetingDisplay` - Display area για greeting
4. `Counter` - Counter display component

**Concept**: Component composition & props passing

</div>

**Hint**: Κάθε component θα είναι function που παίρνει props:
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

**Σημείωση**: Το state παραμένει στο `App` — τα παιδιά λαμβάνουν τιμές και callbacks μέσω props.

---

## Common Troubleshooting Issues

### Issue 1: Cannot connect Expo Go to dev server
- Βεβαιωθείτε ότι είστε στο ίδιο WiFi network
- Disable VPN
- Check firewall settings
- Try tunnel mode: `npx expo start --tunnel`

### Issue 2: "Project is incompatible with this version of Expo Go"
- Ενημερώστε το Expo Go από το App Store / Play Store
- ή ενημερώστε το project: `npx expo install expo@latest --fix`

### Issue 3: Slow performance on Expo Go
- Αναμενόμενο σε debug mode
- Production builds είναι πολύ ταχύτερα
- Use: `npx expo start --no-dev --minify`

### Issue 4: "Text strings must be rendered within a <Text> component"
- Κάποιο κείμενο (ή κενό/ερωτηματικό) βρίσκεται απευθείας μέσα σε `<View>`
- Συχνή αιτία: `{count && <Text>...</Text>}` όταν `count === 0` — χρησιμοποιήστε `{count > 0 && ...}`

### Issue 5: "Each child in a list should have a unique key prop"
- Προσθέστε μοναδικό `key` στο στοιχείο που επιστρέφει το `map()`

---

## Checklist

**Mobile Testing**:
- [ ] App loads on Expo Go
- [ ] Touch interactions work
- [ ] Keyboard shows/hides properly
- [ ] Layout looks good on device

**Λειτουργικότητα**:
- [ ] Buttons respond to clicks (`+`, `−`, Reset)
- [ ] Ο counter δεν γίνεται αρνητικός ούτε ξεπερνά το όριο
- [ ] Input accepts text (Greek characters)
- [ ] State updates reflect in UI
- [ ] Η λίστα ιστορικού προστίθεται & καθαρίζεται σωστά

**Code Quality**:
- [ ] No console errors / warnings
- [ ] No unused variables
- [ ] Proper indentation
- [ ] Meaningful variable names

---
