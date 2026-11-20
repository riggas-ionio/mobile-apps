# React Native Lab Exercises 5
## Εργαστηριακές Ασκήσεις: Navigation & Shared State

**Προαπαιτούμενα**: Ολοκληρωμένο το [Lab 04](../lab04/README.md) — το project `~/ReactNativeLab/usersapp` φέρνει χρήστες από το API και τους εμφανίζει σε `FlatList`.

**Στόχοι του εργαστηρίου**:
- ✅ Εγκατάσταση & ρύθμιση του **React Navigation 7**
- ✅ **Native Stack** navigator: πλοήγηση μεταξύ οθονών και μεταφορά παραμέτρων
- ✅ **Bottom Tabs** navigator με icons & badges
- ✅ **Nested navigators** (stack μέσα σε tab)
- ✅ Το πρόβλημα του **prop drilling** και η λύση με **Context API**
- ✅ Πολύπλοκο state με **`useReducer`** (+ Context)

Τελικό αποτέλεσμα: η εφαρμογή Users με 3 tabs (**Χρήστες**, **Αγαπημένα**, **Σχετικά**), οθόνη λεπτομερειών χρήστη και λίστα αγαπημένων που "μοιράζεται" σε όλες τις οθόνες.

---

## Άσκηση 0: Εγκατάσταση React Navigation

```bash
cd ~/ReactNativeLab/usersapp

npx expo install @react-navigation/native @react-navigation/native-stack @react-navigation/bottom-tabs react-native-screens react-native-safe-area-context @expo/vector-icons

npx expo start
```

| Package | Ρόλος |
|---|---|
| `@react-navigation/native` | Πυρήνας: `NavigationContainer`, hooks |
| `@react-navigation/native-stack` | Stack navigator με native οθόνες (UINavigationController / Fragment) |
| `@react-navigation/bottom-tabs` | Bottom tab bar |
| `react-native-screens` | Native containers για κάθε οθόνη (απόδοση) |
| `react-native-safe-area-context` | Safe areas — το είχαμε ήδη από το Lab 04 |
| `@expo/vector-icons` | Icons (Ionicons, MaterialIcons, ...) |

<div class="tip">

**`npx expo install`** (όχι `npm install`): τα `react-native-screens` & `react-native-safe-area-context` έχουν native κώδικα και πρέπει να ταιριάζουν με την έκδοση του Expo SDK.  
**AI assistants**: Το `create-expo-app` δημιουργεί αρχείο `AGENTS.md` που λέει στα AI εργαλεία να χρησιμοποιούν **Expo Router**. Σε αυτό το εργαστήριο χρησιμοποιούμε **React Navigation** — αν δουλεύετε με AI assistant, ενημερώστε τον (ή διορθώστε το `AGENTS.md`).

</div>

---

## Θεωρία: Πώς δουλεύει το React Navigation

```
NavigationContainer           ← ένα, στη ρίζα της εφαρμογής
└── Tab.Navigator             ← bottom tabs
    ├── Tab.Screen "UsersTab" → Stack.Navigator   (nested)
    │                             ├── Stack.Screen "Users"
    │                             └── Stack.Screen "UserDetails"
    ├── Tab.Screen "Favorites"
    └── Tab.Screen "About"
```

| Navigator | Συμπεριφορά | Παράδειγμα |
|---|---|---|
| **Stack** | Οθόνες η μία πάνω στην άλλη (push / pop), back button | Λίστα → Λεπτομέρειες |
| **Bottom Tabs** | Εναλλαγή μεταξύ "τμημάτων" της εφαρμογής | Αρχική / Αναζήτηση / Προφίλ |
| **Drawer** | Πλαϊνό μενού | Ρυθμίσεις, λογαριασμός |

Κάθε component που δηλώνεται ως `Screen` λαμβάνει αυτόματα δύο props:
- **`navigation`**: `navigate('Name', params)`, `goBack()`, `setOptions({...})`
- **`route`**: `route.name`, `route.params`

Σε components που **δεν** είναι screens (π.χ. ένα button βαθιά στο δέντρο) χρησιμοποιούμε τα hooks `useNavigation()` / `useRoute()`.

