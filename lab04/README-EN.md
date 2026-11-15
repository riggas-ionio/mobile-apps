# React Native Lab Exercises 4
## Lab Exercises: Components, Lists & useEffect

**Prerequisites**: [Lab 03](../lab03/README-EN.md) completed — `useState`, `onPress`, `TextInput`, rendering a list with `map` + `key`.

**Lab goals**:
- ✅ Create our own components in separate files & use **props**
- ✅ Lists with `ScrollView` and `FlatList` (and when to use each)
- ✅ Safe areas (notch, status bar) with `react-native-safe-area-context`
- ✅ Side effects with the `useEffect` hook (dependency array, cleanup)
- ✅ Fetch data from a REST API with `fetch` (loading / error states)
- ✅ Search (derived state) & pull-to-refresh

In this lab we build a **Users** app that fetches users from the API we saw in Lab 01 (`jsonplaceholder.typicode.com`). In Lab 05 we will add navigation to it.

---

## Exercise 0: New project

<div class="exercise">

**Goal**: Create the `usersapp` project

</div>

```bash
cd ~/ReactNativeLab
npx create-expo-app@latest usersapp --template blank
cd usersapp

# Library for safe areas (notch, status bar, home indicator)
npx expo install react-native-safe-area-context

npx expo start
```

<div class="tip">

**`npx expo install`** instead of `npm install`: it automatically picks the version of the package that is **compatible with the Expo SDK** of your project. Use it for every package that contains native code.

</div>

Open the app in the browser (`w`) **and** in Expo Go (QR code), as in Lab 03.

---

## Exercise 1: First component with props

<div class="exercise">

**Goal**: A reusable component in its own file

**Steps**:
1. Create a `components/` folder at the root of the project
2. Create the file `components/UserCard.js` (code below)
3. In `App.js` add `import UserCard from './components/UserCard';`
4. Display **three** cards with different props

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
// App.js (inside the return)
<UserCard name="Maria Papadopoulou" email="maria@ionio.gr" city="Corfu" />
<UserCard name="Nikos Georgiou" email="nikos@ionio.gr" city="Lefkada" />
<UserCard name="Eleni Dimou" email="eleni@ionio.gr" />
```

<details>
<summary>Styles for UserCard</summary>

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

**New concepts**:
- **Props**: parameters the parent passes to the child — `function UserCard({ name, email, city })` (destructuring, Lab 01)
- **Unidirectional data flow**: data flows from parent → child. The child does **not** change its props
- **Optional prop**: the third card has no `city` — hence the `{city ? ... : null}`

**Question**: What is the difference between **props** and **state**? Which of the two would you use to make a card "expand" and show more information?

---

## Exercise 2: List with ScrollView & Safe Areas

<div class="exercise">

**Goal**: Display many users from an array, with scrolling

**Requirements**:
1. Define an array `USERS` (outside the component) with **at least 12** users `{ id, name, email, city }`
2. Display them with `map` inside a `ScrollView`
3. Wrap the app in `SafeAreaProvider` / `SafeAreaView` so the title is not hidden behind the notch / status bar
4. Test on your phone: does it scroll? Is anything hidden?

</div>

```jsx
import { StatusBar } from 'expo-status-bar';
import { ScrollView, StyleSheet, Text } from 'react-native';
import { SafeAreaProvider, SafeAreaView } from 'react-native-safe-area-context';
import UserCard from './components/UserCard';

const USERS = [
  { id: 1, name: 'Maria Papadopoulou', email: 'maria@ionio.gr', city: 'Corfu' },
  { id: 2, name: 'Nikos Georgiou', email: 'nikos@ionio.gr', city: 'Lefkada' },
  { id: 3, name: 'Eleni Dimou', email: 'eleni@ionio.gr', city: 'Zakynthos' },
  // ... add more
];

export default function App() {
  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.title}>Users</Text>
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

