# React Native Lab Exercises 4
## Εργαστηριακές Ασκήσεις: Components, Λίστες & useEffect

**Προαπαιτούμενα**: Ολοκληρωμένο το [Lab 03](../lab03/README.md) — `useState`, `onPress`, `TextInput`, rendering λίστας με `map` + `key`.

**Στόχοι του εργαστηρίου**:
- ✅ Δημιουργία δικών μας components σε ξεχωριστά αρχεία & χρήση **props**
- ✅ Λίστες με `ScrollView` και `FlatList` (και πότε χρησιμοποιούμε το καθένα)
- ✅ Safe areas (notch, status bar) με `react-native-safe-area-context`
- ✅ Side effects με το `useEffect` hook (dependency array, cleanup)
- ✅ Ανάκτηση δεδομένων από REST API με `fetch` (loading / error states)
- ✅ Αναζήτηση (derived state) & pull-to-refresh

Σε αυτό το εργαστήριο φτιάχνουμε μια εφαρμογή **Users** που φέρνει χρήστες από το API που είδαμε στο Lab 01 (`jsonplaceholder.typicode.com`). Στο Lab 05 θα της προσθέσουμε navigation.

---

## Άσκηση 0: Νέο project

<div class="exercise">

**Στόχος**: Δημιουργία του project `usersapp`

</div>

```bash
cd ~/ReactNativeLab
npx create-expo-app@latest usersapp --template blank
cd usersapp

# Βιβλιοθήκη για safe areas (notch, status bar, home indicator)
npx expo install react-native-safe-area-context

npx expo start
```

<div class="tip">

**`npx expo install`** αντί για `npm install`: επιλέγει αυτόματα την έκδοση του package που είναι **συμβατή με το Expo SDK** του project σας. Χρησιμοποιήστε το για κάθε package που έχει native κώδικα.

</div>

Ανοίξτε την εφαρμογή στον browser (`w`) **και** στο Expo Go (QR code), όπως στο Lab 03.

---

## Άσκηση 1: Πρώτο component με props

<div class="exercise">

**Στόχος**: Ένα επαναχρησιμοποιήσιμο component σε δικό του αρχείο

**Βήματα**:
1. Δημιουργήστε φάκελο `components/` στη ρίζα του project
2. Δημιουργήστε το αρχείο `components/UserCard.js` (κώδικας παρακάτω)
3. Στο `App.js` κάντε `import UserCard from './components/UserCard';`
4. Εμφανίστε **τρεις** κάρτες με διαφορετικά props

</div>

```jsx
// components/UserCard.js
import { StyleSheet, Text, View } from 'react-native';

export default function UserCard({ name, email, city }) {
  return (
    <View style={styles.card}>
      <View style={styles.avatar}>
        <Text style={styles.avatarText}>{name.charAt(0)}</Text>
      </View>
      <View style={styles.info}>
        <Text style={styles.name}>{name}</Text>
        <Text style={styles.email}>{email}</Text>
        {city ? <Text style={styles.city}>📍 {city}</Text> : null}
      </View>
    </View>
  );
}
```

```jsx
// App.js (μέσα στο return)
<UserCard name="Μαρία Παπαδοπούλου" email="maria@ionio.gr" city="Κέρκυρα" />
<UserCard name="Νίκος Γεωργίου" email="nikos@ionio.gr" city="Λευκάδα" />
<UserCard name="Ελένη Δήμου" email="eleni@ionio.gr" />
```

<details>
<summary>Styles για το UserCard</summary>

```jsx
const styles = StyleSheet.create({
  card: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: 'white',
    padding: 15,
    borderRadius: 10,
    marginHorizontal: 15,
    marginVertical: 6,
    elevation: 2, // Android shadow
    shadowColor: '#000', // iOS shadow
    shadowOpacity: 0.1,
    shadowRadius: 4,
    shadowOffset: { width: 0, height: 2 },
  },
  avatar: {
    width: 48,
    height: 48,
    borderRadius: 24,
    backgroundColor: '#3498db',
    alignItems: 'center',
    justifyContent: 'center',
    marginRight: 15,
  },
  avatarText: {
    color: 'white',
    fontSize: 20,
    fontWeight: 'bold',
  },
  info: {
    flex: 1,
  },
  name: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#2c3e50',
  },
  email: {
    fontSize: 14,
    color: '#7f8c8d',
    marginTop: 2,
  },
  city: {
    fontSize: 14,
    color: '#34495e',
    marginTop: 4,
  },
});
```
</details>

