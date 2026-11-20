# React Native Lab Exercises 5
## Lab Exercises: Navigation & Shared State

**Prerequisites**: [Lab 04](../lab04/README-EN.md) completed — the project `~/ReactNativeLab/usersapp` fetches users from the API and displays them in a `FlatList`.

**Lab goals**:
- ✅ Install & set up **React Navigation 7**
- ✅ **Native Stack** navigator: navigating between screens and passing parameters
- ✅ **Bottom Tabs** navigator with icons & badges
- ✅ **Nested navigators** (a stack inside a tab)
- ✅ The **prop drilling** problem and the solution with the **Context API**
- ✅ Complex state with **`useReducer`** (+ Context)

End result: the Users app with 3 tabs (**Users**, **Favorites**, **About**), a user details screen and a favorites list that is "shared" across all screens.

---

## Exercise 0: Install React Navigation

```bash
cd ~/ReactNativeLab/usersapp

npx expo install @react-navigation/native @react-navigation/native-stack @react-navigation/bottom-tabs react-native-screens react-native-safe-area-context @expo/vector-icons

npx expo start
```

| Package | Role |
|---|---|
| `@react-navigation/native` | Core: `NavigationContainer`, hooks |
| `@react-navigation/native-stack` | Stack navigator with native screens (UINavigationController / Fragment) |
| `@react-navigation/bottom-tabs` | Bottom tab bar |
| `react-native-screens` | Native containers for each screen (performance) |
| `react-native-safe-area-context` | Safe areas — we already have it from Lab 04 |
| `@expo/vector-icons` | Icons (Ionicons, MaterialIcons, ...) |

<div class="tip">

**`npx expo install`** (not `npm install`): `react-native-screens` & `react-native-safe-area-context` contain native code and must match the Expo SDK version.  
**AI assistants**: `create-expo-app` generates an `AGENTS.md` file that tells AI tools to use **Expo Router**. In this lab we use **React Navigation** — if you work with an AI assistant, tell it so (or fix `AGENTS.md`).

</div>

---

## Theory: How React Navigation works

```
NavigationContainer           ← one, at the root of the app
└── Tab.Navigator             ← bottom tabs
    ├── Tab.Screen "UsersTab" → Stack.Navigator   (nested)
    │                             ├── Stack.Screen "Users"
    │                             └── Stack.Screen "UserDetails"
    ├── Tab.Screen "Favorites"
    └── Tab.Screen "About"
```

| Navigator | Behavior | Example |
|---|---|---|
| **Stack** | Screens on top of each other (push / pop), back button | List → Details |
| **Bottom Tabs** | Switch between "sections" of the app | Home / Search / Profile |
| **Drawer** | Side menu | Settings, account |

Every component declared as a `Screen` automatically receives two props:
- **`navigation`**: `navigate('Name', params)`, `goBack()`, `setOptions({...})`
- **`route`**: `route.name`, `route.params`

In components that are **not** screens (e.g. a button deep in the tree) we use the hooks `useNavigation()` / `useRoute()`.

---

## Exercise 1: Convert to a screen & first Stack Navigator

<div class="exercise">

**Goal**: The user list becomes the first screen of a stack navigator

**Steps**:
1. Create a `screens/` folder
2. Move **all** the code of `App.js` (Lab 04) to `screens/UsersScreen.js` and rename the function to `UsersScreen`
3. Fix the import: `import UserCard from '../components/UserCard';`
4. In `UsersScreen` **remove** the `SafeAreaProvider`, `SafeAreaView`, `StatusBar` and the "Users" title — the navigator's header will take care of them. Wrap the content in `<View style={styles.container}>`
5. Write the new `App.js` (below)

</div>

```jsx
// App.js
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { StatusBar } from 'expo-status-bar';
import UsersScreen from './screens/UsersScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Users" component={UsersScreen} options={{ title: 'Users' }} />
      </Stack.Navigator>
      <StatusBar style="auto" />
    </NavigationContainer>
  );
}
```

<details>
<summary>Sample solution (render of UsersScreen)</summary>

