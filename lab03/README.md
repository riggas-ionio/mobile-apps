# React Native Lab Exercises 3
## Εργαστηριακές Ασκήσεις: Campus Companion App Development

Σταδιακή Ανάπτυξη με State Management, Context API & Navigation

---

# Περιεχόμενα Lab Session

**Setup & Basics (Ασκήσεις 1-3)**
- Environment Setup, Expo CLI, First App

**Components & Styling (Ασκήσεις 4-5)**
- Core Components, Custom Components, StyleSheet

_συνεχίζεται..._

---

# Campus Companion App
## Τι θα φτιάξουμε;

Μια εφαρμογή που βοηθά τους φοιτητές να οργανώσουν τη φοιτητική τους ζωή:

**Χαρακτηριστικά (Features)**:
- 📚 **Courses Screen**: Λίστα μαθημάτων
- 📅 **Schedule Screen**: Εβδομαδιαίο πρόγραμμα
- 📝 **Tasks Screen**: To-do list για assignments
- 🏫 **Campus Map**: Πληροφορίες κτιρίων/αιθουσών
- 👤 **Profile Screen**: Στοιχεία φοιτητή

**Τεχνολογίες**:
- React Native + Expo
- Context API για global state
- React Navigation (Stack + Tabs)
- No backend (local state μόνο)

---

# Εργαστηριακή Άσκηση 1
## Environment Setup - Node.js & Expo CLI

### Στόχοι (Goals)
✅ Εγκατάσταση Node.js  
✅ Εγκατάσταση Expo CLI  
✅ Δημιουργία πρώτου Expo project  
✅ Εκτέλεση app σε emulator/device  

### Θεωρία
**Node.js**: JavaScript runtime environment  
**Expo**: Εργαλείο για React Native development χωρίς native code  
**Expo Go**: Mobile app για testing στο κινητό  

---

# Άσκηση 0 - Preparation & Setup 

**1. Εγκατάσταση Node.js**
```bash
# Κατέβασε από https://nodejs.org (LTS version)
# Έλεγχος εγκατάστασης:
node --version  # v18.x.x ή νεότερο
npm --version   # 9.x.x ή νεότερο
```

**2. Εγκατάσταση Expo CLI**
```bash
npm install -g expo 
expo --version
```

**3. Δημιουργία Project**
```bash
##   N.B.: Χρήση --template expo-template-blank για JS project
mkdir -p ~/ReactNativeLab/week3/CampusCompanion
cd ~/ReactNativeLab/week3/CampusCompanion
npx create-expo-app --template expo-template-blank
```

**4. Start Development Server**
```bash
npm start
```
ή: 
```
expo start
```

---

# Άσκηση 1 - Testing & Verification
## Πώς δοκιμάζουμε;

**Physical Device**
1. Κατέβασε "Expo Go" app (iOS/Android)
2. Scan το QR code από το terminal
3. Το app θα ανοίξει στο κινητό σου

<!-- **Option B: Android Emulator**
```bash
# Press 'a' στο terminal για Android
```

**Option C: iOS Simulator** (Mac only)
```bash
# Press 'i' στο terminal για iOS
``` -->

**Expected Output**:
Βλέπεις την default Expo οθόνη με "Open up App.js to start working..."

---

# Άσκηση 1 - Project Structure
## Αρχεία και Φάκελοι

```
CampusCompanion/
├── App.js              # Main entry point
├── app.json           # Expo configuration
├── package.json       # Dependencies
├── node_modules/      # Installed packages
├── assets/           # Images, icons, splash
│   ├── icon.png
│   └── splash.png
└── .expo/            # Expo cache (ignore)
```

**App.js** - Το κύριο αρχείο:
```javascript
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Open up App.js to start working!</Text>
      <StatusBar style="auto" />
    </View>
  );
}
```

---

#  Άσκηση 2
## Πρώτο Custom Component - Welcome Screen

### Στόχοι
✅ Κατανόηση JSX syntax  
✅ Core components: View, Text, Image  
✅ Δημιουργία custom component  
✅ Basic styling  

### Θεωρία - JSX
**JSX** = JavaScript XML - επέκταση του JavaScript  
- Μοιάζει με HTML αλλά είναι JavaScript
- Χρησιμοποιεί camelCase: `backgroundColor` αντί `background-color`
- Self-closing tags: `<Image />`
- Κάθε component επιστρέφει JSX