**SafeAreaView**: We use the `SafeAreaView` from **`react-native-safe-area-context`** and not the one from `react-native` — the built-in one is deprecated and only works on iOS.  
**key**: As in Lab 03, every item of the `map` needs a unique `key`.

</div>

### Exercise 2b: Manual insets with `useSafeAreaInsets`

<div class="exercise">

**Goal**: Control the padding only where needed, instead of using `SafeAreaView`

**Requirements**:
1. Replace the `SafeAreaView` with a plain `View`
2. Get the insets with `useSafeAreaInsets()` and apply **only** `paddingTop: insets.top` to the title header
3. Give the header its own background color (e.g. `#3498db`) — notice that the color "extends" behind the status bar as well
4. Add `paddingBottom: insets.bottom` to the `contentContainerStyle` of the `ScrollView`, so the last card is not hidden behind the home indicator
5. Print `console.log(insets)` and compare the values on different devices

</div>

<details>
<summary>Sample solution (App.js)</summary>

```jsx
import { StatusBar } from 'expo-status-bar';
import { ScrollView, StyleSheet, Text, View } from 'react-native';
import { SafeAreaProvider, useSafeAreaInsets } from 'react-native-safe-area-context';
import UserCard from './components/UserCard';

// const USERS = [ ... ] as in Exercise 2

function UsersScreen() {
  const insets = useSafeAreaInsets();  // { top, bottom, left, right }
  console.log(insets);

  return (
    <View style={styles.container}>
      <View style={[styles.header, { paddingTop: insets.top }]}>
        <Text style={styles.title}>Users</Text>
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

**Why a separate component?** `useSafeAreaInsets()` reads its values from the `SafeAreaProvider`, so it must be called in a component that is **inside** it — not in the `App` itself, which renders the provider.

</div>

---

## Exercise 3: FlatList

<div class="exercise">

**Goal**: Replace `ScrollView` + `map` with `FlatList`

**Requirements**:
1. Replace the `ScrollView` with a `FlatList`
2. Set the props `data`, `keyExtractor` and `renderItem`
3. Add a `ListEmptyComponent` — test it with `data={[]}`
4. (Optional) `ItemSeparatorComponent` or `ListHeaderComponent`

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
  ListEmptyComponent={<Text style={styles.empty}>No users found</Text>}
/>
```

**ScrollView vs FlatList**:

| `ScrollView` | `FlatList` |
|---|---|
| Renders **all** children immediately | Renders only what is visible (+ a few more) — **virtualization** |
| Suitable for few / fixed items (forms, pages) | Suitable for large or dynamic lists |
| `map` + `key` | `data` + `renderItem` + `keyExtractor` |
| — | Built in: empty state, header/footer, pull-to-refresh, infinite scroll |

**Question**: `keyExtractor` must return a **string**. Why do we use `item.id.toString()`? Why is it **not** a good idea to use the array index as the key?

---

## Theory: The useEffect Hook

Components must be **pure**: same props/state → same JSX. Anything that "reaches outside" the render is a **side effect**: API calls, timers, subscriptions, logging.

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // 1. Setup: runs AFTER the render
  return () => {
    // 2. Cleanup (optional): before the next effect & on unmount
  };
}, [dependencies]); // 3. When it runs again
```

| Dependency array | When the effect runs |
|---|---|
| *(no array)* | After **every** render — rarely what we want |
| `[]` | **Once**, after the first render (mount) |
| `[a, b]` | On mount **and** every time `a` or `b` changes |

<div class="tip">

**You don't need an effect** for values computed from props/state (e.g. a filtered list) — compute them directly during render (see Exercise 6).

</div>

---

## Exercise 4: useEffect with timer & cleanup

<div class="exercise">

**Goal**: Understand mount / unmount and cleanup

**Steps**:
1. Create `components/Clock.js` (code below)
2. In `App.js` add a `showClock` state and a button "Show / Hide clock"
3. Display `<Clock />` only when `showClock === true`
4. Open the logs (the `npx expo start` terminal or the browser console) and press the button a few times

</div>

```jsx
// components/Clock.js
import { useEffect, useState } from 'react';
import { StyleSheet, Text } from 'react-native';

