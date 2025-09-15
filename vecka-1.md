# Vecka 1: Grunder i React Native

## Lärandemål för veckan

Efter denna vecka ska du kunna:

- Förklara vad React Native är och varför det används
- Förstå vad komponenter är och hur de fungerar
- Sätta upp en utvecklingsmiljö med Expo
- Förstå grundläggande arkitekturen i React Native
- Skapa din första enkla React Native-applikation
- Känna till skillnaderna mellan Android och iOS på en grundläggande nivå

## Vad är React Native?

React Native är ett cross-platform ramverk utvecklat av Facebook (Meta) som låter dig bygga mobilapplikationer med JavaScript och React. Istället för att rendera till webbläsarens DOM, renderar React Native till native komponenter på målplattformen.

Alla mobiler har sina egna ramverk. Android använder Java medans iOS (Apple) använder Swift. Att bygga en app i Java för Android innebär att det byggs "natively". Ett problem uppstår om man vill bygga en app för både Android och Apple: man måste bygga två appar som ser likadana ut, i två olika språk. React Native är till för att ta bort detta problem.

**Viktiga egenskaper:**

- **En kodbas, två plattformar**: Skriv en gång, kör överallt (med vissa plattformsspecifika anpassningar)
- **Native prestanda**: Använder riktiga native komponenter, inte webvyer
- **JavaScript och React**: Bygger på teknologier du kanske redan känner
- **Hot Reload**: Se förändringar direkt utan att kompilera om hela appen
- **Stor community**: Många utvecklare, bibliotek och resurser

### En analogi: React Native som universell översättare

Tänk dig att du är en författare som vill publicera en bok på flera språk. Traditionellt skulle du behöva:

- Skriva boken på svenska
- Anställa en översättare för engelska
- Anställa en annan översättare för tyska
- Och så vidare för varje språk

Detta är precis hur traditionell mobilutveckling fungerar. För att skapa en app som fungerar på både Android och iOS behöver du normalt:

- Skriva appen i Java/Kotlin för Android
- Skriva samma app igen i Swift/Objective-C för iOS
- Underhålla två helt separata kodbaser

**React Native fungerar som en universell översättare för mobilappar.** Du skriver din kod en gång (i JavaScript och React), och React Native "översätter" den automatiskt till det språk som varje plattform förstår.

## Vad är komponenter?

En **komponent** i React Native är en återanvändbar kodbit som representerar en del av användargränssnittet. Komponenter kan vara:

**1. Anpassade komponenter** - De som du skapar själv genom att kombinera andra komponenter.
**2. Inbyggda komponenter** - De som kommer med React Native:

- `View` - En container (som en osynlig låda) (jämförbar med `div`)
- `Text` - För att visa text (jämförbar med `p`)
- `Button` - För knappar som användaren kan trycka på
- `Image` - För att visa bilder

### Analogi: Komponenter som LEGO-klossar

Tänk dig att du ska bygga ett hus med LEGO. Du har olika typer av klossar:

- **Grundklossar** (som väggar) - dessa är som `View`-komponenter
- **Fönster** - dessa är som `Image`-komponenter
- **Dörrar** - dessa är som `Button`-komponenter
- **Skyltar med text** - dessa är som `Text`-komponenter

Varje LEGO-kloss har ett specifikt syfte och kan kombineras med andra klossar för att bygga något större och mer komplext. På samma sätt är komponenter i React Native återanvändbara byggstenar som du kombinerar för att skapa din mobilapplikation.

### Exempel på en enkel komponent

```jsx
// Detta är en komponent - tänk på det som en mall eller ritning
function WelcomeMessage() {
  return <Text>Välkommen till min app!</Text>;
}

// Du kan använda den här komponenten flera gånger:
function MyApp() {
  return (
    <View>
      <WelcomeMessage />
      <WelcomeMessage />
      <WelcomeMessage />
    </View>
  );
}
```

**Viktigt att förstå:**

- Komponenter är som mallar - du skapar dem en gång och kan använda dem många gånger
- Komponenter kan ta emot "props" (egenskaper) för att anpassa sitt beteende
- Komponenter kan innehålla andra komponenter (som att bygga upp en LEGO-bit med flera LEGO-klossar)

## React Native Fundamentals: Props, State och Hooks

### Props - Egenskaper som skickas mellan komponenter

**Props** (properties) är data som du skickar från en föräldrakomponent till en barnkomponent. Det är som att ge instruktioner eller information till en LEGO-kloss om hur den ska se ut eller bete sig.