**Νέα concepts**:
- **Props**: παράμετροι που περνάει ο parent στο child — `function UserCard({ name, email, city })` (destructuring, Lab 01)
- **Unidirectional data flow**: τα δεδομένα ρέουν από parent → child. Το child **δεν** αλλάζει τα props του
- **Optional prop**: η τρίτη κάρτα δεν έχει `city` — γι' αυτό το `{city ? ... : null}`

**Ερώτηση**: Ποια η διαφορά ανάμεσα σε **props** και **state**; Ποιο από τα δύο θα χρησιμοποιούσατε για να "ανοίγει" μια κάρτα και να δείχνει περισσότερες πληροφορίες;

---

## Άσκηση 2: Λίστα με ScrollView & Safe Areas

<div class="exercise">

**Στόχος**: Εμφάνιση πολλών χρηστών από ένα array, με scrolling

**Ζητούμενα**:
1. Ορίστε ένα array `USERS` (έξω από το component) με **τουλάχιστον 12** χρήστες `{ id, name, email, city }`
2. Εμφανίστε τους με `map` μέσα σε `ScrollView`
3. Τυλίξτε την εφαρμογή σε `SafeAreaProvider` / `SafeAreaView` ώστε ο τίτλος να μην κρύβεται πίσω από το notch / status bar
4. Δοκιμάστε στο κινητό: γίνεται scroll; Κρύβεται κάτι;

</div>

```jsx
import { StatusBar } from 'expo-status-bar';
import { ScrollView, StyleSheet, Text } from 'react-native';
import { SafeAreaProvider, SafeAreaView } from 'react-native-safe-area-context';
import UserCard from './components/UserCard';

const USERS = [
  { id: 1, name: 'Μαρία Παπαδοπούλου', email: 'maria@ionio.gr', city: 'Κέρκυρα' },
  { id: 2, name: 'Νίκος Γεωργίου', email: 'nikos@ionio.gr', city: 'Λευκάδα' },
  { id: 3, name: 'Ελένη Δήμου', email: 'eleni@ionio.gr', city: 'Ζάκυνθος' },
  // ... προσθέστε κι άλλους
];

export default function App() {
  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.title}>Χρήστες</Text>
        <ScrollView>
          {USERS.map(user => (
            <UserCard key={user.id} name={user.name} email={user.email} city={user.city} />
          ))}
        </ScrollView>
        <StatusBar style="auto" />
      </SafeAreaView>
    </SafeAreaProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#2c3e50',
    margin: 15,
  },
});
```

<div class="tip">

**SafeAreaView**: Χρησιμοποιούμε το `SafeAreaView` από το **`react-native-safe-area-context`** και όχι από το `react-native` — το built-in είναι deprecated και δουλεύει μόνο σε iOS.  
**key**: Όπως στο Lab 03, κάθε στοιχείο του `map` χρειάζεται μοναδικό `key`.

</div>

### Άσκηση 2b: Χειροκίνητα insets με `useSafeAreaInsets`

<div class="exercise">

**Στόχος**: Έλεγχος του padding μόνο όπου χρειάζεται, αντί για `SafeAreaView`

**Ζητούμενα**:
1. Αντικαταστήστε το `SafeAreaView` με ένα απλό `View`
2. Πάρτε τα insets με `useSafeAreaInsets()` και εφαρμόστε **μόνο** `paddingTop: insets.top` στον τίτλο-header
3. Δώστε στον header δικό του χρώμα φόντου (π.χ. `#3498db`) — παρατηρήστε ότι το χρώμα "απλώνεται" και πίσω από το status bar
4. Προσθέστε `paddingBottom: insets.bottom` στο `contentContainerStyle` του `ScrollView`, ώστε η τελευταία κάρτα να μην κρύβεται πίσω από το home indicator
5. Τυπώστε `console.log(insets)` και συγκρίνετε τις τιμές σε διαφορετικές συσκευές

</div>

<details>
<summary>Ενδεικτική λύση (App.js)</summary>