---

## Άσκηση 1: Μετατροπή σε screen & πρώτος Stack Navigator

<div class="exercise">

**Στόχος**: Η λίστα χρηστών γίνεται η πρώτη οθόνη ενός stack navigator

**Βήματα**:
1. Δημιουργήστε φάκελο `screens/`
2. Μεταφέρετε **όλο** τον κώδικα του `App.js` (Lab 04) στο `screens/UsersScreen.js` και μετονομάστε τη συνάρτηση σε `UsersScreen`
3. Διορθώστε το import: `import UserCard from '../components/UserCard';`
4. Στο `UsersScreen` **αφαιρέστε** τα `SafeAreaProvider`, `SafeAreaView`, `StatusBar` και τον τίτλο "Χρήστες" — το header του navigator θα τα αναλάβει. Τυλίξτε το περιεχόμενο σε `<View style={styles.container}>`
5. Γράψτε το νέο `App.js` (παρακάτω)

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
        <Stack.Screen name="Users" component={UsersScreen} options={{ title: 'Χρήστες' }} />
      </Stack.Navigator>
      <StatusBar style="auto" />
    </NavigationContainer>
  );
}
```

<details>
<summary>Ενδεικτική λύση (render του UsersScreen)</summary>

```jsx
export default function UsersScreen() {
  // ... state, loadUsers, useEffect, retry, refresh, filteredUsers όπως στο Lab 04

  if (loading) {
    return <ActivityIndicator size="large" color="#3498db" style={styles.center} />;
  }

  if (error) {
    return (
      <View style={styles.center}>
        <Text style={styles.errorText}>Σφάλμα: {error}</Text>
        <TouchableOpacity style={styles.button} onPress={retry}>
          <Text style={styles.buttonText}>Δοκιμή ξανά</Text>
        </TouchableOpacity>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.search}
        placeholder="Αναζήτηση..."
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
        ListEmptyComponent={<Text style={styles.empty}>Δεν βρέθηκαν χρήστες</Text>}
        refreshing={refreshing}
        onRefresh={refresh}
      />
    </View>
  );
}
```
</details>

**Ερώτηση**: Η εφαρμογή φαίνεται ίδια, αλλά τώρα έχει header. Γιατί δεν χρειαζόμαστε πια `SafeAreaView` στο πάνω μέρος;

---

## Άσκηση 2: Οθόνη λεπτομερειών & παράμετροι

<div class="exercise">

**Στόχος**: Tap σε χρήστη → οθόνη `UserDetails` με όλες τις πληροφορίες του

**Βήματα**:
1. Στο `UserCard`: το εξωτερικό `View` γίνεται `TouchableOpacity` με prop `onPress`
2. Στο `UsersScreen`: δεχτείτε το prop `navigation` και στο `renderItem` περάστε  
   `onPress={() => navigation.navigate('UserDetails', { user: item })}`
3. Δημιουργήστε το `screens/UserDetailsScreen.js` που διαβάζει το `route.params.user` και δείχνει email, τηλέφωνο, website, πόλη, εταιρεία
4. Δηλώστε τη νέα οθόνη στο stack, με τίτλο το όνομα του χρήστη
5. Δοκιμάστε το back button **και** το swipe-back gesture (iOS) / back button της συσκευής (Android)

</div>

```jsx
// App.js — δεύτερο screen
<Stack.Screen
  name="UserDetails"
  component={UserDetailsScreen}
  options={({ route }) => ({ title: route.params.user.name })}
