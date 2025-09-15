# Vecka 2: Användargränssnitt och Design

## Lärandemål för veckan

Efter denna vecka ska du kunna:

- Förstå och använda Flexbox för layout i React Native
- Skapa responsiva användargränssnitt som fungerar på olika skärmstorlekar
- Använda avancerade styling-tekniker och animationer
- Implementera navigation mellan olika skärmar
- Tillämpa designprinciper för mobila applikationer
- Skapa tilltalande och användarvänliga gränssnitt

## Introduktion till mobil UI/UX-design

### Analogi: Designa ett hem vs designa för mobil

Tänk dig skillnaden mellan att designa ett hem och designa för mobil:

**Designa ett hem (Desktop/Web):**

- Mycket plats att arbeta med
- Människor sitter bekvämt i soffor
- Kan använda mus för precision
- Stor skärm för att visa mycket information samtidigt

**Designa för mobil:**

- Begränsat utrymme (som att designa en husbil)
- Människor är ofta i rörelse eller distraherade
- Använder tummar för navigering
- Liten skärm kräver prioritering av innehåll

### Grundprinciper för mobil design

**1. Thumb-First Design (Tumme-först design)**
Människor håller telefoner med en hand och använder tummen för navigering.

```jsx
// Placera viktiga knappar inom "thumb zone"
const styles = StyleSheet.create({
  bottomButton: {
    position: "absolute",
    bottom: 30, // Lätt att nå med tummen
    left: 20,
    right: 20,
    height: 50,
    backgroundColor: "#007AFF",
    borderRadius: 25,
  },
  topButton: {
    // Undvik att placera viktiga knappar längst upp
    // Svårt att nå med tummen på stora telefoner
  },
});
```

**2. Touch Target Size (Pekområden)**
Knappar och klickbara element måste vara tillräckligt stora för fingrar.

```jsx
const styles = StyleSheet.create({
  // För liten - svår att träffa
  tooSmallButton: {
    width: 30,
    height: 30,
  },

  // Lagom storlek - lätt att träffa
  goodSizeButton: {
    minWidth: 44, // iOS rekommendation
    minHeight: 44,
    padding: 12,
  },

  // Större för viktiga aktioner
  primaryButton: {
    minWidth: 60,
    minHeight: 60,
    padding: 16,
  },
});
```

**3. Visual Hierarchy (Visuell hierarki)**
Hjälp användare att förstå vad som är viktigast.

```jsx
const styles = StyleSheet.create({
  // Viktigast - största, fetast
  primaryHeading: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#000",
    marginBottom: 8,
  },

  // Näst viktigast
  secondaryHeading: {
    fontSize: 20,
    fontWeight: "600",
    color: "#333",
    marginBottom: 6,
  },

  // Minst viktigt
  bodyText: {
    fontSize: 16,
    fontWeight: "normal",
    color: "#666",
    lineHeight: 24,
  },
});
```

## Flexbox - Layoutsystemet i React Native

### Analogi: Flexbox som en smart byrå

Tänk dig en magisk byrå som automatiskt organiserar dina kläder:

- **Flex Direction**: Bestämmer om kläderna läggs i rader (row) eller staplas (column)
- **Justify Content**: Hur kläderna distribueras längs huvudaxeln (vänster, höger, centrerat, utspritt)
- **Align Items**: Hur kläderna justeras tvärs huvudaxeln (topp, botten, centrerat)
- **Flex**: Hur mycket plats varje klädesplagg ska ta upp

### Grundläggande Flexbox-koncept

**Huvudaxel vs Tväraxel:**

```jsx
// Default: column (vertikal stack)
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "column", // Huvudaxel: top → bottom
    // Tväraxel: left ↔ right
  },
});

// Ändra till row (horisontell rad)
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "row", // Huvudaxel: left → right
    // Tväraxel: top ↕ bottom
  },
});
```

### Praktiska Flexbox-exempel

**1. Centrerat innehåll**

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