```jsx
import { StatusBar } from 'expo-status-bar';
import { ScrollView, StyleSheet, Text, View } from 'react-native';
import { SafeAreaProvider, useSafeAreaInsets } from 'react-native-safe-area-context';
import UserCard from './components/UserCard';

// const USERS = [ ... ] όπως στην Άσκηση 2

function UsersScreen() {
  const insets = useSafeAreaInsets();  // { top, bottom, left, right }
  console.log(insets);

  return (
    <View style={styles.container}>
      <View style={[styles.header, { paddingTop: insets.top }]}>
        <Text style={styles.title}>Χρήστες</Text>
      </View>
      <ScrollView contentContainerStyle={{ paddingBottom: insets.bottom }}>
        {USERS.map(user => (
          <UserCard key={user.id} name={user.name} email={user.email} city={user.city} />
        ))}
      </ScrollView>
      <StatusBar style="light" />
    </View>
  );
}

export default function App() {
  return (
    <SafeAreaProvider>
      <UsersScreen />
    </SafeAreaProvider>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#ecf0f1' },
  header: { backgroundColor: '#3498db' },
  title: { fontSize: 28, fontWeight: 'bold', color: '#fff', margin: 15 },
});
```

</details>

<div class="tip">

**Γιατί ξεχωριστό component;** Το `useSafeAreaInsets()` διαβάζει τιμές από το `SafeAreaProvider`, άρα πρέπει να καλείται σε component που βρίσκεται **μέσα** σε αυτό — όχι στο ίδιο το `App` που το κάνει render.

</div>

---

## Άσκηση 3: FlatList

<div class="exercise">

**Στόχος**: Αντικατάσταση του `ScrollView` + `map` με `FlatList`

**Ζητούμενα**:
1. Αντικαταστήστε το `ScrollView` με `FlatList`
2. Ορίστε τα props `data`, `keyExtractor` και `renderItem`
3. Προσθέστε `ListEmptyComponent` — δοκιμάστε με `data={[]}`
4. (Προαιρετικά) `ItemSeparatorComponent` ή `ListHeaderComponent`

</div>

```jsx
import { FlatList, StyleSheet, Text } from 'react-native';

// ...
<FlatList
  data={USERS}
  keyExtractor={item => item.id.toString()}
  renderItem={({ item }) => (
    <UserCard name={item.name} email={item.email} city={item.city} />
  )}
  ListEmptyComponent={<Text style={styles.empty}>Δεν βρέθηκαν χρήστες</Text>}
/>
```

**ScrollView vs FlatList**:

| `ScrollView` | `FlatList` |
|---|---|
| Κάνει render **όλα** τα children αμέσως | Κάνει render μόνο ό,τι φαίνεται (+ λίγα ακόμα) — **virtualization** |
| Κατάλληλο για λίγα / σταθερά στοιχεία (φόρμες, σελίδες) | Κατάλληλο για μεγάλες ή δυναμικές λίστες |
| `map` + `key` | `data` + `renderItem` + `keyExtractor` |
| — | Έτοιμα: empty state, header/footer, pull-to-refresh, infinite scroll |

**Ερώτηση**: Το `keyExtractor` πρέπει να επιστρέφει **string**. Γιατί χρησιμοποιούμε `item.id.toString()`; Γιατί **δεν** είναι καλή ιδέα να χρησιμοποιούμε το index του πίνακα ως key;

---

## Θεωρία: Το useEffect Hook

Τα components πρέπει να είναι **pure**: ίδια props/state → ίδιο JSX. Ό,τι "βγαίνει έξω" από το render είναι **side effect**: κλήσεις σε API, timers, subscriptions, logging.

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // 1. Setup: τρέχει ΜΕΤΑ το render
  return () => {
    // 2. Cleanup (προαιρετικό): πριν το επόμενο effect & στο unmount
  };
}, [dependencies]); // 3. Πότε ξανατρέχει
```

| Dependency array | Πότε τρέχει το effect |
|---|---|
| *(χωρίς array)* | Μετά από **κάθε** render — σπάνια το θέλουμε |
| `[]` | **Μία** φορά, μετά το πρώτο render (mount) |
| `[a, b]` | Στο mount **και** κάθε φορά που αλλάζει το `a` ή το `b` |

<div class="tip">

**Δεν χρειάζεστε effect** για τιμές που υπολογίζονται από props/state (π.χ. φιλτραρισμένη λίστα) — υπολογίστε τες απευθείας στο render (βλ. Άσκηση 6).

</div>

---

## Άσκηση 4: useEffect με timer & cleanup

<div class="exercise">

**Στόχος**: Κατανόηση mount / unmount και cleanup

**Βήματα**:
1. Δημιουργήστε το `components/Clock.js` (κώδικας παρακάτω)
2. Στο `App.js` προσθέστε state `showClock` και ένα button "Εμφάνιση / Απόκρυψη ρολογιού"
3. Εμφανίστε το `<Clock />` μόνο όταν `showClock === true`
4. Ανοίξτε τα logs (terminal του `npx expo start` ή browser console) και πατήστε το button μερικές φορές

</div>

```jsx
// components/Clock.js
import { useEffect, useState } from 'react';
import { StyleSheet, Text } from 'react-native';