/>
```

<details>
<summary>Ενδεικτική λύση (UserDetailsScreen.js)</summary>

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
        <Text style={styles.label}>Τηλέφωνο</Text>
        <Text style={styles.value}>{user.phone}</Text>
        <Text style={styles.label}>Website</Text>
        <Text style={styles.value}>{user.website}</Text>
        <Text style={styles.label}>Πόλη</Text>
        <Text style={styles.value}>{user.address.city}</Text>
        <Text style={styles.label}>Εταιρεία</Text>
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

**Τι περνάμε ως params;** Εδώ περνάμε ολόκληρο το αντικείμενο `user` για απλότητα. Η καλύτερη πρακτική είναι να περνάμε **μόνο ό,τι προσδιορίζει** τα δεδομένα (π.χ. `{ userId: 3 }`) και η οθόνη να τα βρίσκει από μια κοινή πηγή (API, Context). Θα το δούμε στο Lab 06.  
**Ποτέ** functions στα params — πρέπει να είναι serializable (JSON).

</div>

---

## Άσκηση 3: Header styling & οργάνωση κώδικα

<div class="exercise">

**Στόχος**: Ενιαία εμφάνιση header σε όλες τις οθόνες του stack & navigator σε δικό του αρχείο

**Βήματα**:
1. Δημιουργήστε φάκελο `navigation/` και αρχείο `navigation/UsersStack.js`
2. Μεταφέρετε εκεί το `Stack.Navigator` με τα δύο screens, ως component `UsersStack`
3. Προσθέστε `screenOptions` στο navigator για χρώμα header & τίτλου
4. Στο `App.js` αντικαταστήστε το `Stack.Navigator` με `<UsersStack />`

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
      <Stack.Screen name="Users" component={UsersScreen} options={{ title: 'Χρήστες' }} />
      <Stack.Screen
        name="UserDetails"
        component={UserDetailsScreen}
        options={({ route }) => ({ title: route.params.user.name })}
      />
    </Stack.Navigator>
  );
}
```

**Ερώτηση**: Ποια η διαφορά ανάμεσα σε `screenOptions` (στο navigator) και `options` (σε κάθε screen); Ποιο υπερισχύει;

---

## Άσκηση 4: Bottom Tabs & nested navigators

<div class="exercise">

**Στόχος**: Τρία tabs — το stack της Άσκησης 3 γίνεται το πρώτο tab

**Βήματα**:
1. Δημιουργήστε δύο απλές οθόνες: `screens/FavoritesScreen.js` (προς το παρόν ένα `Text` "Αγαπημένα") και `screens/AboutScreen.js`
2. Στο `App.js` δημιουργήστε `Tab = createBottomTabNavigator()` με τρία `Tab.Screen`: `UsersTab` (component `UsersStack`), `Favorites`, `About`
3. Στο `UsersTab` βάλτε `headerShown: false` — γιατί;
4. Προσθέστε icons με `Ionicons` από το `@expo/vector-icons`

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
          options={{ title: 'Χρήστες', headerShown: false }}
        />
        <Tab.Screen name="Favorites" component={FavoritesScreen} options={{ title: 'Αγαπημένα' }} />
        <Tab.Screen name="About" component={AboutScreen} options={{ title: 'Σχετικά' }} />
      </Tab.Navigator>
      <StatusBar style="auto" />
    </NavigationContainer>
  );
}
```

<div class="tip">

**Icons**: Αναζητήστε ονόματα icons στο https://icons.expo.fyi (π.χ. `people` / `people-outline`).  
**Δοκιμή**: Ανοίξτε τις λεπτομέρειες ενός χρήστη, πηγαίνετε στο tab "Σχετικά" και επιστρέψτε στο "Χρήστες". Είστε ακόμα στις λεπτομέρειες; Κάθε tab κρατάει το **δικό του** stack.

</div>

---

## Θεωρία: Prop Drilling & Context API

Θέλουμε **αγαπημένους χρήστες**: προσθήκη από την οθόνη λεπτομερειών, ❤️ στη λίστα, λίστα στο tab "Αγαπημένα", badge στο tab. Πού "ζει" αυτό το state;

```
App { favorites }                         ← κοινός πρόγονος
└── Tab.Navigator
    ├── UsersStack                         [δεν το χρειάζεται]
    │   ├── UsersScreen                    [✓ ❤️ στις κάρτες]
    │   │   └── UserCard                   [✓]
    │   └── UserDetailsScreen              [✓ toggle]
    └── FavoritesScreen                    [✓ λίστα]