function CenteredContent() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Jag är centrerad!</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center", // Centrerar vertikalt
    alignItems: "center", // Centrerar horisontellt
    backgroundColor: "#f0f0f0",
  },
  text: {
    fontSize: 20,
    fontWeight: "bold",
  },
});
```

**2. Header, Content, Footer layout**

```jsx
function AppLayout() {
  return (
    <View style={styles.container}>
      {/* Header - fast höjd */}
      <View style={styles.header}>
        <Text style={styles.headerText}>Header</Text>
      </View>

      {/* Content - tar upp resterande utrymme */}
      <View style={styles.content}>
        <Text>Detta är innehållet som expanderar</Text>
      </View>

      {/* Footer - fast höjd */}
      <View style={styles.footer}>
        <Text style={styles.footerText}>Footer</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "column",
  },
  header: {
    height: 80, // Fast höjd
    backgroundColor: "#007AFF",
    justifyContent: "center",
    alignItems: "center",
  },
  content: {
    flex: 1, // Tar upp all resterande plats
    backgroundColor: "#fff",
    padding: 20,
  },
  footer: {
    height: 60, // Fast höjd
    backgroundColor: "#333",
    justifyContent: "center",
    alignItems: "center",
  },
  headerText: {
    color: "#fff",
    fontSize: 18,
    fontWeight: "bold",
  },
  footerText: {
    color: "#fff",
    fontSize: 14,
  },
});
```

**3. Sidovid layout (sidebar + main)**

```jsx
function SidebarLayout() {
  return (
    <View style={styles.container}>
      {/* Sidebar - fast bredd */}
      <View style={styles.sidebar}>
        <Text style={styles.sidebarText}>Menu</Text>
      </View>

      {/* Main content - resterande utrymme */}
      <View style={styles.mainContent}>
        <Text>Huvudinnehåll här</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "row", // Horisontell layout
  },
  sidebar: {
    width: 100, // Fast bredd
    backgroundColor: "#34495e",
    padding: 10,
  },
  mainContent: {
    flex: 1, // Tar upp resterande bredd
    backgroundColor: "#fff",
    padding: 20,
  },
  sidebarText: {
    color: "#fff",
    fontSize: 16,
  },
});
```

**4. Distribuerade element**

```jsx
function DistributedElements() {
  return (
    <View style={styles.container}>
      <Text style={styles.item}>Första</Text>
      <Text style={styles.item}>Andra</Text>
      <Text style={styles.item}>Tredje</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "row",
    justifyContent: "space-between", // Jämn fördelning
    alignItems: "center",
    padding: 20,
  },
  item: {
    fontSize: 16,
    padding: 10,
    backgroundColor: "#e0e0e0",
    borderRadius: 5,
  },
});

// Andra justifyContent-alternativ:
// 'flex-start' - allt till vänster/topp
// 'flex-end' - allt till höger/botten
// 'center' - allt centrerat
// 'space-around' - lika mellanrum runt varje element
// 'space-evenly' - exakt lika mellanrum överallt
```

## Responsiv design för olika skärmstorlekar

### Analogi: Kläder som passar alla storlekar

Precis som kläder behöver finnas i olika storlekar för att passa alla människor, behöver mobilappar anpassa sig till olika skärmstorlekar - från små telefoner till stora surfplattor.

### Få skärmdimensioner

```jsx
import { Dimensions, Platform } from "react-native";

// Få aktuella skärmdimensioner
const { width, height } = Dimensions.get("window");
const screenData = Dimensions.get("screen"); // Inkluderar status bar

console.log(`Skärmbredd: ${width}`);
console.log(`Skärmhöjd: ${height}`);

// Lyssna på orientationsändringar
import { useEffect, useState } from "react";

function useDeviceOrientation() {
  const [screenData, setScreenData] = useState(Dimensions.get("window"));

  useEffect(() => {
    const onChange = (result) => {
      setScreenData(result.window);
    };

    const subscription = Dimensions.addEventListener("change", onChange);
    return () => subscription?.remove();
  }, []);

  return {
    ...screenData,
    isLandscape: screenData.width > screenData.height,
    isPortrait: screenData.height > screenData.width,
  };
}
```

### Responsiva komponenter

```jsx
import React from "react";
import { Dimensions, StyleSheet, Text, View } from "react-native";

const { width } = Dimensions.get("window");