export default function Clock() {
  const [now, setNow] = useState(new Date());

  useEffect(() => {
    console.log('Clock: mount → interval starts');
    const id = setInterval(() => setNow(new Date()), 1000);

    return () => {
      console.log('Clock: unmount → cleanup');
      clearInterval(id);
    };
  }, []);

  return <Text style={styles.clock}>🕒 {now.toLocaleTimeString('en-GB')}</Text>;
}

const styles = StyleSheet.create({
  clock: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2c3e50',
  },
});
```

**Experiments** (restore the code after each one):
1. Delete the `return () => {...}` (cleanup). Hide and show the clock 3 times. What do you see in the logs? Why?
2. Delete the `[]`. How many times is "mount" printed, and why?

<details>
<summary>Sample solution (App.js)</summary>

```jsx
const [showClock, setShowClock] = useState(true);

// ...
<TouchableOpacity style={styles.button} onPress={() => setShowClock(prev => !prev)}>
  <Text style={styles.buttonText}>
    {showClock ? 'Hide clock' : 'Show clock'}
  </Text>
</TouchableOpacity>
{showClock && <Clock />}
```
</details>

When you're done, you can remove the clock from `App.js` — we won't need it in the next exercises.

---

## Exercise 5: Data from an API with useEffect & fetch

<div class="exercise">

**Goal**: The users come from `https://jsonplaceholder.typicode.com/users` instead of the local array

**Requirements**:
1. Three states: `users` (`[]`), `loading` (`true`), `error` (`null`)
2. An `async` function `loadUsers` with `fetch`, `try` / `catch` / `finally` (as in Lab 01)
3. Call `loadUsers` inside `useEffect(..., [])`
4. While `loading` → `ActivityIndicator`. If `error` → message + a **"Try again"** button
5. Watch out: in the API the city is in `item.address.city`

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

**Why not `useEffect(async () => {...})`?** The effect function must return **nothing or a cleanup function** — an `async` function always returns a Promise. That's why we define the `async` function separately and call it inside the effect.  
**`response.ok`**: `fetch` does **not** reject on HTTP 404/500 — we have to check it ourselves.

</div>

**Tests**:
- Change the URL to `.../userss` — does the error appear? Does "Try again" work after you fix it?
- On your phone, turn on **airplane mode** and reload (`r` in the terminal). What message do you get?

<details>
<summary>Sample solution (render)</summary>

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
      <Text style={styles.errorText}>Error: {error}</Text>
      <TouchableOpacity style={styles.button} onPress={retry}>
        <Text style={styles.buttonText}>Try again</Text>
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
      ListEmptyComponent={<Text style={styles.empty}>No users found</Text>}
    />
  );
}

return (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Text style={styles.title}>Users</Text>
      {content}
      <StatusBar style="auto" />
    </SafeAreaView>
  </SafeAreaProvider>
);

// additional styles
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

## Exercise 6: Search (derived state)

<div class="exercise">

**Goal**: Filter the list as the user types

**Requirements**:
1. A `TextInput` above the list, with a `query` state
2. The list shows only the users whose name contains the `query` (case-insensitive)
3. If nobody matches, the `ListEmptyComponent` is shown

</div>

```jsx
const [query, setQuery] = useState('');

// Derived state: computed on every render, does NOT need its own useState
const q = query.trim().toLowerCase();
const filteredUsers = users.filter(u => u.name.toLowerCase().includes(q));

// ...
<TextInput
  style={styles.search}
  placeholder="Search..."
  value={query}
  onChangeText={setQuery}
  autoCorrect={false}
/>

// and in the FlatList: data={filteredUsers}

// additional style
search: {
  backgroundColor: 'white',
  marginHorizontal: 15,
  marginBottom: 10,
  padding: 10,
  borderRadius: 8,
},
```