export default function Clock() {
  const [now, setNow] = useState(new Date());

  useEffect(() => {
    console.log('Clock: mount → ξεκινά το interval');
    const id = setInterval(() => setNow(new Date()), 1000);

    return () => {
      console.log('Clock: unmount → cleanup');
      clearInterval(id);
    };
  }, []);

  return <Text style={styles.clock}>🕒 {now.toLocaleTimeString('el-GR')}</Text>;
}

const styles = StyleSheet.create({
  clock: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2c3e50',
  },
});
```

**Πειράματα** (επαναφέρετε τον κώδικα μετά από κάθε ένα):
1. Σβήστε το `return () => {...}` (cleanup). Κρύψτε και εμφανίστε το ρολόι 3 φορές. Τι βλέπετε στα logs; Γιατί;
2. Σβήστε το `[]`. Πόσες φορές τυπώνεται το "mount" και γιατί;

<details>
<summary>Ενδεικτική λύση (App.js)</summary>

```jsx
const [showClock, setShowClock] = useState(true);

// ...
<TouchableOpacity style={styles.button} onPress={() => setShowClock(prev => !prev)}>
  <Text style={styles.buttonText}>
    {showClock ? 'Απόκρυψη ρολογιού' : 'Εμφάνιση ρολογιού'}
  </Text>
</TouchableOpacity>
{showClock && <Clock />}
```
</details>

Όταν τελειώσετε, μπορείτε να αφαιρέσετε το ρολόι από το `App.js` — δεν θα το χρειαστούμε στις επόμενες ασκήσεις.

---

## Άσκηση 5: Δεδομένα από API με useEffect & fetch

<div class="exercise">

**Στόχος**: Οι χρήστες έρχονται από το `https://jsonplaceholder.typicode.com/users` αντί για το τοπικό array

**Ζητούμενα**:
1. Τρία state: `users` (`[]`), `loading` (`true`), `error` (`null`)
2. Μια `async` συνάρτηση `loadUsers` με `fetch`, `try` / `catch` / `finally` (όπως στο Lab 01)
3. Κλήση της `loadUsers` μέσα σε `useEffect(..., [])`
4. Όσο `loading` → `ActivityIndicator`. Αν `error` → μήνυμα + button **"Δοκιμή ξανά"**
5. Προσοχή: στο API η πόλη είναι στο `item.address.city`

</div>

```jsx
const API_URL = 'https://jsonplaceholder.typicode.com/users';

export default function App() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const loadUsers = async () => {
    try {
      setError(null);
      const response = await fetch(API_URL);
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }
      const data = await response.json();
      setUsers(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    loadUsers();
  }, []);

  // ...
}
```

<div class="tip">

**Γιατί όχι `useEffect(async () => {...})`;** Η συνάρτηση του effect πρέπει να επιστρέφει **τίποτα ή cleanup function** — μια `async` συνάρτηση επιστρέφει πάντα Promise. Γι' αυτό ορίζουμε την `async` συνάρτηση χωριστά και την καλούμε μέσα στο effect.  
**`response.ok`**: Το `fetch` **δεν** κάνει reject σε HTTP 404/500 — πρέπει να το ελέγξουμε εμείς.

</div>

**Δοκιμές**:
- Αλλάξτε το URL σε `.../userss` — εμφανίζεται το error; Δουλεύει το "Δοκιμή ξανά" αφού το διορθώσετε;
- Στο κινητό, ενεργοποιήστε **airplane mode** και κάντε reload (`r` στο terminal). Τι μήνυμα παίρνετε;

<details>
<summary>Ενδεικτική λύση (render)</summary>