## Δημιούργησε το WelcomeScreen

Δημιουργήστε  την αρχική οθόνη (Welcome Screen) της εφαρμογής — το πρώτο component που θα βλέπει ο χρήστης όταν ανοίγει την εφαρμογή Campus Companion.
Προσθέστε styling.

<details>

```javascript
import { StyleSheet, Text, View, Image } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Image 
        source={{ uri: 'https://via.placeholder.com/150' }}
        style={styles.logo}
      />
      <Text style={styles.title}>Campus Companion</Text>
      <Text style={styles.subtitle}>
        Η εφαρμογή σου για τη φοιτητική ζωή
      </Text>
      <Text style={styles.version}>v1.0.0</Text>
    </View>
  );
}
```

```javascript
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#4A90E2',
    alignItems: 'center',
    justifyContent: 'center',
    padding: 20,
  },
  logo: {
    width: 120,
    height: 120,
    marginBottom: 20,
    borderRadius: 60,
  },
  title: {
    fontSize: 32,
    fontWeight: 'bold',
    color: '#FFFFFF',
    marginBottom: 10,
  },
  subtitle: {
    fontSize: 16,
    color: '#E8F4FF',
    textAlign: 'center',
    marginBottom: 30,
  },
  version: {
    fontSize: 12,
    color: '#B8D8F5',
    position: 'absolute',
    bottom: 20,
  },
});
```

</details>


**Flexbox Layout** (default in React Native):
- `flex: 1` - Παίρνει όλο το διαθέσιμο χώρο
- `flexDirection: 'column'` - Κάθετη διάταξη (default)
- `alignItems: 'center'` - Κεντράρισμα horizontal
- `justifyContent: 'center'` - Κεντράρισμα vertical

**Style Properties**:
- Όλα σε camelCase
- Τιμές χωρίς units: `fontSize: 32` (όχι '32px')
- Colors: hex, rgb, rgba, named colors
- Position: relative (default), absolute

**Tips**:
- Χρησιμοποίησε `StyleSheet.create()` 
- Grouping styles για reusability
- Hot reload: Save file και βλέπεις αλλαγές αμέσως!

---

# Άσκηση 3
## Component Organization - Folder Structure

### Στόχοι
✅ Οργάνωση κώδικα σε folders  
✅ Δημιουργία reusable components  
✅ Import/Export patterns  
✅ Props passing  

### Θεωρία - Component Files
Χωρίζουμε τον κώδικα σε **αρχεία** για:
- **Maintainability**: Εύκολη συντήρηση
- **Reusability**: Επαναχρησιμοποίηση
- **Testing**: Καλύτερο testing
- **Collaboration**: Team work

---

## Δημιούργησε τους φακέλους

```bash
CampusCompanion/
├── App.js
├── src/
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.js
│   │   │   ├── Card.js
│   │   │   └── Header.js
│   │   └── CourseCard.js
│   ├── screens/
│   │   ├── WelcomeScreen.js
│   │   ├── HomeScreen.js
│   │   └── CoursesScreen.js
│   └── styles/
│       ├── colors.js
│       └── globalStyles.js
```

Δημιούργησε τους φακέλους
```bash
mkdir -p src/components/common src/screens src/styles
```

## Styling

<details>

**src/styles/colors.js**
```javascript
export default {
  primary: '#4A90E2',
  secondary: '#50E3C2',
  accent: '#F5A623',
  background: '#F8F9FA',
  white: '#FFFFFF',
  black: '#000000',
  gray: '#6C757D',
  lightGray: '#E9ECEF',
  success: '#28A745',
  error: '#DC3545',
  text: {
    primary: '#212529',
    secondary: '#6C757D',
    light: '#ADB5BD',
  }
};
```

**src/styles/globalStyles.js**
```javascript
import { StyleSheet } from 'react-native';
import colors from './colors';

export default StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  centered: {
    alignItems: 'center',
    justifyContent: 'center',
  },
  // Περισσότερα παρακάτω...
});
```
</details>

## Δημιουργία Custom Button Component

<details>