```jsx
export default function UsersScreen() {
  // ... state, loadUsers, useEffect, retry, refresh, filteredUsers as in Lab 04

  if (loading) {
    return <ActivityIndicator size="large" color="#3498db" style={styles.center} />;
  }

  if (error) {
    return (
      <View style={styles.center}>
        <Text style={styles.errorText}>Error: {error}</Text>
        <TouchableOpacity style={styles.button} onPress={retry}>
          <Text style={styles.buttonText}>Try again</Text>
        </TouchableOpacity>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.search}
        placeholder="Search..."
        value={query}
        onChangeText={setQuery}
        autoCorrect={false}
      />
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
    </View>
  );
}
```
</details>

**Question**: The app looks the same, but now it has a header. Why don't we need a `SafeAreaView` at the top any more?

---

## Exercise 2: Details screen & parameters

<div class="exercise">

**Goal**: Tap on a user → `UserDetails` screen with all their information

**Steps**:
1. In `UserCard`: the outer `View` becomes a `TouchableOpacity` with an `onPress` prop
2. In `UsersScreen`: accept the `navigation` prop and in `renderItem` pass  
   `onPress={() => navigation.navigate('UserDetails', { user: item })}`
3. Create `screens/UserDetailsScreen.js`, which reads `route.params.user` and shows email, phone, website, city, company
4. Declare the new screen in the stack, with the user's name as the title
5. Test the back button **and** the swipe-back gesture (iOS) / the device back button (Android)

</div>

```jsx
// App.js — second screen
<Stack.Screen
  name="UserDetails"
  component={UserDetailsScreen}
  options={({ route }) => ({ title: route.params.user.name })}
/>
```

<details>
<summary>Sample solution (UserDetailsScreen.js)</summary>

```jsx
import { ScrollView, StyleSheet, Text, View } from 'react-native';

export default function UserDetailsScreen({ route }) {
  const { user } = route.params;

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content}>
      <View style={styles.avatar}>
        <Text style={styles.avatarText}>{user.name.charAt(0)}</Text>
      </View>
      <Text style={styles.name}>{user.name}</Text>
      <Text style={styles.username}>@{user.username}</Text>

      <View style={styles.section}>
        <Text style={styles.label}>Email</Text>
        <Text style={styles.value}>{user.email}</Text>
        <Text style={styles.label}>Phone</Text>
        <Text style={styles.value}>{user.phone}</Text>
        <Text style={styles.label}>Website</Text>
        <Text style={styles.value}>{user.website}</Text>
        <Text style={styles.label}>City</Text>
        <Text style={styles.value}>{user.address.city}</Text>
        <Text style={styles.label}>Company</Text>
        <Text style={styles.value}>{user.company.name}</Text>
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
  },
  content: {
    alignItems: 'center',
    padding: 20,
  },
  avatar: {
    width: 96,
    height: 96,
    borderRadius: 48,
    backgroundColor: '#3498db',
    alignItems: 'center',
    justifyContent: 'center',
    marginBottom: 15,
  },
  avatarText: {
    color: 'white',
    fontSize: 40,
    fontWeight: 'bold',
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2c3e50',
  },
  username: {
    fontSize: 16,
    color: '#7f8c8d',
    marginBottom: 20,
  },
  section: {
    width: '100%',
    maxWidth: 400,
    backgroundColor: 'white',
    borderRadius: 10,
    padding: 15,
    marginBottom: 20,
  },
  label: {
    fontSize: 12,
    color: '#7f8c8d',
    textTransform: 'uppercase',
    marginTop: 10,
  },
  value: {
    fontSize: 16,
    color: '#2c3e50',
  },
});
```
</details>

<div class="tip">

**What do we pass as params?** Here we pass the whole `user` object for simplicity. The better practice is to pass **only what identifies** the data (e.g. `{ userId: 3 }`) and let the screen find it from a shared source (API, Context). We will see this in Lab 06.  
**Never** pass functions in params — they must be serializable (JSON).

</div>

---

## Exercise 3: Header styling & code organization

<div class="exercise">

**Goal**: A consistent header look on all screens of the stack & the navigator in its own file

**Steps**:
1. Create a `navigation/` folder and the file `navigation/UsersStack.js`
2. Move the `Stack.Navigator` with the two screens there, as a component `UsersStack`
3. Add `screenOptions` to the navigator for the header & title color
4. In `App.js` replace the `Stack.Navigator` with `<UsersStack />`

