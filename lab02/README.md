
# React Native Lab Exercises 1
## Εργαστηριακές Ασκήσεις Setup & Development

## Άσκηση 1: Εγκατάσταση Node.js & NPM

<div class="exercise">

**Στόχος**: Εγκατάσταση Node.js και verification του NPM

**Βήματα**:
```bash
# 1. Εγκατάσταση Node.js (LTS version)
### Official Installer (Προτεινόμενη)
### - Download από [nodejs.org](https://nodejs.org/)
### - Προτείνεται η **LTS version** (Long Term Support)


# 2. Verification
node --version    # Πρέπει να δείτε: v20.x.x ή νεότερο
npm --version     # Πρέπει να δείτε: 10.x.x ή νεότερο

# 3. Test Node.js REPL
node
> console.log("Hello from Node.js!")
> .exit

# 4. Test NPM
npm help
```

</div>

**Σημειώστε**: Τα version numbers που βλέπετε

---

## Άσκηση 2: Node.js Basics & First Script

### Δημιουργία του πρώτου Node.js script:

```bash
# 1. Δημιουργία directory για το lab
mkdir ~/ReactNativeLab
cd ~/ReactNativeLab

# 2. Δημιουργία test script
cat > hello.js << 'EOF'
// hello.js - First Node.js script
console.log("Hello from Node.js!");
console.log("Current directory:", __dirname);
console.log("Node version:", process.version);

const sum = (a, b) => a + b;
console.log("Sum of 5 + 3 =", sum(5, 3));
EOF

# 3. Εκτέλεση
node hello.js
```

**Αναμενόμενο αποτέλεσμα**: Πρέπει να δείτε τα αντίστοιχα μηνύματα στο τερματικό σας

---

## Άσκηση 3: NPM Package Management

<div class="exercise">

**Στόχος**: Κατανόηση NPM και δημιουργία `package.json`

**Βήματα**:
```bash
# 1. Δημιουργία νέου project directory
mkdir ~/ReactNativeLab/npm-test
cd ~/ReactNativeLab/npm-test

# 2. Initialize NPM project
npm init -y

# 3. Εξέταση του package.json
cat package.json

# 4. Εγκατάσταση test package
npm install chalk

# 5. Δημιουργία script που χρησιμοποιεί το package
cat > app.js << 'EOF'
const chalk = require('chalk');
console.log(chalk.blue('Hello in blue!'));
console.log(chalk.red.bold('Error message in red!'));
EOF

# 6. Εκτέλεση
node app.js
```

</div>

---

## Άσκηση 4: Κατανόηση package.json

### Ανατομία του package.json:
```json
{
  "name": "npm-test",           // Project name
  "version": "1.0.0",            // Semantic versioning
  "description": "",             // Project description
  "main": "index.js",            // Entry point
  "scripts": {                   // NPM scripts
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],                // Search keywords
  "author": "",                  // Your name
  "license": "ISC",              // License type
  "dependencies": {              // Production dependencies
    "chalk": "^4.1.2"
  }
}
```

<div class="tip">