```jsx
// Barnkomponent som tar emot props
function Greeting({ name, age }) {
  return (
    <View>
      <Text>Hej {name}!</Text>
      <Text>Du är {age} år gammal.</Text>
    </View>
  );
}

// Föräldrakomponent som skickar props
function App() {
  return (
    <View>
      <Greeting name="Anna" age={25} />
      <Greeting name="Erik" age={30} />
    </View>
  );
}
```

**Viktiga regler för props:**

- Props är **read-only** - barnkomponenten kan inte ändra dem
- Props skickas "nedåt" från förälder till barn
- Props gör komponenter återanvändbara och flexibla

### State - Komponentens interna tillstånd

**State** är data som en komponent "äger" och kan ändra. Det är som komponentens "minne" - information den håller koll på och kan uppdatera.

```jsx
import { useState } from "react";

function Counter() {
  // useState skapar en state-variabel och en funktion för att uppdatera den
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text>Antal klick: {count}</Text>
      <Button
        title="Klicka mig!"
        onPress={() => setCount(count + 1)}
      />
    </View>
  );
}
```

**Viktigt om state:**

- State är **privat** för komponenten
- När state ändras renderas komponenten om automatiskt
- Använd alltid setState-funktionen för att uppdatera state

### useEffect - Hantera sidoeffekter

**useEffect** är en Hook som låter dig utföra sidoeffekter i funktionella komponenter. Sidoeffekter är saker som: hämta data från internet, starta timers, prenumerera på events, etc.

```jsx
import { useEffect, useState } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // useEffect körs efter att komponenten renderats
  useEffect(() => {
    // Hämta användardata
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => response.json())
      .then(userData => {
        setUser(userData);
        setLoading(false);
      });
  }, [userId]); // Dependency array - körs om när userId ändras

  if (loading) {
    return <Text>Laddar användare...</Text>;
  }

  return (
    <View>
      <Text>Namn: {user.name}</Text>
      <Text>Email: {user.email}</Text>
    </View>
  );
}
```

**useEffect patterns:**

```jsx
// 1. Kör efter varje render
useEffect(() => {
  console.log("Komponenten har renderats");
});

// 2. Kör endast vid första render
useEffect(() => {
  console.log("Komponenten har monterats");
}, []);

// 3. Kör när specifika värden ändras
useEffect(() => {
  console.log("userId har ändrats");
}, [userId]);

// 4. Cleanup - städa upp när komponenten tas bort
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Timer tick");
  }, 1000);

  // Cleanup function
  return () => {
    clearInterval(timer);
  };
}, []);
```

### Praktiskt exempel: En komplett komponent

```jsx
import React, { useEffect, useState } from "react";
import { Button, Text, TextInput, View } from "react-native";

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [inputText, setInputText] = useState("");

  // Ladda todos när komponenten startar
  useEffect(() => {
    console.log("TodoApp har monterats");
    // Här skulle du kunna ladda todos från AsyncStorage eller API
  }, []);

  // Funktion för att lägga till ny todo
  const addTodo = () => {
    if (inputText.trim()) {
      const newTodo = {
        id: Date.now(),
        text: inputText,
        completed: false,
      };
      setTodos([...todos, newTodo]);
      setInputText(""); // Rensa input
    }
  };

  // Funktion för att markera todo som klar
  const toggleTodo = (id) => {
    setTodos(
      todos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ),
    );
  };

  return (
    <View>
      <Text>Min Todo-app</Text>

      <TextInput
        value={inputText}
        onChangeText={setInputText}
        placeholder="Skriv en ny uppgift"
      />

      <Button title="Lägg till" onPress={addTodo} />

      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={() => toggleTodo(todo.id)}
        />
      ))}
    </View>
  );
}

// Separat komponent för todo-items
function TodoItem({ todo, onToggle }) {
  return (
    <View>
      <Text
        style={{
          textDecorationLine: todo.completed ? "line-through" : "none",
        }}
      >
        {todo.text}
      </Text>
      <Button
        title={todo.completed ? "Ångra" : "Klar"}
        onPress={onToggle}
      />
    </View>
  );
}
```

### Sammanfattning: Viktiga koncept att komma ihåg

**Props:**

- Data som skickas från förälder till barn
- Read-only - kan inte ändras av barnkomponenten
- Gör komponenter återanvändbara

**State:**

- Komponentens privata data som kan ändras
- När state ändras renderas komponenten om
- Använd useState Hook för att hantera state

**useEffect:**

- För sidoeffekter (API-anrop, timers, prenumerationer)
- Kan köras vid olika tillfällen beroende på dependency array
- Kom ihåg cleanup för att undvika minnesläckor

**Hooks (allmänt):**