</div>

```jsx
// navigation/UsersStack.js
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import UsersScreen from '../screens/UsersScreen';
import UserDetailsScreen from '../screens/UserDetailsScreen';

const Stack = createNativeStackNavigator();

export default function UsersStack() {
  return (
    <Stack.Navigator
      screenOptions={{
        headerStyle: { backgroundColor: '#3498db' },
        headerTintColor: 'white',
        headerTitleStyle: { fontWeight: 'bold' },
      }}
    >
      <Stack.Screen name="Users" component={UsersScreen} options={{ title: 'Users' }} />
      <Stack.Screen
        name="UserDetails"
        component={UserDetailsScreen}
        options={({ route }) => ({ title: route.params.user.name })}
      />
    </Stack.Navigator>
  );
}
```

**Question**: What is the difference between `screenOptions` (on the navigator) and `options` (on each screen)? Which one wins?

---

## Exercise 4: Bottom Tabs & nested navigators

<div class="exercise">

**Goal**: Three tabs — the stack of Exercise 3 becomes the first tab

**Steps**:
1. Create two simple screens: `screens/FavoritesScreen.js` (for now just a `Text` "Favorites") and `screens/AboutScreen.js`
2. In `App.js` create `Tab = createBottomTabNavigator()` with three `Tab.Screen`s: `UsersTab` (component `UsersStack`), `Favorites`, `About`
3. On `UsersTab` set `headerShown: false` — why?
4. Add icons with `Ionicons` from `@expo/vector-icons`

</div>

```jsx
// App.js
import { Ionicons } from '@expo/vector-icons';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { NavigationContainer } from '@react-navigation/native';
import { StatusBar } from 'expo-status-bar';
import UsersStack from './navigation/UsersStack';
import AboutScreen from './screens/AboutScreen';
import FavoritesScreen from './screens/FavoritesScreen';

const Tab = createBottomTabNavigator();

const TAB_ICONS = {
  UsersTab: 'people',
  Favorites: 'heart',
  About: 'information-circle',
};

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        screenOptions={({ route }) => ({
          tabBarIcon: ({ focused, color, size }) => {
            const name = TAB_ICONS[route.name];
            return <Ionicons name={focused ? name : `${name}-outline`} size={size} color={color} />;
          },
          tabBarActiveTintColor: '#3498db',
          tabBarInactiveTintColor: 'gray',
        })}
      >
        <Tab.Screen
          name="UsersTab"
          component={UsersStack}
          options={{ title: 'Users', headerShown: false }}
        />
        <Tab.Screen name="Favorites" component={FavoritesScreen} options={{ title: 'Favorites' }} />
        <Tab.Screen name="About" component={AboutScreen} options={{ title: 'About' }} />
      </Tab.Navigator>
      <StatusBar style="auto" />
    </NavigationContainer>
  );
}
```

<div class="tip">

**Icons**: Look up icon names at https://icons.expo.fyi (e.g. `people` / `people-outline`).  
**Test**: Open the details of a user, go to the "About" tab and come back to "Users". Are you still on the details? Each tab keeps **its own** stack.

</div>

---

## Theory: Prop Drilling & Context API

We want **favorite users**: add from the details screen, ❤️ in the list, a list in the "Favorites" tab, a badge on the tab. Where does this state "live"?

```
App { favorites }                         ← common ancestor
└── Tab.Navigator
    ├── UsersStack                         [doesn't need it]
    │   ├── UsersScreen                    [✓ ❤️ on the cards]
    │   │   └── UserCard                   [✓]
    │   └── UserDetailsScreen              [✓ toggle]
    └── FavoritesScreen                    [✓ list]
```

With props it would have to pass through navigators and intermediate components (**prop drilling**) — and with React Navigation this isn't even easy to do.

**Context API**:
1. `createContext()` — create it
2. `<Context.Provider value={...}>` — provides the value to the **whole** subtree
3. `useContext(Context)` — any component inside the Provider reads the value