function ResponsiveGrid() {
  // Beräkna antal kolumner baserat på skärmbredd
  const getColumns = () => {
    if (width < 400) return 2; // Små telefoner
    if (width < 600) return 3; // Stora telefoner
    if (width < 900) return 4; // Små surfplattor
    return 5; // Stora surfplattor
  };

  const columns = getColumns();
  const itemWidth = (width - 40 - (columns - 1) * 10) / columns; // 40px padding, 10px gap

  return (
    <View style={styles.container}>
      <Text style={styles.title}>
        Responsiv grid ({columns} kolumner)
      </Text>

      <View style={styles.grid}>
        {[...Array(12)].map((_, index) => (
          <View
            key={index}
            style={[styles.gridItem, { width: itemWidth }]}
          >
            <Text style={styles.itemText}>{index + 1}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: "#f5f5f5",
  },
  title: {
    fontSize: 20,
    fontWeight: "bold",
    marginBottom: 20,
    textAlign: "center",
  },
  grid: {
    flexDirection: "row",
    flexWrap: "wrap",
    justifyContent: "space-between",
    gap: 10, // Mellanrum mellan element (kräver React Native 0.71+)
  },
  gridItem: {
    aspectRatio: 1, // Kvadratiska element
    backgroundColor: "#007AFF",
    borderRadius: 8,
    justifyContent: "center",
    alignItems: "center",
    marginBottom: 10,
  },
  itemText: {
    color: "#fff",
    fontSize: 16,
    fontWeight: "bold",
  },
});
```

### Responsiv typografi

```jsx
import { Dimensions, PixelRatio } from "react-native";

const { width } = Dimensions.get("window");

// Funktioner för responsiv storlek
const scale = (size) => (width / 320) * size;
const moderateScale = (size, factor = 0.5) =>
  size + (scale(size) - size) * factor;

const responsiveFontSize = (size) => {
  const newSize = moderateScale(size);
  return Math.max(newSize, size * 0.8); // Minimum storlek
};

const styles = StyleSheet.create({
  title: {
    fontSize: responsiveFontSize(24),
    fontWeight: "bold",
  },
  body: {
    fontSize: responsiveFontSize(16),
    lineHeight: responsiveFontSize(24),
  },
  small: {
    fontSize: responsiveFontSize(12),
  },
});
```

### Hantera orientationsändringar

```jsx
import React, { useEffect, useState } from "react";
import { Dimensions, StyleSheet, Text, View } from "react-native";

function OrientationAwareComponent() {
  const [orientation, setOrientation] = useState("portrait");
  const [screenData, setScreenData] = useState(Dimensions.get("window"));

  useEffect(() => {
    const updateLayout = (dims) => {
      setScreenData(dims.window);
      setOrientation(
        dims.window.width > dims.window.height ? "landscape" : "portrait",
      );
    };

    const subscription = Dimensions.addEventListener("change", updateLayout);
    return () => subscription?.remove();
  }, []);

  const isLandscape = orientation === "landscape";

  return (
    <View
      style={[
        styles.container,
        isLandscape ? styles.landscapeContainer : styles.portraitContainer,
      ]}
    >
      <Text style={styles.orientationText}>
        Nuvarande orientering: {orientation}
      </Text>

      <Text style={styles.dimensionsText}>
        {screenData.width} x {screenData.height}
      </Text>

      <View
        style={[
          styles.contentBox,
          isLandscape ? styles.landscapeContent : styles.portraitContent,
        ]}
      >
        <Text style={styles.contentText}>
          Innehåll som anpassar sig efter orientering
        </Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    justifyContent: "center",
    alignItems: "center",
  },
  portraitContainer: {
    backgroundColor: "#e3f2fd",
  },
  landscapeContainer: {
    backgroundColor: "#f3e5f5",
  },
  contentBox: {
    backgroundColor: "#fff",
    borderRadius: 10,
    padding: 20,
    marginTop: 20,
    justifyContent: "center",
    alignItems: "center",
  },
  portraitContent: {
    width: "90%",
    height: 200,
  },
  landscapeContent: {
    width: "70%",
    height: 150,
  },
  orientationText: {
    fontSize: 20,
    fontWeight: "bold",
    color: "#333",
  },
  dimensionsText: {
    fontSize: 16,
    color: "#666",
    marginTop: 10,
  },
  contentText: {
    fontSize: 16,
    textAlign: "center",
    color: "#333",
  },
});
```

## Avancerad styling och animationer

### Gradients och avancerade effekter

React Native stöder inte CSS-gradients direkt, men vi kan använda bibliotek eller skapa effekter med shadows och colors.

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

function StylishCard() {
  return (
    <View style={styles.card}>
      <Text style={styles.cardTitle}>Stilfullt kort</Text>
      <Text style={styles.cardText}>
        Med skuggor och rundade hörn
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    backgroundColor: "#fff",
    borderRadius: 15,
    padding: 20,
    margin: 15,

    // iOS skugga
    shadowColor: "#000",
    shadowOffset: {
      width: 0,
      height: 4,
    },
    shadowOpacity: 0.3,
    shadowRadius: 6,

    // Android skugga
    elevation: 8,

    // Subtil border
    borderWidth: 1,
    borderColor: "rgba(0,0,0,0.1)",
  },
  cardTitle: {
    fontSize: 20,
    fontWeight: "bold",
    color: "#333",
    marginBottom: 10,
  },
  cardText: {
    fontSize: 16,
    color: "#666",
    lineHeight: 24,
  },
});
```

### Enkla animationer med Animated API

```jsx
import React, { useEffect, useRef } from "react";
import {
  Animated,
  StyleSheet,
  Text,
  TouchableOpacity,
  View,
} from "react-native";

function AnimatedButton() {
  const scaleValue = useRef(new Animated.Value(1)).current;
  const opacityValue = useRef(new Animated.Value(0)).current;

  // Fade in animation vid start
  useEffect(() => {
    Animated.timing(opacityValue, {
      toValue: 1,
      duration: 1000,
      useNativeDriver: true,
    }).start();
  }, []);

  // Press animation
  const onPressIn = () => {
    Animated.spring(scaleValue, {
      toValue: 0.95,
      useNativeDriver: true,
    }).start();
  };

  const onPressOut = () => {
    Animated.spring(scaleValue, {
      toValue: 1,
      friction: 3,
      tension: 40,
      useNativeDriver: true,
    }).start();
  };

  return (
    <Animated.View
      style={[
        styles.container,
        {
          opacity: opacityValue,
          transform: [{ scale: scaleValue }],
        },
      ]}
    >
      <TouchableOpacity
        style={styles.button}
        onPressIn={onPressIn}
        onPressOut={onPressOut}
        activeOpacity={1}
      >
        <Text style={styles.buttonText}>Animerad knapp</Text>
      </TouchableOpacity>
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  container: {
    margin: 20,
  },
  button: {
    backgroundColor: "#007AFF",
    paddingHorizontal: 30,
    paddingVertical: 15,
    borderRadius: 25,
    justifyContent: "center",
    alignItems: "center",
  },
  buttonText: {
    color: "#fff",
    fontSize: 16,
    fontWeight: "bold",
  },
});
```

### Pulserande animation

```jsx
import React, { useEffect, useRef } from "react";
import { Animated, StyleSheet, View } from "react-native";

function PulsingDot() {
  const pulseAnimation = useRef(new Animated.Value(1)).current;

  useEffect(() => {
    const pulse = () => {
      Animated.sequence([
        Animated.timing(pulseAnimation, {
          toValue: 1.3,
          duration: 800,
          useNativeDriver: true,
        }),
        Animated.timing(pulseAnimation, {
          toValue: 1,
          duration: 800,
          useNativeDriver: true,
        }),
      ]).start(() => pulse()); // Loopa animationen
    };

    pulse();
  }, []);

  return (
    <View style={styles.container}>
      <Animated.View
        style={[
          styles.dot,
          {
            transform: [{ scale: pulseAnimation }],
          },
        ]}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    justifyContent: "center",
    alignItems: "center",
    padding: 50,
  },
  dot: {
    width: 50,
    height: 50,
    borderRadius: 25,
    backgroundColor: "#FF6B6B",
  },
});
```

## Navigation mellan skärmar

Navigation är grunden för att skapa flöden i mobilappar. Vi använder Expo Router, det moderna fil-baserade navigationssystemet för Expo och React Native.

### Installation av Expo Router

Detta görs automatiskt vid skapande av ett fullt Expo projekt, men man kan också installera det manuellt:

```bash
# Installera Expo Router
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar

# Uppdatera package.json
npm install react-native-reanimated
```

### Grundläggande Stack Navigation

Med Expo Router skapar vi navigation genom att skapa filer i `app`-mappen. Strukturen ser ut så här:

```
app/
  _layout.js
  index.js
  details.js
  profile.js
```

#### `app/_layout.js` - Rot-layout

Denna komponent bestämmer hur alla sidor (tabs) skall se ut, eller snarare vilka delar som skall delas mellan varje tab.

```jsx
import { Stack } from "expo-router";

export default function RootLayout() {
  return (
    <Stack
      screenOptions={{
        headerStyle: {
          backgroundColor: "#007AFF",
        },
        headerTintColor: "#fff",
        headerTitleStyle: {
          fontWeight: "bold",
        },
      }}
    >
      <Stack.Screen
        name="index"
        options={{
          title: "Hemskärm",
        }}
      />
      <Stack.Screen
        name="details"
        options={{
          title: "Detaljer",
        }}
      />
      <Stack.Screen
        name="profile"
        options={{
          title: "Profil",
        }}
      />
    </Stack>
  );
}
```

#### `app/index.js` - Hemskärm

```jsx
import { router } from "expo-router";
import React from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function HomeScreen() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Hemskärm</Text>
      <Text style={styles.description}>
        Välkommen till vår app! Navigera till olika skärmar.
      </Text>

      <View style={styles.buttonContainer}>
        <Button
          title="Gå till Detaljer"
          onPress={() => router.push("/details")}
        />
      </View>

      <View style={styles.buttonContainer}>
        <Button
          title="Gå till Profil"
          onPress={() =>
            router.push({
              pathname: "/profile",
              params: {
                userName: "Anna Andersson",
                userAge: 28,
              },
            })}
          color="#FF6B6B"
        />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
    backgroundColor: "#f5f5f5",
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#333",
    marginBottom: 10,
  },
  description: {
    fontSize: 16,
    color: "#666",
    textAlign: "center",
    marginBottom: 30,
    lineHeight: 24,
  },
  buttonContainer: {
    marginVertical: 10,
    width: "80%",
  },
});
```

#### `app/details.js` - Detaljskärm

```jsx
import { router } from "expo-router";
import React from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function DetailsScreen() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Detaljer</Text>
      <Text style={styles.description}>
        Detta är detaljskärmen. Här kan du visa mer information.
      </Text>

      <View style={styles.buttonContainer}>
        <Button
          title="Tillbaka"
          onPress={() => router.back()}
        />
      </View>

      <View style={styles.buttonContainer}>
        <Button
          title="Gå till Profil"
          onPress={() =>
            router.push({
              pathname: "/profile",
              params: {
                userName: "Från Detaljer",
                userAge: 25,
              },
            })}
          color="#4CAF50"
        />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
    backgroundColor: "#f5f5f5",
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#333",
    marginBottom: 10,
  },
  description: {
    fontSize: 16,
    color: "#666",
    textAlign: "center",
    marginBottom: 30,
    lineHeight: 24,
  },
  buttonContainer: {
    marginVertical: 10,
    width: "80%",
  },
});
```

#### `app/profile.js` - Profilskärm med parametrar

```jsx
import { router, useLocalSearchParams } from "expo-router";
import React from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function ProfileScreen() {
  // Få parametrar från URL:en
  const { userName, userAge } = useLocalSearchParams();

  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Profil</Text>
      <Text style={styles.description}>
        Användare: {userName}
      </Text>
      <Text style={styles.description}>
        Ålder: {userAge} år
      </Text>

      <View style={styles.buttonContainer}>
        <Button
          title="Uppdatera parametrar"
          onPress={() =>
            router.setParams({
              userName: "Uppdaterad Användare",
              userAge: String(Number(userAge) + 1),
            })}
          color="#9C27B0"
        />
      </View>

      <View style={styles.buttonContainer}>
        <Button
          title="Tillbaka till Hem"
          onPress={() => router.push("/")}
        />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
    backgroundColor: "#f5f5f5",
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#333",
    marginBottom: 10,
  },
  description: {
    fontSize: 16,
    color: "#666",
    textAlign: "center",
    marginBottom: 30,
    lineHeight: 24,
  },
  buttonContainer: {
    marginVertical: 10,
    width: "80%",
  },
});
```

### Bottom Tab Navigation

För tabs skapar vi följande struktur:

```
app/
  (tabs)/
    _layout.js
    index.js
    search.js
    favorites.js
    profile.js
```

#### `app/(tabs)/_layout.js` - Tab Layout

```jsx
import { Ionicons } from "@expo/vector-icons";
import { Tabs } from "expo-router";

export default function TabLayout() {
  return (
    <Tabs
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;

          if (route.name === "index") {
            iconName = focused ? "home" : "home-outline";
          } else if (route.name === "search") {
            iconName = focused ? "search" : "search-outline";
          } else if (route.name === "favorites") {
            iconName = focused ? "heart" : "heart-outline";
          } else if (route.name === "profile") {
            iconName = focused ? "person" : "person-outline";
          }

          return <Ionicons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: "#007AFF",
        tabBarInactiveTintColor: "gray",
        tabBarStyle: {
          backgroundColor: "#fff",
          borderTopWidth: 1,
          borderTopColor: "#e0e0e0",
        },
        headerStyle: {
          backgroundColor: "#007AFF",
        },
        headerTintColor: "#fff",
        headerTitleStyle: {
          fontWeight: "bold",
        },
      })}
    >
      <Tabs.Screen
        name="index"
        options={{
          title: "Hem",
        }}
      />
      <Tabs.Screen
        name="search"
        options={{
          title: "Sök",
        }}
      />
      <Tabs.Screen
        name="favorites"
        options={{
          title: "Favoriter",
          tabBarBadge: 3,
        }}
      />
      <Tabs.Screen
        name="profile"
        options={{
          title: "Profil",
        }}
      />
    </Tabs>
  );
}
```

#### `app/(tabs)/index.js` - Hem Tab

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

export default function HomeTab() {
  return (
    <View style={styles.tabScreen}>
      <Text style={styles.tabTitle}>🏠 Hem</Text>
      <Text style={styles.tabText}>
        Välkommen till hemskärmen!
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  tabScreen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#f5f5f5",
    padding: 20,
  },
  tabTitle: {
    fontSize: 32,
    fontWeight: "bold",
    marginBottom: 20,
  },
  tabText: {
    fontSize: 18,
    textAlign: "center",
    color: "#666",
    lineHeight: 26,
  },
});
```

#### `app/(tabs)/search.js` - Sök Tab

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

export default function SearchTab() {
  return (
    <View style={styles.tabScreen}>
      <Text style={styles.tabTitle}>🔍 Sök</Text>
      <Text style={styles.tabText}>
        Här kan du söka efter innehåll.
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  tabScreen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#f5f5f5",
    padding: 20,
  },
  tabTitle: {
    fontSize: 32,
    fontWeight: "bold",
    marginBottom: 20,
  },
  tabText: {
    fontSize: 18,
    textAlign: "center",
    color: "#666",
    lineHeight: 26,
  },
});
```