```jsx
const retry = () => {
  setLoading(true);
  loadUsers();
};

let content;
if (loading) {
  content = <ActivityIndicator size="large" color="#3498db" style={styles.center} />;
} else if (error) {
  content = (
    <View style={styles.center}>
      <Text style={styles.errorText}>Σφάλμα: {error}</Text>
      <TouchableOpacity style={styles.button} onPress={retry}>
        <Text style={styles.buttonText}>Δοκιμή ξανά</Text>
      </TouchableOpacity>
    </View>
  );
} else {
  content = (
    <FlatList
      data={users}
      keyExtractor={item => item.id.toString()}
      renderItem={({ item }) => (
        <UserCard name={item.name} email={item.email} city={item.address.city} />
      )}
      ListEmptyComponent={<Text style={styles.empty}>Δεν βρέθηκαν χρήστες</Text>}
    />
  );
}

return (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Text style={styles.title}>Χρήστες</Text>
      {content}
      <StatusBar style="auto" />
    </SafeAreaView>
  </SafeAreaProvider>
);

// επιπλέον styles
center: {
  flex: 1,
  alignItems: 'center',
  justifyContent: 'center',
},
errorText: {
  fontSize: 16,
  color: '#e74c3c',
  marginBottom: 15,
},
button: {
  backgroundColor: '#3498db',
  paddingHorizontal: 30,
  paddingVertical: 12,
  borderRadius: 8,
},
buttonText: {
  color: 'white',
  fontSize: 16,
  fontWeight: 'bold',
},
empty: {
  textAlign: 'center',
  color: '#7f8c8d',
  marginTop: 30,
},
```
</details>

---

## Άσκηση 6: Αναζήτηση (derived state)

<div class="exercise">

**Στόχος**: Φιλτράρισμα της λίστας καθώς ο χρήστης πληκτρολογεί

**Ζητούμενα**:
1. `TextInput` πάνω από τη λίστα, με state `query`
2. Η λίστα εμφανίζει μόνο τους χρήστες των οποίων το όνομα περιέχει το `query` (χωρίς διάκριση πεζών/κεφαλαίων)
3. Αν δεν ταιριάζει κανείς, εμφανίζεται το `ListEmptyComponent`

</div>

```jsx
const [query, setQuery] = useState('');

// Derived state: υπολογίζεται σε κάθε render, ΔΕΝ χρειάζεται δικό του useState
const q = query.trim().toLowerCase();
const filteredUsers = users.filter(u => u.name.toLowerCase().includes(q));

// ...
<TextInput
  style={styles.search}
  placeholder="Αναζήτηση..."
  value={query}
  onChangeText={setQuery}
  autoCorrect={false}
/>

// και στη FlatList: data={filteredUsers}

// επιπλέον style
search: {
  backgroundColor: 'white',
  marginHorizontal: 15,
  marginBottom: 10,
  padding: 10,
  borderRadius: 8,
},
```

**Ερώτηση**: Θα μπορούσαμε να έχουμε `const [filteredUsers, setFilteredUsers] = useState([])` και ένα `useEffect` που το ενημερώνει όταν αλλάζει το `query`. Γιατί η παραπάνω λύση είναι καλύτερη;

---

## Άσκηση 7: Pull-to-refresh

<div class="exercise">

**Στόχος**: Ο χρήστης "τραβάει" τη λίστα προς τα κάτω για ανανέωση

**Ζητούμενα**:
1. State `refreshing` (`false`)
2. Συνάρτηση `refresh` που θέτει `refreshing = true`, περιμένει (`await`) την `loadUsers()` και μετά `refreshing = false`
3. Props `refreshing` και `onRefresh` στη `FlatList`
4. Δοκιμάστε στο κινητό (στον browser το gesture δεν υποστηρίζεται)

</div>

<details>
<summary>Ενδεικτική λύση</summary>

```jsx
const [refreshing, setRefreshing] = useState(false);

const refresh = async () => {
  setRefreshing(true);
  await loadUsers();
  setRefreshing(false);
};

// ...
<FlatList
  data={filteredUsers}
  keyExtractor={item => item.id.toString()}
  renderItem={({ item }) => (
    <UserCard name={item.name} email={item.email} city={item.address.city} />
  )}
  ListEmptyComponent={<Text style={styles.empty}>Δεν βρέθηκαν χρήστες</Text>}
  refreshing={refreshing}
  onRefresh={refresh}
/>
```
</details>

Κρατήστε το project `usersapp` — θα το συνεχίσουμε στο Lab 05.

---

## 🎯 Bonus Challenge A: Expandable card (state μέσα στο child)

<div class="exercise">

**Στόχος**: Κάθε κάρτα "ανοίγει" με tap και δείχνει τηλέφωνο & website

**Hints**:
- Το `UserCard` αποκτά δικό του `const [expanded, setExpanded] = useState(false)`
- Το εξωτερικό `View` γίνεται `TouchableOpacity` με `onPress={() => setExpanded(prev => !prev)}`
- Περάστε επιπλέον props `phone` και `website` από το `renderItem`