**`useReducer`**: When the state has many "actions" (add, remove, clear, ...), we gather them in one **pure** function `reducer(state, action) → newState`:

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
dispatch({ type: 'TOGGLE', payload: user });
```

---

## Exercise 5: FavoritesContext with useReducer

<div class="exercise">

**Goal**: Shared favorites state for the whole app

**Steps**:
1. Create a `context/` folder and the file `context/FavoritesContext.js` (below)
2. In `App.js` wrap the `NavigationContainer` with `<FavoritesProvider>`
3. Study the reducer: why do we return a **new** array instead of using `push` / `splice`?

</div>

```jsx
// context/FavoritesContext.js
import { createContext, useContext, useMemo, useReducer } from 'react';

const FavoritesContext = createContext(null);

// Reducer: (current state, action) → new state — never mutation!
function favoritesReducer(state, action) {
  switch (action.type) {
    case 'TOGGLE': {
      const exists = state.some(u => u.id === action.payload.id);
      return exists
        ? state.filter(u => u.id !== action.payload.id)
        : [...state, action.payload];
    }
    case 'CLEAR':
      return [];
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

export function FavoritesProvider({ children }) {
  const [favorites, dispatch] = useReducer(favoritesReducer, []);

  const value = useMemo(
    () => ({
      favorites,
      isFavorite: id => favorites.some(u => u.id === id),
      toggleFavorite: user => dispatch({ type: 'TOGGLE', payload: user }),
      clearFavorites: () => dispatch({ type: 'CLEAR' }),
    }),
    [favorites]
  );

  return <FavoritesContext.Provider value={value}>{children}</FavoritesContext.Provider>;
}

export function useFavorites() {
  const context = useContext(FavoritesContext);
  if (!context) {
    throw new Error('useFavorites must be used within a FavoritesProvider');
  }
  return context;
}
```

<div class="tip">

**Custom hook `useFavorites`**: Components don't need to know about `FavoritesContext` — they simply call `useFavorites()`. If we forget it outside the Provider, we get a clear error message.  
**`useMemo`**: Without it, a **new** `value` object is created on every render of the Provider → re-render in **all** consumers.

</div>

---

## Exercise 6: Using the Context in the screens

<div class="exercise">

**Requirements**:
1. **UserDetailsScreen**: a button "❤️ Add to favorites" / "💔 Remove from favorites" that calls `toggleFavorite(user)` (red when the user is a favorite)
2. **UserCard**: new prop `isFavorite` — shows a ❤️ on the right. In `UsersScreen` pass `isFavorite={isFavorite(item.id)}`
3. **FavoritesScreen**: a `FlatList` with the favorites (using `UserCard`), an empty state and a "Clear" button (only when the list is not empty)
4. **Tab badge**: the "Favorites" tab shows how many favorites there are (`tabBarBadge`)
5. Tap on a user in **Favorites** → opens their details in the **Users** tab

</div>

**Hints**:
```jsx
// 4. The badge needs useFavorites() — so the component must be INSIDE the Provider.
//    Move the Tab.Navigator into a separate component Tabs:
function Tabs() {
  const { favorites } = useFavorites();
  return (
    <Tab.Navigator /* ... */>
      {/* ... */}
      <Tab.Screen
        name="Favorites"
        component={FavoritesScreen}
        options={{
          title: 'Favorites',
          tabBarBadge: favorites.length > 0 ? favorites.length : undefined,
        }}
      />
    </Tab.Navigator>
  );
}

export default function App() {
  return (
    <FavoritesProvider>
      <NavigationContainer>
        <Tabs />
        <StatusBar style="auto" />
      </NavigationContainer>
    </FavoritesProvider>
  );
}
```

```jsx
// 5. Navigate to a screen of a nested navigator (another tab)
navigation.navigate('UsersTab', {
  screen: 'UserDetails',
  params: { user: item },
});
```

<details>
<summary>Sample solution (UserDetailsScreen — button)</summary>

```jsx
import { ScrollView, StyleSheet, Text, TouchableOpacity, View } from 'react-native';
import { useFavorites } from '../context/FavoritesContext';

export default function UserDetailsScreen({ route }) {
  const { user } = route.params;
  const { isFavorite, toggleFavorite } = useFavorites();
  const favorite = isFavorite(user.id);

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content}>
      {/* ... as in Exercise 2 ... */}

      <TouchableOpacity
        style={[styles.button, favorite && styles.buttonActive]}
        onPress={() => toggleFavorite(user)}
      >
        <Text style={styles.buttonText}>
          {favorite ? '💔 Remove from favorites' : '❤️ Add to favorites'}
        </Text>
      </TouchableOpacity>
    </ScrollView>
  );
}