**src/components/common/Button.js**
```javascript
import React from 'react';
import { TouchableOpacity, Text, StyleSheet } from 'react-native';
import colors from '../../styles/colors';

const Button = ({ title, onPress, variant = 'primary' }) => {
  return (
    <TouchableOpacity 
      style={[
        styles.button, 
        variant === 'secondary' && styles.buttonSecondary
      ]}
      onPress={onPress}
      activeOpacity={0.7}
    >
      <Text style={styles.buttonText}>{title}</Text>
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  button: {
    backgroundColor: colors.primary,
    paddingVertical: 12,
    paddingHorizontal: 24,
    borderRadius: 8,
    alignItems: 'center',
    minWidth: 120,
  },
  buttonSecondary: {
    backgroundColor: colors.secondary,
  },
  buttonText: {
    color: colors.white,
    fontSize: 16,
    fontWeight: '600',
  },
});

export default Button;
```

</details>

## Χρήση Custom Component στο WelcomeScreen

<details>

**src/screens/WelcomeScreen.js**
```javascript
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';
import Button from '../components/common/Button';
import colors from '../styles/colors';

const WelcomeScreen = ({ onGetStarted }) => {
  return (
    <View style={styles.container}>
      <Image 
        source={{ uri: 'https://via.placeholder.com/150' }}
        style={styles.logo}
      />
      <Text style={styles.title}>Campus Companion</Text>
      <Text style={styles.subtitle}>
        Οργάνωσε τη φοιτητική σου ζωή
      </Text>
      
      <Button 
        title="Ξεκίνα" 
        onPress={onGetStarted}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.primary,
    alignItems: 'center',
    justifyContent: 'center',
    padding: 20,
  },
  // ... rest of styles
});

export default WelcomeScreen;
```

</details>

---

# Άσκηση 4
## State Management - useState Hook

### Στόχοι
✅ Κατανόηση React state  
✅ Χρήση useState hook  
✅ Event handling  
✅ Conditional rendering  

### Θεωρία - State
**State** = Δυναμικά δεδομένα που αλλάζουν με το χρόνο

**Πότε χρειάζεται state;**
- User input (forms, toggles)
- UI state (modal open/close, selected item)
- Data από API calls
- Counters, timers

**Χωρίς state**: Static UI  
**Με state**: Interactive, reactive UI

## Βασικό παράδειγμα useState

<details>

**Δημιούργησε: src/screens/HomeScreen.js**
```javascript
import React, { useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import Button from '../components/common/Button';
import colors from '../styles/colors';

const HomeScreen = () => {
  // State declaration
  const [taskCount, setTaskCount] = useState(0);
  const [completedTasks, setCompletedTasks] = useState(0);
  
  const addTask = () => {
    setTaskCount(taskCount + 1);
  };
  
  const completeTask = () => {
    if (taskCount > completedTasks) {
      setCompletedTasks(completedTasks + 1);
    }
  };
  
  const pendingTasks = taskCount - completedTasks;
  
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Dashboard</Text>
      
      <View style={styles.statsContainer}>
        <StatCard label="Συνολικά Tasks" value={taskCount} />
        <StatCard label="Ολοκληρωμένα" value={completedTasks} />
        <StatCard label="Εκκρεμή" value={pendingTasks} />
      </View>
      
      <View style={styles.buttonContainer}>
        <Button title="Νέο Task" onPress={addTask} />
        <Button 
          title="Ολοκλήρωση" 
          onPress={completeTask}
          variant="secondary"
        />
      </View>
    </View>
  );
};
```

</details>

## Δημιουργία styling

<details>

```javascript
const StatCard = ({ label, value }) => (
  <View style={styles.statCard}>
    <Text style={styles.statValue}>{value}</Text>
    <Text style={styles.statLabel}>{label}</Text>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
    padding: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: colors.text.primary,
    marginBottom: 20,
  },
  statsContainer: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 30,
  },
  statCard: {
    backgroundColor: colors.white,
    padding: 15,
    borderRadius: 12,
    alignItems: 'center',
    flex: 1,
    marginHorizontal: 5,
    elevation: 2,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
  },
  statValue: {
    fontSize: 32,
    fontWeight: 'bold',
    color: colors.primary,
    marginBottom: 5,
  },
  statLabel: {
    fontSize: 12,
    color: colors.text.secondary,
    textAlign: 'center',
  },
  buttonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
  },
});

export default HomeScreen;
```
</details>

---