### Kombinerad Navigation (Stack + Tabs)

För att kombinera stack och tabs, skapa denna struktur:

```
app/
  (tabs)/
    _layout.js
    (home)/
      _layout.js
      index.js
      details.js
    (profile)/
      _layout.js
      index.js
      settings.js
```

#### `app/(tabs)/(home)/_layout.js` - Hem Stack

```jsx
import { Stack } from "expo-router";

export default function HomeLayout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          title: "Hem",
        }}
      />
      <Stack.Screen
        name="details"
        options={{
          title: "Detaljer",
        }}
      />
    </Stack>
  );
}
```

#### `app/(tabs)/(home)/index.js` - Hem Huvudskärm

```jsx
import { router } from "expo-router";
import React from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function HomeMainScreen() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Hem Huvudskärm</Text>
      <Button
        title="Visa detaljer"
        onPress={() => router.push("/(tabs)/(home)/details")}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: "bold",
    marginBottom: 20,
  },
});
```

#### `app/(tabs)/(home)/details.js` - Hem Detaljer

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

export default function HomeDetailsScreen() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Hem Detaljer</Text>
      <Text style={styles.text}>Detaljerad information här.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: "bold",
    marginBottom: 20,
  },
  text: {
    fontSize: 16,
    textAlign: "center",
  },
});
```

### Fördelar med Expo Router

1. **Fil-baserad routing** - Navigationstruktur matchar filmapp-strukturen
2. **Automatisk deep linking** - URL:er genereras automatiskt
3. **TypeScript-stöd** - Bättre typning av parametrar och routes
4. **Web-kompatibilitet** - Samma kod fungerar på web
5. **Enklare konfiguration** - Mindre boilerplate-kod än React Navigation

Expo Router är det moderna sättet att hantera navigation i Expo-appar och ger en mer intuitiv utvecklarupplevelse!

## Sammanfattning av Vecka 2

### Vad har du lärt dig?

**Designprinciper för mobil:**
✅ **Thumb-first design** - Placera viktiga element inom räckhåll för tummen
✅ **Touch targets** - Gör klickbara element tillräckligt stora (minst 44px)
✅ **Visual hierarchy** - Använd storlek, färg och placering för att guida användaren

**Flexbox och layout:**
✅ **Flexbox-system** - Förstå huvudaxel, tväraxel och hur element positioneras
✅ **Responsiv design** - Anpassa layout för olika skärmstorlekar
✅ **Orientationshantering** - Hantera när användaren roterar enheten

**Avancerad styling:**
✅ **Skuggor och effekter** - Skapa djup med shadows och elevation
✅ **Animationer** - Använd Animated API för smooth övergångar
✅ **Plattformsspecifik styling** - Anpassa utseende för iOS vs Android

**Navigation:**
✅ **Stack Navigation** - Grundläggande navigation mellan skärmar
✅ **Tab Navigation** - Bottom tabs för huvudsektioner
✅ **Kombinerad navigation** - Nästlade navigatorer för komplexa appar
✅ **Parametrar** - Skicka data mellan skärmar

### Nästa steg

I **Vecka 3** kommer vi att fördjupa oss i plattformar och interaktion:

- Djupare förståelse av Android vs iOS-skillnader
- Plattformsspecifik kod och funktionalitet
- Interaktion med andra applikationer
- Filhantering och delning
- Enhetsspecifika funktioner (kamera, GPS, etc.)

### Praktiska övningsförslag

**1. Responsiv profil-app:**

- Skapa en användarprofilsapp som anpassar sig till olika skärmstorlekar
- Implementera orientationshantering
- Använd Flexbox för layout

**2. Animerad todo-lista:**

- Bygg en todo-app med animationer när objekt läggs till/tas bort
- Använd Animated API för smooth övergångar
- Lägg till swipe-to-delete funktionalitet

**3. Multi-screen navigation:**

- Skapa en app med både Stack och Tab navigation
- Implementera sökning och filtrering
- Skicka data mellan skärmar

**4. Designutmaning:**

- Återskapa en befintlig mobil-app's design
- Fokusera på att matcha typografi, spacing och färger
- Implementera mikrointeraktioner och animationer

### Viktiga takeaways

🎨 **Design kommer först** - Tänk på användarupplevelsen innan du börjar koda

📐 **Flexbox är nyckeln** - Master Flexbox så löser du de flesta layoutproblem

📱 **Tänk mobilt** - Anpassa för tummar, små skärmar och olika orientationer

🎭 **Animationer förbättrar upplevelsen** - Men använd dem sparsamt och meningsfullt

🧭 **Navigation strukturerar appen** - Välj rätt navigationstyp för ditt användningsfall

📏 **Responsivitet är viktigt** - Din app ska fungera på alla skärmstorlekar

---

_Nästa: [Vecka 3 - Plattformar och Interaktion](./vecka-3.md) →_