```

Με props θα έπρεπε να περάσει μέσα από navigators και ενδιάμεσα components (**prop drilling**) — και με το React Navigation αυτό δεν γίνεται καν εύκολα.

**Context API**:
1. `createContext()` — δημιουργία
2. `<Context.Provider value={...}>` — παρέχει την τιμή σε **όλο** το υπο-δέντρο
3. `useContext(Context)` — οποιοδήποτε component μέσα στον Provider διαβάζει την τιμή

**`useReducer`**: Όταν το state έχει πολλές "ενέργειες" (add, remove, clear, ...), τις συγκεντρώνουμε σε μια **pure** συνάρτηση `reducer(state, action) → newState`:

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
dispatch({ type: 'TOGGLE', payload: user });
```

---

## Άσκηση 5: FavoritesContext με useReducer

<div class="exercise">

**Στόχος**: Κοινό state αγαπημένων για όλη την εφαρμογή

**Βήματα**:
1. Δημιουργήστε φάκελο `context/` και το αρχείο `context/FavoritesContext.js` (παρακάτω)
2. Στο `App.js` τυλίξτε το `NavigationContainer` με `<FavoritesProvider>`
3. Μελετήστε τον reducer: γιατί επιστρέφουμε **νέο** array και δεν κάνουμε `push` / `splice`;

</div>

```jsx
// context/FavoritesContext.js
import { createContext, useContext, useMemo, useReducer } from 'react';

const FavoritesContext = createContext(null);

// Reducer: (τρέχον state, action) → νέο state — ποτέ mutation!
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
      throw new Error(`Άγνωστο action: ${action.type}`);
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
    throw new Error('useFavorites πρέπει να χρησιμοποιείται μέσα σε FavoritesProvider');
  }
  return context;
}
```

<div class="tip">

**Custom hook `useFavorites`**: Τα components δεν χρειάζεται να ξέρουν για το `FavoritesContext` — καλούν απλώς `useFavorites()`. Αν το ξεχάσουμε έξω από τον Provider, παίρνουμε σαφές μήνυμα σφάλματος.  
**`useMemo`**: Χωρίς αυτό, σε κάθε render του Provider δημιουργείται **νέο** object `value` → re-render σε **όλους** τους consumers.

</div>

---

## Άσκηση 6: Χρήση του Context στις οθόνες

<div class="exercise">

**Ζητούμενα**:
1. **UserDetailsScreen**: Button "❤️ Προσθήκη στα αγαπημένα" / "💔 Αφαίρεση από αγαπημένα" που καλεί `toggleFavorite(user)` (κόκκινο όταν είναι αγαπημένος)
2. **UserCard**: νέο prop `isFavorite` — εμφανίζει ❤️ στα δεξιά. Στο `UsersScreen` περάστε `isFavorite={isFavorite(item.id)}`
3. **FavoritesScreen**: `FlatList` με τους αγαπημένους (με `UserCard`), empty state και button "Καθαρισμός" (μόνο όταν η λίστα δεν είναι κενή)
4. **Tab badge**: το tab "Αγαπημένα" δείχνει πόσοι είναι οι αγαπημένοι (`tabBarBadge`)
5. Tap σε χρήστη στα **Αγαπημένα** → ανοίγει τις λεπτομέρειές του στο tab **Χρήστες**

</div>

**Hints**:
```jsx
// 4. Το badge χρειάζεται useFavorites() — άρα το component πρέπει να είναι ΜΕΣΑ στον Provider.
//    Μεταφέρετε το Tab.Navigator σε ξεχωριστό component Tabs:
function Tabs() {
  const { favorites } = useFavorites();
  return (
    <Tab.Navigator /* ... */>
      {/* ... */}
      <Tab.Screen
        name="Favorites"
        component={FavoritesScreen}
        options={{
          title: 'Αγαπημένα',
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
// 5. Πλοήγηση σε screen ενός nested navigator (άλλο tab)
navigation.navigate('UsersTab', {
  screen: 'UserDetails',
  params: { user: item },
});
```

<details>
<summary>Ενδεικτική λύση (UserDetailsScreen — button)</summary>