# Άσκηση 5
## Lists - FlatList & Data Rendering

### Στόχοι
✅ Rendering λιστών με FlatList  
✅ Key extractors  
✅ List item components  
✅ Empty states  

### Θεωρία - Lists in React Native
**FlatList vs ScrollView**:
- **ScrollView**: Κάνει render όλα τα items (μικρές λίστες)
- **FlatList**: Lazy loading, virtualization (μεγάλες λίστες)

**FlatList Props**:
- `data`: Array of items
- `renderItem`: Function που κάνει render κάθε item
- `keyExtractor`: Unique key για κάθε item

## Λίστα Μαθημάτων

<details>

**src/screens/CoursesScreen.js**
```javascript
import React, { useState } from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
import colors from '../styles/colors';

const CoursesScreen = () => {
  const [courses, setCourses] = useState([
    { 
      id: '1', 
      code: 'CS101', 
      name: 'Εισαγωγή στον Προγραμματισμό',
      credits: 6,
      professor: 'Δρ. Παπαδόπουλος',
      color: '#FF6B6B'
    },
    { 
      id: '2', 
      code: 'CS201', 
      name: 'Δομές Δεδομένων',
      credits: 6,
      professor: 'Δρ. Γεωργίου',
      color: '#4ECDC4'
    },
    { 
      id: '3', 
      code: 'MATH101', 
      name: 'Μαθηματική Ανάλυση',
      credits: 8,
      professor: 'Δρ. Αντωνίου',
      color: '#95E1D3'
    },
    { 
      id: '4', 
      code: 'CS301', 
      name: 'Αρχιτεκτονική Υπολογιστών',
      credits: 6,
      professor: 'Δρ. Νικολάου',
      color: '#F38181'
    },
  ]);

  const renderCourseItem = ({ item }) => (
    <CourseCard course={item} />
  );

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Τα Μαθήματά Μου</Text>
      <FlatList
        data={courses}
        renderItem={renderCourseItem}
        keyExtractor={item => item.id}
        contentContainerStyle={styles.listContainer}
      />
    </View>
  );
};
```

</details>

## CourseCard Component: Custom List Item Component

<details>

```javascript
const CourseCard = ({ course }) => (
  <View style={[styles.card, { borderLeftColor: course.color }]}>
    <View style={styles.cardHeader}>
      <Text style={styles.courseCode}>{course.code}</Text>
      <View style={styles.creditsContainer}>
        <Text style={styles.credits}>{course.credits} ECTS</Text>
      </View>
    </View>
    <Text style={styles.courseName}>{course.name}</Text>
    <Text style={styles.professor}>👨‍🏫 {course.professor}</Text>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  header: {
    fontSize: 24,
    fontWeight: 'bold',
    color: colors.text.primary,
    padding: 20,
    paddingBottom: 10,
  },
  listContainer: {
    padding: 15,
  },
  card: {
    backgroundColor: colors.white,
    borderRadius: 12,
    padding: 15,
    marginBottom: 15,
    borderLeftWidth: 4,
    elevation: 3,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
  },
  // ... more styles on next slide
});
```
</details>

## Δημιουργία styling

<details>

```javascript
const styles = StyleSheet.create({
  // ... previous styles
  cardHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 8,
  },
  courseCode: {
    fontSize: 16,
    fontWeight: 'bold',
    color: colors.primary,
  },
  creditsContainer: {
    backgroundColor: colors.lightGray,
    paddingHorizontal: 8,
    paddingVertical: 4,
    borderRadius: 12,
  },
  credits: {
    fontSize: 12,
    color: colors.text.secondary,
    fontWeight: '600',
  },
  courseName: {
    fontSize: 18,
    fontWeight: '600',
    color: colors.text.primary,
    marginBottom: 6,
  },
  professor: {
    fontSize: 14,
    color: colors.text.secondary,
  },
});

export default CoursesScreen;
```

</details>

---

## Χρήσιμοι Σύνδεσμοι

**Official Documentation**:
- React: https://react.dev
- React Native: https://reactnative.dev
- Expo: https://docs.expo.dev
- React Navigation: https://reactnavigation.org

**Learning Resources**:
- React Native Express: https://www.reactnative.express
- JavaScript.info: https://javascript.info
- MDN Web Docs: https://developer.mozilla.org