- Funktioner som börjar med "use"
- Låter dig "koppla in" React-funktionalitet i funktionella komponenter
- Följ Hook-reglerna: anropa endast på toppnivå, endast i React-komponenter

## Varför Expo?

För denna kurs kommer vi att använda **Expo** som vår utvecklingsplattform. Expo gör det enkelt att utveckla och testa applikationer.

### Analogi: Expo som ett färdigt husbyggsats

Tänk dig två sätt att bygga ett hus:

**Alternativ 1 (React Native CLI)**: Du köper mark, anlitar arkitekter, köper alla byggmaterial separat, anlitar olika specialister (elektriker, rörmokare, etc.). Du har full kontroll men det tar mycket tid och kunskap.

**Alternativ 2 (Expo)**: Du köper ett färdigt husbyggsats som innehåller allt du behöver: färdiga väggar, installationer, verktyg och en manual. Du kan börja bygga direkt och får ett fungerande hus snabbare.

Expo ger dig:

- **Snabb start**: Ingen komplex installation av Android Studio eller Xcode
- **Inbyggda verktyg**: Kamera, GPS, push-notiser fungerar direkt
- **Enkel testning**: Testa din app på riktiga enheter med Expo Go-appen
- **Hot Reload**: Se förändringar direkt på din telefon
- **Enklare publicering**: Enklare att publicera till App Store och Google Play

## Installation och Setup med Expo

### Steg 1: Förberedelser

Innan vi börjar behöver du:

- En dator (Windows, Mac eller Linux)
- Internet-anslutning
- En smartphone (Android eller iOS) för testning

### Steg 2: Installera Node.js

Node.js låter dig köra JavaScript på din dator (inte bara i webbläsaren).

```bash
# Kontrollera om du redan har Node.js
node --version

# Ska visa version 16.0.0 eller senare
# Om inte installerat, gå till https://nodejs.org/ och ladda ner
```

**För Windows:**

1. Gå till https://nodejs.org/
2. Ladda ner LTS-versionen
3. Kör installationsfilen
4. Starta om datorn

**För Mac:**

1. Gå till https://nodejs.org/
2. Ladda ner LTS-versionen
3. Kör .pkg-filen
4. Alternativt: använd Homebrew: `brew install node`

### Steg 3: Installera Expo CLI

```bash
# Installera Expo CLI globalt
npm install -g @expo/cli

# Verifiera installationen
expo --version
```

### Steg 4: Skapa ditt första projekt

```bash
# Skapa ett nytt Expo-projekt
npx create-expo-app@latest my-app --template blank

# Navigera till projektmappen
cd my-app

# Starta utvecklingsservern
npx expo start
```

När du kör `npx expo start` öppnas en QR-kod i terminalen och en webbsida med utvecklingsverktyg.

### Steg 5: Installera Expo Go på din telefon

**För Android:**

1. Gå till Google Play Store
2. Sök efter "Expo Go"
3. Installera appen

**För iOS:**

1. Gå till App Store
2. Sök efter "Expo Go"
3. Installera appen

### Steg 6: Testa din app

1. Öppna Expo Go på din telefon
2. Scanna QR-koden från terminalen (Android) eller från webbsidan (iOS)
3. Din app laddas och körs på telefonen!

## Hur React Native fungerar - Arkitektur

### Analogi: React Native som en tolk på ett internationellt möte

Föreställ dig ett möte där representanter från olika länder ska kommunicera. Istället för att alla ska lära sig varandras språk, använder de en tolk som:

1. **Lyssnar** på vad någon säger på sitt eget språk
2. **Förstår** innebörden i meddelandet
3. **Översätter** och förmedlar budskapet till andra deltagare på deras språk
4. **Säkerställer** att alla förstår samma sak

React Native fungerar på liknande sätt:

```
JavaScript Code (Din kod)
        ↓
JavaScript Thread (Tolken)
        ↓
Bridge (Kommunikationskanalen)
        ↓
Native Thread (Android/iOS)
        ↓
Native Components (UI-element)
```

### De tre huvuddelarna

**1. JavaScript Thread**

- Här körs din React Native-kod
- Hanterar affärslogik, state management, och API-anrop
- Kommunicerar med native sidan via "bridge"

**2. Bridge**

- Kommunikationskanalen mellan JavaScript och native kod
- Serialiserar data och meddelanden
- Asynkron kommunikation (non-blocking)

**3. Native Thread**

- Den riktiga Android/iOS-koden som körs
- Hanterar UI-rendering och systeminteraktioner
- Använder plattformens native komponenter

### Praktiskt exempel: En Button-komponent