// additional styles
button: {
  backgroundColor: '#3498db',
  paddingHorizontal: 30,
  paddingVertical: 15,
  borderRadius: 8,
},
buttonActive: {
  backgroundColor: '#e74c3c',
},
buttonText: {
  color: 'white',
  fontSize: 16,
  fontWeight: 'bold',
},
```
</details>

<details>
<summary>Sample solution (UserCard)</summary>

```jsx
import { StyleSheet, Text, TouchableOpacity, View } from 'react-native';

export default function UserCard({ name, email, city, isFavorite, onPress }) {
  return (
    <TouchableOpacity style={styles.card} onPress={onPress}>
      <View style={styles.avatar}>
        <Text style={styles.avatarText}>{name.charAt(0)}</Text>
      </View>
      <View style={styles.info}>
        <Text style={styles.name}>{name}</Text>
        <Text style={styles.email}>{email}</Text>
        {city ? <Text style={styles.city}>📍 {city}</Text> : null}
      </View>
      {isFavorite && <Text style={styles.heart}>❤️</Text>}
    </TouchableOpacity>
  );
}

// additional style
heart: {
  fontSize: 20,
  marginLeft: 10,
},
```
</details>

<details>
<summary>Sample solution (FavoritesScreen.js)</summary>

```jsx
import { FlatList, StyleSheet, Text, TouchableOpacity, View } from 'react-native';
import UserCard from '../components/UserCard';
import { useFavorites } from '../context/FavoritesContext';