**Question**: We could have `const [filteredUsers, setFilteredUsers] = useState([])` and a `useEffect` that updates it when `query` changes. Why is the solution above better?

---

## Exercise 7: Pull-to-refresh

<div class="exercise">

**Goal**: The user "pulls" the list down to refresh it

**Requirements**:
1. A `refreshing` state (`false`)
2. A `refresh` function that sets `refreshing = true`, waits (`await`) for `loadUsers()` and then sets `refreshing = false`
3. The props `refreshing` and `onRefresh` on the `FlatList`
4. Test on your phone (the gesture is not supported in the browser)

</div>

<details>
<summary>Sample solution</summary>

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
  ListEmptyComponent={<Text style={styles.empty}>No users found</Text>}
  refreshing={refreshing}
  onRefresh={refresh}
/>
```
</details>

Keep the `usersapp` project — we will continue it in Lab 05.

---

## 🎯 Bonus Challenge A: Expandable card (state inside the child)

<div class="exercise">

**Goal**: Each card "expands" on tap and shows phone & website

**Hints**:
- `UserCard` gets its own `const [expanded, setExpanded] = useState(false)`
- The outer `View` becomes a `TouchableOpacity` with `onPress={() => setExpanded(prev => !prev)}`
- Pass the extra props `phone` and `website` from `renderItem`

</div>

**Question**: Each card has **its own** `expanded`. What would you change if you wanted **only one** card to be open at a time? (Hint: where should the state "live"?)

---

## 🎯 Bonus Challenge B: Custom hook `useFetch`

<div class="exercise">

**Goal**: Reuse the fetch logic in a **custom hook**

Custom hooks are plain functions whose name starts with `use` and which call other hooks.

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
// Usage
const { data: users, loading, error } = useFetch('https://jsonplaceholder.typicode.com/users');
```

**Questions**:
1. What does the `cancelled` variable do? In which scenario is it needed? (Hint: the `url` changes before the previous request finishes)
2. Use `useFetch` for the posts of a user: `https://jsonplaceholder.typicode.com/users/1/posts`

---

## Common Troubleshooting Issues

### Issue 1: "VirtualizedLists should never be nested inside plain ScrollViews with the same orientation"
- You have put a `FlatList` inside a `ScrollView` — remove the `ScrollView`
- If you need content above/below the list, use `ListHeaderComponent` / `ListFooterComponent`

### Issue 2: The list doesn't scroll or is cut off
- The parent of the list needs `flex: 1` (e.g. `container: { flex: 1 }` on the `SafeAreaView`)

### Issue 3: Endless requests / the app "freezes"
- A `useEffect` **without** a dependency array that calls `setState` → new render → effect again → ...
- Add `[]` (or the correct dependencies)

### Issue 4: `TypeError: Network request failed`
- Check your internet connection (on the phone too)
- Check that the URL is correct and starts with `https://`

### Issue 5: `TypeError: Cannot read properties of undefined (reading 'city')`
- In the local array we had `city`, in the API it is `address.city`
- Check the data structure with `console.log(data[0])`

### Issue 6: The clock keeps writing logs after it is hidden
- The cleanup (`clearInterval`) is missing from the `useEffect`

---

## Checklist

**Components & Lists**:
- [ ] `UserCard` lives in `components/UserCard.js` and accepts props
- [ ] The list is displayed with `FlatList` and `keyExtractor`
- [ ] The title is not hidden behind the notch / status bar on the phone
- [ ] I know when to use `SafeAreaView` and when to use `useSafeAreaInsets()`

**useEffect & API**:
- [ ] A loading indicator is shown while loading
- [ ] The data comes from the API (10 users)
- [ ] On error a message is shown and "Try again" works
- [ ] I understand when the effect runs with `[]`, `[x]` and without an array

**Functionality**:
- [ ] Search filters the list (also with uppercase letters)
- [ ] Pull-to-refresh works on the phone

**Code Quality**:
- [ ] No console errors / warnings
- [ ] No unused imports / variables
- [ ] One component per file

---