När du skriver denna React Native-kod:

```jsx
import { Button } from "react-native";

function MyApp() {
  return (
    <Button
      title="Klicka här"
      onPress={() => alert("Hej!")}
    />
  );
}
```

Händer följande under huven:

1. **På Android**: React Native skapar en `android.widget.Button`
2. **På iOS**: React Native skapar en `UIButton`
3. **Samma kod**: Men resultatet ser native ut på båda plattformarna

## Grundläggande komponenter i React Native

Nu när vi förstår vad komponenter är, låt oss utforska de viktigaste inbyggda komponenterna.

### View - Byggstenen för layout

**Analogi**: `View` är som en tom låda eller behållare. Precis som du kan ha lådor i olika storlekar för att organisera saker hemma, använder du `View` för att organisera andra komponenter i din app.

```jsx
import React from "react";
import { StyleSheet, View } from "react-native";

function MyComponent() {
  return (
    <View style={styles.container}>
      {/* Andra komponenter går här */}
      <View style={styles.box}>
        {/* Detta är en låda inuti en annan låda */}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1, // Tar upp all tillgänglig plats
    backgroundColor: "#f0f0f0",
    padding: 20, // Mellanrum inuti behållaren
  },
  box: {
    width: 100,
    height: 100,
    backgroundColor: "blue",
  },
});
```

**Viktiga egenskaper för View:**

- `style` - Hur komponenten ska se ut och positioneras
- `children` - Andra komponenter som finns inuti denna View

### Text - För att visa text

**Analogi**: `Text` är som etiketter på lådorna. All text i React Native måste vara insluten i en `Text`-komponent. Du kan inte bara skriva text direkt.

```jsx
import React from "react";
import { StyleSheet, Text } from "react-native";

function MyTextComponent() {
  return (
    <View>
      <Text style={styles.title}>
        Välkommen till React Native!
      </Text>

      <Text style={styles.paragraph}>
        Detta är ett stycke text som förklarar något viktigt. Text-komponenten
        kan innehålla flera rader.
      </Text>

      <Text style={styles.bold}>
        Denna text är fet.
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  title: {
    fontSize: 24,
    fontWeight: "bold",
    color: "#333",
    textAlign: "center",
    marginBottom: 10, // Mellanrum under titeln
  },
  paragraph: {
    fontSize: 16,
    color: "#666",
    lineHeight: 24, // Höjd mellan rader
    marginBottom: 10,
  },
  bold: {
    fontSize: 16,
    fontWeight: "bold",
    color: "red",
  },
});
```

**Viktiga egenskaper för Text:**

- `style` - Styling som fontSize, color, fontWeight
- `numberOfLines` - Begränsa antal rader
- `onPress` - Gör texten klickbar

### Button - För användarinteraktion

**Analogi**: `Button` är som ljusknapparna hemma. Användare trycker på dem för att få något att hända.

```jsx
import React from "react";
import { Alert, Button, View } from "react-native";

function MyButtonComponent() {
  // Funktion som körs när knappen trycks
  const handlePress = () => {
    Alert.alert("Information", "Du tryckte på knappen!");
  };

  const handleSecondPress = () => {
    console.log("Knapp 2 trycktes!"); // Syns i utvecklarkonsolen
  };

  return (
    <View style={{ padding: 20 }}>
      {/* Grundläggande knapp */}
      <Button
        title="Tryck här"
        onPress={handlePress}
        color="#007AFF" // iOS blå färg
      />

      {/* Knapp med annan funktion */}
      <View style={{ marginTop: 10 }}>
        <Button
          title="Andra knappen"
          onPress={handleSecondPress}
          color="#FF6B6B" // Röd färg
        />
      </View>

      {/* Inaktiverad knapp */}
      <View style={{ marginTop: 10 }}>
        <Button
          title="Inaktiverad knapp"
          onPress={() => {}} // Tom funktion
          disabled={true} // Knappen är inaktiverad
        />
      </View>
    </View>
  );
}
```

**Viktiga egenskaper för Button:**

- `title` - Texten som visas på knappen
- `onPress` - Funktion som körs när knappen trycks
- `color` - Färg på knappen
- `disabled` - Om knappen ska vara inaktiverad

### Image - För att visa bilder

**Analogi**: `Image` är som ramar på väggen hemma. Du kan visa foton, ikoner eller andra bilder.