export default function FavoritesScreen({ navigation }) {
  const { favorites, clearFavorites } = useFavorites();

  return (
    <View style={styles.container}>
      <FlatList
        data={favorites}
        keyExtractor={item => item.id.toString()}
        renderItem={({ item }) => (
          <UserCard
            name={item.name}
            email={item.email}
            city={item.address.city}
            onPress={() =>
              // Navigate to a screen of a nested navigator (another tab)
              navigation.navigate('UsersTab', {
                screen: 'UserDetails',
                params: { user: item },
              })
            }
          />
        )}
        ListEmptyComponent={
          <Text style={styles.empty}>You have no favorite users yet</Text>
        }
        contentContainerStyle={styles.list}
      />
      {favorites.length > 0 && (
        <TouchableOpacity style={styles.button} onPress={clearFavorites}>
          <Text style={styles.buttonText}>Clear</Text>
        </TouchableOpacity>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
  },
  list: {
    paddingVertical: 10,
  },
  empty: {
    textAlign: 'center',
    color: '#7f8c8d',
    marginTop: 30,
  },
  button: {
    backgroundColor: '#e74c3c',
    margin: 15,
    paddingVertical: 15,
    borderRadius: 8,
    alignItems: 'center',
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```
</details>

**Questions**:
1. Add a user to the favorites and go back to the list. Does the ❤️ appear immediately? Which mechanism causes `UsersScreen` to re-render?
2. Reload the app (`r`). What happens to the favorites? Why? (We will solve this with local storage.)

### Exercise 6b (optional): Sorting with `useMemo` & a header button

<div class="exercise">

**Goal**: An **A→Z / Z→A** button in the header of the user list

**Requirements**:
1. In `UsersScreen`: a `sortAsc` state (`true`)
2. `sortedUsers` with `useMemo`, depending **only** on `users` and `sortAsc`
3. `filteredUsers` now filters `sortedUsers` (not `users`)
4. With `navigation.setOptions` inside a `useEffect`, set a `headerRight` with a button that toggles `sortAsc`

</div>

```jsx
import { useEffect, useMemo, useState } from 'react';

// ... inside UsersScreen, BEFORE the if (loading) / if (error) return
const [sortAsc, setSortAsc] = useState(true);

const sortedUsers = useMemo(() => {
  console.log('Sorting...');
  // [...users]: sort() mutates the original array!
  return [...users].sort((a, b) =>
    sortAsc ? a.name.localeCompare(b.name) : b.name.localeCompare(a.name)
  );
}, [users, sortAsc]);

const filteredUsers = sortedUsers.filter(u => u.name.toLowerCase().includes(q));

useEffect(() => {
  navigation.setOptions({
    headerRight: () => (
      <TouchableOpacity onPress={() => setSortAsc(prev => !prev)}>
        <Text style={styles.sortButton}>{sortAsc ? 'A→Z' : 'Z→A'}</Text>
      </TouchableOpacity>
    ),
  });
}, [navigation, sortAsc]);

// additional style
sortButton: {
  color: 'white',
  fontSize: 16,
  fontWeight: 'bold',
},
```

<div class="tip">

**Rules of Hooks**: `useMemo` / `useEffect` must come **before** the loading / error `return`s — otherwise the number of hooks changes between renders and React throws an error.

</div>

**Questions**:
1. Type in the search box: is "Sorting..." printed? And when you press the button? Why?
2. Is sorting 10 users really "expensive"? When is `useMemo` worth it? (See "Measure first" in the slides.)

---

## 🎯 Bonus Challenge A: ❤️ in the header

<div class="exercise">

**Goal**: A heart icon on the right side of the details screen's header

**Hints**:
- Option `headerRight: () => <FavoriteButton user={route.params.user} />` on the `UserDetails` screen
- `FavoriteButton` is a component that calls `useFavorites()` and renders `<Ionicons name={favorite ? 'heart' : 'heart-outline'} ... />`

</div>

---

## 🎯 Bonus Challenge B: ThemeContext (dark mode)

<div class="exercise">

**Goal**: A second, independent Context for a light / dark theme

**Hints**:
- `ThemeProvider` with a `dark` state and a `toggleTheme` function
- A switch (`<Switch />`) in the "About" tab
- Screens read colors from `useTheme()`
- For the header/tab bar: prop `theme={dark ? DarkTheme : DefaultTheme}` on the `NavigationContainer` (import from `@react-navigation/native`)

</div>

**Question**: Why are two small Contexts (`Favorites`, `Theme`) better than one big `AppContext`?

---

## Common Troubleshooting Issues

### Issue 1: "Couldn't find a navigation object. Is your component inside NavigationContainer?"
- You are calling `useNavigation()` in a component that is **outside** the `NavigationContainer`

### Issue 2: "Looks like you have nested a 'NavigationContainer' inside another"
- There is **only one** `NavigationContainer`, in `App.js`. Nested navigators (`UsersStack`) do **not** have their own

### Issue 3: "The action 'NAVIGATE' with payload {...} was not handled by any navigator"
- Wrong screen name (names are case-sensitive: `'UserDetails'` ≠ `'userDetails'`)
- For a screen in another tab use `navigate('UsersTab', { screen: 'UserDetails', params })`

### Issue 4: "Non-serializable values were found in the navigation state"
- You passed a function (e.g. a callback) in the params — use Context instead of callbacks

### Issue 5: "useFavorites must be used within a FavoritesProvider"
- `FavoritesProvider` must wrap **all** components that call `useFavorites()` — including `Tabs`

### Issue 6: Two headers, one below the other
- Both the tab navigator and the nested stack show a header — set `headerShown: false` on `UsersTab`

### Issue 7: Errors about `RNSScreen` / `RNCSafeAreaProvider` or incompatible versions
- You installed with `npm install` — run `npx expo install --fix` and restart with `npx expo start --clear`

---

## Checklist

**Navigation**:
- [ ] Tapping a user opens their details, with their name in the title
- [ ] Back button & gesture work
- [ ] Three tabs with icons, one header per screen
- [ ] Each tab keeps its own stack

**Context & useReducer**:
- [ ] Add / remove a favorite from the details screen
- [ ] ❤️ in the user list, badge with the count on the tab
- [ ] Favorites list with an empty state & "Clear"
- [ ] Tapping a favorite opens the details in the "Users" tab
- [ ] (Optional) Sorting A→Z / Z→A from the header, with `useMemo`

**Code Quality**:
- [ ] Folder structure: `components/`, `screens/`, `navigation/`, `context/`
- [ ] No console errors / warnings
- [ ] The reducer does not mutate the state

---