**Versioning**: `^4.1.2` = Compatible με 4.x.x (όχι 5.0.0)  
**node_modules/**: Περιέχει downloaded packages (μην τα κάνετε commit σε repo)

</div>

---

## Άσκηση 5: Εγκατάσταση Expo CLI

<div class="exercise">

**Στόχος**: Εγκατάσταση Expo CLI και δημιουργία πρώτου project

**Βήματα**:
```bash
# 1. Εγκατάσταση Expo CLI globally
npm install -g expo-cli

# 2. Verification
expo --version

# 3. Δημιουργία νέου Expo project
cd ~/ReactNativeLab
expo init MyFirstApp

# Επιλέξτε: blank (θα χρησιμοποιήσουμε arrow keys)
# Project name: MyFirstApp

# 4. Είσοδος στο project
cd MyFirstApp

# 5. Εξέταση της δομής
ls -la
```

</div>

---

## Άσκηση 6: Θεωρία - Expo Project Structure

### Δομή του Expo Project:
```
MyFirstApp/
├── .expo/                   # Expo configuration
├── .expo-shared/            # Shared Expo settings
├── assets/                  # Images, fonts, etc.
│   ├── icon.png
│   └── splash.png
├── node_modules/            # NPM dependencies
├── App.js                   # >>> Main component <<<
├── app.json                 # App configuration
├── babel.config.js          # Babel transpiler config
├── package.json             # Dependencies & scripts
└── package-lock.json        # Lock file for dependencies
```

**Κύρια αρχεία**:
- `App.js`: Entry point της εφαρμογής
- `app.json`: Metadata (name, version, orientation, etc.)
- `package.json`: Scripts & dependencies

---

## Άσκηση 7: Εξέταση & Κατανόηση App.js

<div class="exercise">

**Στόχος**: Ανάλυση του default Expo App.js

**Βήματα**:
```bash
# 1. Άνοιγμα του App.js
cd ~/ReactNativeLab/MyFirstApp
cat App.js
```

**Ερωτήσεις**:
1. Ποια components χρησιμοποιούνται; 
2. Πού ορίζεται το styling; 
3. Τι είναι το `export default`; 
4. Πώς το component επιστρέφει JSX; 

</div>

---

## Άσκηση 7b: Default App.js Ανάλυση

```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Open up App.js to start working on your app!</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

**Ανάλυση components**:
- `View`: Container (like `<div>` στο HTML)
- `Text`: Text element (όλο το text πρέπει να είναι σε `<Text>`)
- `StyleSheet.create()`: Performance-optimized styles

---

## Άσκηση 8: Εκτέλεση της Εφαρμογής

<div class="exercise">

**Στόχος**: Εκτέλεση της εφαρμογής σε browser (Expo web)

**Βήματα**:
```bash
# 1. Εκκίνηση Expo development server
cd ~/ReactNativeLab/MyFirstApp
npm start

# ή
expo start

# 2. Στο menu που εμφανίζεται στο terminal:
#    Πατήστε 'w' για web browser

# 3. Θα ανοίξει browser window αυτόματα
#    URL: http://localhost:19006

# 4. Παρατηρήστε:
#    - Το κείμενο "Open up App.js to start working..."
#    - Το layout (centered)
#    - Development tools στο browser
```

</div>

---

## Άσκηση 8b: Expo Dev Tools

### Όταν τρέξετε `expo start`, βλέπετε:

```
Starting Metro Bundler

› Metro waiting on exp://192.168.1.x:19000
› Scan the QR code above with Expo Go (Android) or the Camera app (iOS)

› Press a │ open Android
› Press i │ open iOS simulator
› Press w │ open web

› Press r │ reload app
› Press m │ toggle menu
› Press d │ show developer menu
```

**Browser Dev Tools** (http://localhost:19000):
- 📱 QR Code για mobile testing
- 🌐 Εκτέλεση σε web
- 📊 Connection logs
- ⚙️ Configuration options

---

## Άσκηση 9: Τροποποίηση της Εφαρμογής

<div class="exercise">

**Στόχος**: Αλλαγή του default App.js και παρατήρηση hot reloading

**Βήματα**:
```bash
# 1. Άνοιγμα του App.js σε editor
code App.js  # ή χρησιμοποιήστε άλλο editor

# 2. Αλλάξτε το Text σε:
<Text style={styles.title}>Καλώς ήρθατε στο React Native!</Text>
<Text style={styles.subtitle}>Mobile App Development Lab</Text>

# 3. Προσθέστε styles για το title και subtitle:
title: {
  fontSize: 24,
  fontWeight: 'bold',
  color: '#2c3e50',
  marginBottom: 10,
},
subtitle: {
  fontSize: 16,
  color: '#7f8c8d',
},

# 4. Αποθήκευση (Cmd+S)
# 5. Παρατηρήστε το automatic reload στο browser
```

</div>

---

## Άσκηση 9b: Complete Modified Code

```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Καλώς ήρθατε στο React Native!</Text>
      <Text style={styles.subtitle}>Mobile App Development Lab</Text>
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
  },
});
```

---

## Άσκηση 10: Προσθήκη Button Component

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

## Άσκηση 10: Complete Code με Button

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

## Άσκηση 10: Styles για Button

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

## Άσκηση 11: Εγκατάσταση Expo Go App

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
2. Run `expo start` στον υπολογιστή
3. Scan το QR code με:
   - iOS: Camera app
   - Android: Expo Go app

</div>

---

## Άσκηση 11: Θεωρία - Expo Architecture

### Πώς λειτουργεί το Expo:

```
Development Machine                Mobile Device
┌─────────────────────┐            ┌────────────┐
│ Metro Bundler       │            │ Expo Go App│
│ ↓                   │   WiFi     │            │
│ JavaScript Bundle   │ ─────────> │ JavaScript │
│ ↓                   │            │ Engine     │
│ Expo Dev Server     │            │ ↓          │
│ (Port 19000)        │            │ Native     │
└─────────────────────┘            │ Components │
                                   └────────────┘
```

**Key points**:
- **Metro Bundler**: Transpiles JSX → JavaScript
- **Expo Go**: Container app με pre-built native modules
- **Over-the-air**: JavaScript bundle sent over network
- **No compilation**: Instant updates without rebuild

---

## Άσκηση 12: Advanced Features - TextInput

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

---

## Common Troubleshooting Issues

### Issue 1: "Command not found: expo"
```bash
# Solution:
npm install -g expo-cli
# ή
npx expo start  # Use npx instead
```

### Issue 2: Metro bundler fails to start
```bash
# Solution:
watchman watch-del-all
rm -rf node_modules
npm install
expo start --clear
```

### Issue 3: Cannot connect Expo Go to dev server
- Βεβαιωθείτε ότι είστε στο ίδιο WiFi network
- Disable VPN
- Check firewall settings
- Try tunnel mode: `expo start --tunnel`

### Issue 4: "Unable to resolve module"
```bash
# Solution:
rm -rf node_modules package-lock.json
npm install
expo start --clear
```

### Issue 5: Slow performance on Expo Go
- Αναμενόμενο σε debug mode
- Production builds είναι πολύ ταχύτερα
- Use: `expo start --no-dev --minify`

### Issue 6: Hot reload not working
```bash
# Solution:
# Press 'r' in terminal to reload manually
# ή
expo start --clear
```

---

## Checklist

**Browser Testing**:
- [ ] App loads without errors
- [ ] All text displays correctly (Greek characters)
- [ ] Buttons respond to clicks
- [ ] Input accepts text
- [ ] State updates reflect in UI
- [ ] Hot reload works

**Mobile Testing** (optional):
- [ ] App loads on Expo Go
- [ ] Touch interactions work
- [ ] Keyboard shows/hides properly
- [ ] Layout looks good on device

**Code Quality**:
- [ ] No console errors
- [ ] No unused variables
- [ ] Proper indentation
- [ ] Meaningful variable names

---