```jsx
import React from "react";
import { Image, StyleSheet, View } from "react-native";

function MyImageComponent() {
  return (
    <View style={styles.container}>
      {/* Bild från internet */}
      <Image
        source={{
          uri: "https://reactnative.dev/img/tiny_logo.png",
        }}
        style={styles.networkImage}
      />

      {/* Lokal bild (måste importeras först) */}
      <Image
        source={require("./assets/my-local-image.png")}
        style={styles.localImage}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    padding: 20,
    alignItems: "center",
  },
  networkImage: {
    width: 100,
    height: 100,
    marginBottom: 20,
  },
  localImage: {
    width: 150,
    height: 150,
    borderRadius: 75, // Gör bilden rund
  },
});
```

### ScrollView - För scrollbart innehåll

**Analogi**: `ScrollView` är som en bokrulle. När innehållet är längre än skärmen kan användaren scrolla för att se resten.

```jsx
import React from "react";
import { ScrollView, StyleSheet, Text, View } from "react-native";

function MyScrollComponent() {
  return (
    <ScrollView style={styles.scrollContainer}>
      <Text style={styles.title}>Scrollbart innehåll</Text>

      {/* Skapa många text-element för att demonstrera scroll */}
      {[...Array(50)].map((_, index) => (
        <Text key={index} style={styles.item}>
          Detta är item nummer {index + 1}
        </Text>
      ))}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  scrollContainer: {
    flex: 1,
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: "bold",
    marginBottom: 20,
    textAlign: "center",
  },
  item: {
    fontSize: 16,
    padding: 10,
    marginBottom: 5,
    backgroundColor: "#f0f0f0",
    borderRadius: 5,
  },
});
```

### TextInput - För användarinmatning

**Analogi**: `TextInput` är som ett formulärfält på papper där användaren kan skriva information.

```jsx
import React, { useState } from "react";
import { StyleSheet, Text, TextInput, View } from "react-native";

function MyTextInputComponent() {
  // State för att lagra vad användaren skriver
  const [text, setText] = useState("");
  const [name, setName] = useState("");

  return (
    <View style={styles.container}>
      <Text style={styles.label}>Skriv något:</Text>
      <TextInput
        style={styles.input}
        value={text}
        onChangeText={setText} // Uppdatera state när användaren skriver
        placeholder="Skriv här..."
        placeholderTextColor="#999"
      />

      <Text style={styles.output}>Du skrev: {text}</Text>

      <Text style={styles.label}>Ditt namn:</Text>
      <TextInput
        style={styles.input}
        value={name}
        onChangeText={setName}
        placeholder="Ange ditt namn"
        autoCapitalize="words" // Stora bokstäver på första bokstaven i ord
      />

      {name.length > 0 && <Text style={styles.greeting}>Hej {name}!</Text>}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    padding: 20,
  },
  label: {
    fontSize: 16,
    fontWeight: "bold",
    marginBottom: 5,
  },
  input: {
    borderWidth: 1,
    borderColor: "#ddd",
    borderRadius: 5,
    padding: 10,
    fontSize: 16,
    marginBottom: 15,
    backgroundColor: "#fff",
  },
  output: {
    fontSize: 16,
    color: "#666",
    marginBottom: 20,
  },
  greeting: {
    fontSize: 18,
    color: "green",
    fontWeight: "bold",
  },
});
```

## Din första React Native-app med Expo

Nu när vi förstår grundkomponenterna, låt oss skapa en komplett app som kombinerar allt vi lärt oss.

### Projektstruktur i Expo

När du skapar ett Expo-projekt får du denna struktur:

```
MyFirstApp/
├── App.js              # Huvudkomponenten (här börjar allt)
├── app.json           # Expo-konfiguration
├── package.json       # Projektberoenden
├── assets/            # Bilder, ikoner, etc.
├── node_modules/      # Installerade paket (genereras automatiskt)
└── .expo/            # Expo-specifika filer (genereras automatiskt)
```

**Viktigt att förstå:**

- `App.js` är ingångspunkten - din huvudkomponent
- `assets/` är där du lägger bilder, ikoner och andra filer
- `package.json` listar alla paket din app använder

### Komplett exempel - "Min första app"

Låt oss skapa en app som visar alla koncept vi lärt oss:

```jsx
// App.js - Detta är din huvudfil
import React, { useState } from "react";
import {
  Alert,
  Button,
  Image,
  SafeAreaView,
  ScrollView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from "react-native";

// Detta är huvudkomponenten för hela appen
function App() {
  // State - variabler som kan ändras och uppdatera gränssnittet
  const [name, setName] = useState("");
  const [message, setMessage] = useState("");
  const [clickCount, setClickCount] = useState(0);

  // Funktion som körs när användaren trycker på "Säg hej"-knappen
  const sayHello = () => {
    if (name.trim() === "") {
      Alert.alert("Fel", "Du måste ange ett namn först!");
      return;
    }

    const greeting = `Hej ${name}! Välkommen till React Native!`;
    setMessage(greeting);
    Alert.alert("Hälsning", greeting);
  };

  // Funktion för att räkna klick
  const incrementCounter = () => {
    const newCount = clickCount + 1;
    setClickCount(newCount);

    // Visa speciella meddelanden vid vissa antal klick
    if (newCount === 5) {
      Alert.alert("Bra jobbat!", "Du har klickat 5 gånger!");
    } else if (newCount === 10) {
      Alert.alert("Wow!", "Du har klickat 10 gånger! Du är en klickexpert!");
    }
  };

  // Funktion för att återställa allt
  const resetEverything = () => {
    setName("");
    setMessage("");
    setClickCount(0);
    Alert.alert("Återställt", "Allt har återställts!");
  };

  // Detta är vad som renderas (visas) på skärmen
  return (
    <SafeAreaView style={styles.container}>
      <ScrollView style={styles.scrollContainer}>
        {/* Header-sektion med bild och titel */}
        <View style={styles.header}>
          <Image
            source={{
              uri: "https://reactnative.dev/img/tiny_logo.png",
            }}
            style={styles.logo}
          />
          <Text style={styles.title}>Min första React Native-app</Text>
          <Text style={styles.subtitle}>
            Byggd med Expo och mycket kärlek ❤️
          </Text>
        </View>

        {/* Namn-sektion */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>👋 Vad heter du?</Text>
          <TextInput
            style={styles.input}
            value={name}
            onChangeText={setName}
            placeholder="Ange ditt namn här..."
            placeholderTextColor="#999"
          />

          <Button
            title="Säg hej!"
            onPress={sayHello}
            color="#4CAF50"
          />

          {/* Visa hälsningsmeddelandet om det finns */}
          {message !== "" && (
            <View style={styles.messageContainer}>
              <Text style={styles.message}>{message}</Text>
            </View>
          )}
        </View>

        {/* Klick-räknare sektion */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>🖱️ Klick-räknare</Text>
          <View style={styles.counterContainer}>
            <Text style={styles.counterLabel}>Antal klick:</Text>
            <Text style={styles.counterValue}>{clickCount}</Text>
          </View>

          <View style={styles.buttonRow}>
            <View style={styles.buttonContainer}>
              <Button
                title="Klicka!"
                onPress={incrementCounter}
                color="#2196F3"
              />
            </View>
          </View>
        </View>

        {/* Information sektion */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>ℹ️ Om denna app</Text>
          <Text style={styles.infoText}>
            Denna app visar grundläggande React Native-komponenter:
          </Text>
          <Text style={styles.bulletPoint}>
            • View - för layout och struktur
          </Text>
          <Text style={styles.bulletPoint}>• Text - för att visa text</Text>
          <Text style={styles.bulletPoint}>
            • TextInput - för användarinput
          </Text>
          <Text style={styles.bulletPoint}>• Button - för interaktion</Text>
          <Text style={styles.bulletPoint}>• Image - för att visa bilder</Text>
          <Text style={styles.bulletPoint}>
            • ScrollView - för scrollbart innehåll
          </Text>
        </View>

        {/* Reset-knapp längst ner */}
        <View style={styles.resetSection}>
          <Button
            title="Återställ allt"
            onPress={resetEverything}
            color="#FF6B6B"
          />
        </View>

        {/* Footer */}
        <View style={styles.footer}>
          <Text style={styles.footerText}>
            Skapat med React Native och Expo 🚀
          </Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

// Styling för alla komponenter
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#f8f9fa",
  },
  scrollContainer: {
    flex: 1,
  },
  header: {
    alignItems: "center",
    padding: 30,
    backgroundColor: "#fff",
    borderBottomWidth: 1,
    borderBottomColor: "#e0e0e0",
  },
  logo: {
    width: 80,
    height: 80,
    marginBottom: 15,
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#2c3e50",
    textAlign: "center",
    marginBottom: 5,
  },
  subtitle: {
    fontSize: 16,
    color: "#7f8c8d",
    textAlign: "center",
  },
  section: {
    margin: 20,
    padding: 20,
    backgroundColor: "#fff",
    borderRadius: 10,
    // Skugga för iOS
    shadowColor: "#000",
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.1,
    shadowRadius: 3.84,
    // Skugga för Android
    elevation: 5,
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: "bold",
    color: "#34495e",
    marginBottom: 15,
  },
  input: {
    borderWidth: 1,
    borderColor: "#ddd",
    borderRadius: 8,
    padding: 12,
    fontSize: 16,
    marginBottom: 15,
    backgroundColor: "#f9f9f9",
  },
  messageContainer: {
    marginTop: 15,
    padding: 15,
    backgroundColor: "#e8f5e8",
    borderRadius: 8,
    borderLeftWidth: 4,
    borderLeftColor: "#4CAF50",
  },
  message: {
    fontSize: 16,
    color: "#2e7d32",
    fontWeight: "500",
  },
  counterContainer: {
    alignItems: "center",
    marginBottom: 20,
  },
  counterLabel: {
    fontSize: 18,
    color: "#555",
    marginBottom: 10,
  },
  counterValue: {
    fontSize: 48,
    fontWeight: "bold",
    color: "#2196F3",
  },
  buttonRow: {
    flexDirection: "row",
    justifyContent: "center",
  },
  buttonContainer: {
    minWidth: 120,
  },
  infoText: {
    fontSize: 16,
    color: "#666",
    marginBottom: 10,
    lineHeight: 24,
  },
  bulletPoint: {
    fontSize: 14,
    color: "#777",
    marginBottom: 5,
    paddingLeft: 10,
  },
  resetSection: {
    margin: 20,
    marginTop: 10,
  },
  footer: {
    padding: 30,
    alignItems: "center",
  },
  footerText: {
    fontSize: 14,
    color: "#95a5a6",
    fontStyle: "italic",
  },
});

// Exportera komponenten så den kan användas
export default App;
```