```jsx
import { ScrollView, StyleSheet, Text, TouchableOpacity, View } from 'react-native';
import { useFavorites } from '../context/FavoritesContext';

export default function UserDetailsScreen({ route }) {
  const { user } = route.params;
  const { isFavorite, toggleFavorite } = useFavorites();
  const favorite = isFavorite(user.id);

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content}>
      {/* ... όπως στην Άσκηση 2 ... */}

      <TouchableOpacity
        style={[styles.button, favorite && styles.buttonActive]}
        onPress={() => toggleFavorite(user)}
      >
        <Text style={styles.buttonText}>
          {favorite ? '💔 Αφαίρεση από αγαπημένα' : '❤️ Προσθήκη στα αγαπημένα'}
        </Text>
      </TouchableOpacity>
    </ScrollView>
  );
}

// επιπλέον styles
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
<summary>Ενδεικτική λύση (UserCard)</summary>

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

// επιπλέον style
heart: {
  fontSize: 20,
  marginLeft: 10,
},
```
</details>

<details>
<summary>Ενδεικτική λύση (FavoritesScreen.js)</summary>

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
              // Πλοήγηση σε screen ενός nested navigator (άλλο tab)
              navigation.navigate('UsersTab', {
                screen: 'UserDetails',
                params: { user: item },
              })
            }
          />
        )}
        ListEmptyComponent={
          <Text style={styles.empty}>Δεν έχετε αγαπημένους χρήστες ακόμα</Text>
        }
        contentContainerStyle={styles.list}
      />
      {favorites.length > 0 && (
        <TouchableOpacity style={styles.button} onPress={clearFavorites}>
          <Text style={styles.buttonText}>Καθαρισμός</Text>
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

**Ερωτήσεις**:
1. Προσθέστε έναν χρήστη στα αγαπημένα και επιστρέψτε στη λίστα. Εμφανίζεται αμέσως η ❤️; Ποιος μηχανισμός προκαλεί το re-render του `UsersScreen`;
2. Κάντε reload την εφαρμογή (`r`). Τι γίνεται με τα αγαπημένα; Γιατί; (Θα το λύσουμε με local storage.)

### Άσκηση 6b (προαιρετική): Ταξινόμηση με `useMemo` & κουμπί στο header

<div class="exercise">

**Στόχος**: Κουμπί **A→Z / Z→A** στο header της λίστας χρηστών

**Ζητούμενα**:
1. Στο `UsersScreen`: state `sortAsc` (`true`)
2. `sortedUsers` με `useMemo`, που εξαρτάται **μόνο** από `users` και `sortAsc`
3. Το `filteredUsers` φιλτράρει πλέον το `sortedUsers` (όχι το `users`)
4. Με `navigation.setOptions` μέσα σε `useEffect`, βάλτε `headerRight` με κουμπί που αλλάζει το `sortAsc`

</div>

```jsx
import { useEffect, useMemo, useState } from 'react';

// ... μέσα στο UsersScreen, ΠΡΙΝ από τα if (loading) / if (error) return
const [sortAsc, setSortAsc] = useState(true);

const sortedUsers = useMemo(() => {
  console.log('Ταξινόμηση...');
  // [...users]: το sort() αλλάζει το αρχικό array!
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

// επιπλέον style
sortButton: {
  color: 'white',
  fontSize: 16,
  fontWeight: 'bold',
},
```

<div class="tip">

**Κανόνες των Hooks**: Τα `useMemo` / `useEffect` πρέπει να βρίσκονται **πριν** από τα `return` του loading / error — αλλιώς ο αριθμός των hooks αλλάζει μεταξύ renders και η React πετάει σφάλμα.

</div>

**Ερωτήσεις**:
1. Πληκτρολογήστε στην αναζήτηση: τυπώνεται "Ταξινόμηση..."; Και όταν πατάτε το κουμπί; Γιατί;
2. Η ταξινόμηση 10 χρηστών είναι πραγματικά "ακριβή"; Πότε αξίζει το `useMemo`; (Βλ. "Μετρήστε πρώτα" στις διαφάνειες.)

---

## 🎯 Bonus Challenge A: ❤️ στο header

<div class="exercise">

**Στόχος**: Εικονίδιο-καρδιά στη δεξιά πλευρά του header της οθόνης λεπτομερειών

**Hints**:
- Option `headerRight: () => <FavoriteButton user={route.params.user} />` στο `UserDetails` screen
- Το `FavoriteButton` είναι component που καλεί `useFavorites()` και εμφανίζει `<Ionicons name={favorite ? 'heart' : 'heart-outline'} ... />`

</div>

---

## 🎯 Bonus Challenge B: ThemeContext (dark mode)

<div class="exercise">

**Στόχος**: Δεύτερο, ανεξάρτητο Context για light / dark θέμα

**Hints**:
- `ThemeProvider` με state `dark` και function `toggleTheme`
- Switch (`<Switch />`) στο tab "Σχετικά"
- Τα screens διαβάζουν χρώματα από το `useTheme()`
- Για το header/tab bar: prop `theme={dark ? DarkTheme : DefaultTheme}` στο `NavigationContainer` (import από `@react-navigation/native`)

</div>

**Ερώτηση**: Γιατί είναι καλύτερα δύο μικρά Contexts (`Favorites`, `Theme`) από ένα μεγάλο `AppContext`;

---

## Common Troubleshooting Issues

### Issue 1: "Couldn't find a navigation object. Is your component inside NavigationContainer?"
- Καλείτε `useNavigation()` σε component που βρίσκεται **έξω** από το `NavigationContainer`

### Issue 2: "Looks like you have nested a 'NavigationContainer' inside another"
- Υπάρχει **ένα** `NavigationContainer` μόνο, στο `App.js`. Τα nested navigators (`UsersStack`) **δεν** έχουν δικό τους

### Issue 3: "The action 'NAVIGATE' with payload {...} was not handled by any navigator"
- Λάθος όνομα screen (τα ονόματα είναι case-sensitive: `'UserDetails'` ≠ `'userDetails'`)
- Για screen σε άλλο tab χρησιμοποιήστε `navigate('UsersTab', { screen: 'UserDetails', params })`

### Issue 4: "Non-serializable values were found in the navigation state"
- Περάσατε function (π.χ. callback) στα params — χρησιμοποιήστε Context αντί για callbacks

### Issue 5: "useFavorites πρέπει να χρησιμοποιείται μέσα σε FavoritesProvider"
- Ο `FavoritesProvider` πρέπει να τυλίγει **όλα** τα components που καλούν `useFavorites()` — και το `Tabs`

### Issue 6: Δύο headers το ένα κάτω από το άλλο
- Και το tab navigator και το nested stack δείχνουν header — βάλτε `headerShown: false` στο `UsersTab`

### Issue 7: Σφάλματα για `RNSScreen` / `RNCSafeAreaProvider` ή incompatible versions
- Εγκαταστήσατε με `npm install` — τρέξτε `npx expo install --fix` και κάντε restart με `npx expo start --clear`

---

## Checklist

**Navigation**:
- [ ] Tap σε χρήστη ανοίγει τις λεπτομέρειές του, με το όνομά του στον τίτλο
- [ ] Back button & gesture λειτουργούν
- [ ] Τρία tabs με icons, ένα header ανά οθόνη
- [ ] Κάθε tab κρατάει το δικό του stack

**Context & useReducer**:
- [ ] Προσθήκη / αφαίρεση αγαπημένου από την οθόνη λεπτομερειών
- [ ] ❤️ στη λίστα χρηστών, badge με πλήθος στο tab
- [ ] Λίστα αγαπημένων με empty state & "Καθαρισμός"
- [ ] Tap στα αγαπημένα ανοίγει τις λεπτομέρειες στο tab "Χρήστες"
- [ ] (Προαιρετικά) Ταξινόμηση A→Z / Z→A από το header, με `useMemo`

**Code Quality**:
- [ ] Δομή φακέλων: `components/`, `screens/`, `navigation/`, `context/`
- [ ] No console errors / warnings
- [ ] Ο reducer δεν κάνει mutation στο state

---