</div>

**Ερώτηση**: Κάθε κάρτα έχει το **δικό της** `expanded`. Τι θα αλλάζατε αν θέλατε να είναι ανοιχτή **μόνο μία** κάρτα κάθε φορά; (Hint: πού πρέπει να "ζει" το state;)

---

## 🎯 Bonus Challenge B: Custom hook `useFetch`

<div class="exercise">

**Στόχος**: Επαναχρησιμοποίηση της λογικής fetch σε ένα **custom hook**

Τα custom hooks είναι απλές συναρτήσεις που το όνομά τους αρχίζει με `use` και καλούν άλλα hooks.

</div>

```jsx
// hooks/useFetch.js
import { useEffect, useState } from 'react';

export default function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    const load = async () => {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }
        const json = await response.json();
        if (!cancelled) setData(json);
      } catch (err) {
        if (!cancelled) setError(err.message);
      } finally {
        if (!cancelled) setLoading(false);
      }
    };

    load();
    return () => {
      cancelled = true;
    };
  }, [url]);

  return { data, loading, error };
}
```

```jsx
// Χρήση
const { data: users, loading, error } = useFetch('https://jsonplaceholder.typicode.com/users');
```

**Ερωτήσεις**:
1. Τι κάνει η μεταβλητή `cancelled`; Σε ποιο σενάριο χρειάζεται; (Hint: το `url` αλλάζει πριν τελειώσει το προηγούμενο request)
2. Χρησιμοποιήστε το `useFetch` για τα posts ενός χρήστη: `https://jsonplaceholder.typicode.com/users/1/posts`

---

## Common Troubleshooting Issues

### Issue 1: "VirtualizedLists should never be nested inside plain ScrollViews with the same orientation"
- Έχετε βάλει `FlatList` μέσα σε `ScrollView` — αφαιρέστε το `ScrollView`
- Αν χρειάζεστε περιεχόμενο πάνω/κάτω από τη λίστα, χρησιμοποιήστε `ListHeaderComponent` / `ListFooterComponent`

### Issue 2: Η λίστα δεν κάνει scroll ή κόβεται
- Ο parent της λίστας χρειάζεται `flex: 1` (π.χ. `container: { flex: 1 }` στο `SafeAreaView`)

### Issue 3: Ατέρμονα requests / η εφαρμογή "κολλάει"
- `useEffect` **χωρίς** dependency array που καλεί `setState` → νέο render → ξανά effect → ...
- Προσθέστε `[]` (ή τις σωστές dependencies)

### Issue 4: `TypeError: Network request failed`
- Ελέγξτε τη σύνδεση στο internet (και στο κινητό)
- Ελέγξτε ότι το URL είναι σωστό και ξεκινά με `https://`

### Issue 5: `TypeError: Cannot read properties of undefined (reading 'city')`
- Στο τοπικό array είχαμε `city`, στο API είναι `address.city`
- Ελέγξτε τη δομή των δεδομένων με `console.log(data[0])`

### Issue 6: Το ρολόι συνεχίζει να γράφει logs αφού κρυφτεί
- Λείπει το cleanup (`clearInterval`) από το `useEffect`

---

## Checklist

**Components & Λίστες**:
- [ ] Το `UserCard` βρίσκεται στο `components/UserCard.js` και δέχεται props
- [ ] Η λίστα εμφανίζεται με `FlatList` και `keyExtractor`
- [ ] Ο τίτλος δεν κρύβεται πίσω από το notch / status bar στο κινητό
- [ ] Ξέρω πότε να χρησιμοποιήσω `SafeAreaView` και πότε `useSafeAreaInsets()`

**useEffect & API**:
- [ ] Εμφανίζεται loading indicator κατά τη φόρτωση
- [ ] Τα δεδομένα έρχονται από το API (10 χρήστες)
- [ ] Σε σφάλμα εμφανίζεται μήνυμα και λειτουργεί το "Δοκιμή ξανά"
- [ ] Καταλαβαίνω πότε τρέχει το effect με `[]`, `[x]` και χωρίς array

**Λειτουργικότητα**:
- [ ] Η αναζήτηση φιλτράρει τη λίστα (και με κεφαλαία)
- [ ] Το pull-to-refresh λειτουργεί στο κινητό

**Code Quality**:
- [ ] No console errors / warnings
- [ ] No unused imports / variables
- [ ] Ένα component ανά αρχείο

---