### Förklaring av exemplet

**Struktur och organisation:**

1. **Import-sektion**: Vi importerar alla komponenter vi behöver från React Native
2. **State-variabler**: Använder `useState` för att lagra data som kan ändras
3. **Event handlers**: Funktioner som körs när användaren interagerar med appen
4. **Rendering**: JSX som beskriver hur appen ska se ut
5. **Styling**: StyleSheet objekt som definierar utseendet

**Viktiga koncept som demonstreras:**

- **Komponenter**: Alla byggstenar (View, Text, Button, etc.)
- **State**: Hur data lagras och uppdateras (`useState`)
- **Props**: Hur data skickas till komponenter (t.ex. `title` på Button)
- **Event handling**: Reagera på användarinteraktioner (`onPress`, `onChangeText`)
- **Conditional rendering**: Visa innehåll bara under vissa förhållanden (`{message !== '' && ...}`)
- **Styling**: Använda StyleSheet för att styla komponenter
- **Layout**: Använd flex och andra layout-egenskaper för att positionera element

**Så här testar du appen:**

1. Kör `npx expo start` i projektmappen
2. Scanna QR-koden med Expo Go på din telefon
3. Testa olika funktioner:
   - Skriv ditt namn och tryck "Säg hej!"
   - Klicka på klick-räknaren flera gånger
   - Scrolla ner för att se all information
   - Tryck "Återställ allt" för att rensa allt

## Grundläggande skillnader mellan Android och iOS

### Design och användarupplevelse

**iOS (Apple Design)**

- **Design Philosophy**: Minimalistisk, ren design med mycket whitespace
- **Navigation**: Ofta navigation bars i toppen och tab bars i botten
- **Typografi**: San Francisco font som standard
- **Ikoner**: Rundade fyrkanter med gradient-bakgrunder
- **Färger**: Mjukare färger, systemblå (#007AFF) som accent
- **Skuggor**: Mer subtila, mjuka skuggor

**Android (Material Design)**

- **Design Philosophy**: Material Design med skuggor, djup och animationer
- **Navigation**: Drawer navigation, floating action buttons
- **Typografi**: Roboto font som standard
- **Ikoner**: Mer geometriska former, outline-stil vanlig
- **Färger**: Kraftigare färger, mer kontrast
- **Skuggor**: Tydligare definierade skuggor (elevation)

### Plattformsdetektering i Expo

React Native och Expo ger dig verktyg för att känna av vilken plattform appen körs på:

```jsx
import { Platform } from "react-native";

function MyPlatformAwareComponent() {
  return (
    <View>
      <Text>Du kör på: {Platform.OS}</Text>

      {/* Visa olika innehåll beroende på plattform */}
      {Platform.OS === "ios"
        ? <Text>Detta är iOS!</Text>
        : <Text>Detta är Android!</Text>}

      {/* Olika styling för olika plattformar */}
      <View
        style={Platform.OS === "ios"
          ? styles.iosContainer
          : styles.androidContainer}
      >
        <Text>Plattformsspecifik styling</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  iosContainer: {
    backgroundColor: "#f0f0f0",
    borderRadius: 10,
    padding: 15,
    // iOS-stil skugga
    shadowColor: "#000",
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
  },
  androidContainer: {
    backgroundColor: "#ffffff",
    borderRadius: 4,
    padding: 15,
    // Android-stil elevation
    elevation: 5,
  },
});
```

### Platform.select() för mer komplex plattformslogik

```jsx
import { Platform, StyleSheet } from "react-native";

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: "#f2f2f2",
        paddingTop: 20, // För iOS status bar
      },
      android: {
        backgroundColor: "#ffffff",
        paddingTop: 0,
      },
    }),
  },
  text: {
    ...Platform.select({
      ios: {
        fontFamily: "Helvetica",
        fontSize: 17,
      },
      android: {
        fontFamily: "Roboto",
        fontSize: 16,
      },
    }),
  },
});
```

### Systembeteenden som skiljer sig

**1. Navigation och "tillbaka"-beteende:**

```jsx
import { BackHandler } from "react-native";

// På Android finns hårdvaru-tillbaka-knapp
// På iOS använder man ofta swipe-gester

// Hantera Android tillbaka-knapp
useEffect(() => {
  const backAction = () => {
    Alert.alert("Avsluta", "Vill du avsluta appen?", [
      { text: "Avbryt", onPress: () => null },
      { text: "Ja", onPress: () => BackHandler.exitApp() },
    ]);
    return true;
  };

  const backHandler = BackHandler.addEventListener(
    "hardwareBackPress",
    backAction,
  );

  return () => backHandler.remove();
}, []);
```

**2. Status bar:**

```jsx
import { StatusBar } from "react-native";

function App() {
  return (
    <View style={{ flex: 1 }}>
      {/* Konfigurera status bar för varje plattform */}
      <StatusBar
        barStyle={Platform.OS === "ios" ? "dark-content" : "light-content"}
        backgroundColor={Platform.OS === "android" ? "#6200EE" : undefined}
      />
      {/* Resten av din app */}
    </View>
  );
}
```

### Nästa steg

I **Vecka 2** kommer vi att fördjupa oss i användargränssnitt och design:

- Avancerad styling och layout med Flexbox
- Responsiv design för olika skärmstorlekar
- Navigation mellan skärmar
- Animationer och användarinteraktioner
- Plattformsspecifik design

### Praktiska övningsförslag

För att befästa veckans kunskaper, prova dessa övningar:

**1. Experimentera med grundkomponenter:**

- Skapa en app med alla komponenter vi lärt oss
- Testa olika styling-alternativ
- Kombinera komponenter på kreativa sätt

**2. Bygg en enkel miniräknare:**

- Använd TextInput för nummer
- Button för operationer (+, -, *, /)
- Text för att visa resultatet
- State för att lagra värden

**3. Skapa en "Om mig"-app:**

- Visa ditt namn, bild och beskrivning
- Lägg till kontaktinformation
- Använd ScrollView för längre innehåll

**4. Testa plattformsskillnader:**

- Använd Expo Go på både Android och iOS (om möjligt)
- Notera skillnader i utseende och beteende
- Experimentera med Platform.select()

**5. AI-experiment:**

- Be AI att förklara ett koncept du inte förstår
- Få hjälp att förbättra din kod
- Generera testdata för dina appar

### Felsökning - Vanliga problem och lösningar

**Problem**: "npx expo start" fungerar inte
**Lösning**:

```bash
# Kontrollera Node.js version
node --version

# Rensa npm cache
npm cache clean --force

# Installera om Expo CLI
npm install -g @expo/cli
```

**Problem**: App laddar inte i Expo Go
**Lösning**:

- Kontrollera att telefon och dator är på samma WiFi
- Starta om både utvecklingsservern och Expo Go-appen
- Prova att skanna QR-koden igen

**Problem**: "Cannot resolve module" felmeddelanden
**Lösning**:

```bash
# Installera beroenden
npm install

# Starta om Metro bundler
npx expo start --clear
```

### Viktiga takeaways

🎯 **React Native låter dig bygga native appar med JavaScript** - en kodbas, två plattformar

🧱 **Komponenter är byggstenar** - lär dig de grundläggande komponenterna väl

📱 **Expo gör utveckling enklare** - perfekt för att komma igång snabbt

🔄 **State gör appar interaktiva** - utan state är din app bara statisk

🎨 **Styling fungerar annorlunda** - använd StyleSheet, inte CSS

🤖 **AI kan hjälpa, men ersätter inte lärande** - förstå alltid vad du gör

---

_Nästa: [Vecka 2 - Användargränssnitt och Design](./vecka-2.md) →_
