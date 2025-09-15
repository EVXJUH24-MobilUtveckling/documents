# Vecka 3: Plattformar och Interaktion

## Lärandemål för veckan

Efter denna vecka ska du kunna:

- Förstå djupare skillnader mellan Android och iOS-plattformarna
- Skriva plattformsspecifik kod när det behövs
- Implementera interaktion med andra applikationer
- Hantera filhantering och delning mellan appar
- Använda enhetsspecifika funktioner som kamera, GPS och sensorer
- Förstå skillnader i användarinteraktionsmönster mellan plattformarna

## Djupare förståelse av Android vs iOS

### Analogi: Två olika kulturer i samma land

Tänk dig att Android och iOS är som två olika regioner i samma land. Båda talar "samma språk" (mobilappar), men de har olika:

- **Arkitektur** (hur städerna är byggda)
- **Kulturella normer** (hur människorna beter sig)
- **Riktlinjer och lagar** (vad som är tillåtet och förväntat)
- **Verktyg och resurser** (vad som finns tillgängligt)

## Arkitekturella skillnader

### Android-arkitektur

**Analogi**: Android är som en öppen stad där vem som helst kan bygga och anpassa

```jsx
// Android har fler alternativ och anpassningsmöjligheter
const AndroidFeatures = {
  // Tillbaka-knapp finns alltid
  backButton: true,

  // Flera hemskärmar och widgets
  homeScreenCustomization: true,

  // Fler sätt att dela data
  intents: ["ACTION_SEND", "ACTION_VIEW", "ACTION_EDIT"],

  // Fil-system mer tillgängligt
  fileSystemAccess: "more-open",

  // Fler enheter och storlekar
  deviceFragmentation: "high",

  // Notifications mer flexibla
  notificationStyles: ["basic", "expanded", "custom", "heads-up"],
};
```

**Viktiga Android-koncept:**

- **Activities**: Skärmar/vyer i appen
- **Intents**: Sätt att kommunicera mellan appar
- **Services**: Bakgrundsprocesser
- **Permissions**: Detaljerad kontroll över vad appar får göra

### iOS-arkitektur

**Analogi**: iOS är som en elegant stad med strikta byggregler för enhetlighet

```jsx
// iOS fokuserar på konsistens och enhetlighet
const iOSFeatures = {
  // Navigation baserat på gester
  backButton: false, // Använder swipe-gester

  // Kontrollerad hemskärm
  homeScreenCustomization: "limited",

  // Standardiserade sätt att dela
  shareSheet: "unified",

  // Sandbox för säkerhet
  fileSystemAccess: "sandboxed",

  // Färre enheter, mer konsistent
  deviceFragmentation: "low",

  // Enhetliga notifikationer
  notificationStyles: ["standard", "rich"],
};
```

**Viktiga iOS-koncept:**

- **View Controllers**: Hanterar skärmar och navigation
- **Delegates**: Kommunikationsmönster mellan objekt
- **App Lifecycle**: Strikt hantering av appens tillstånd
- **Sandbox**: Isolerat miljö för varje app

## Plattformsdetektering och plattformsspecifik kod

### Grundläggande plattformsdetektering

```jsx
import { Platform } from "react-native";

// Olika sätt att identifiera plattform
console.log("Plattform:", Platform.OS); // 'ios' eller 'android'
console.log("Version:", Platform.Version);

// iOS: returnerar iOS version som string "14.0"
// Android: returnerar API level som number 29

// Detaljerad plattformsinformation
if (Platform.OS === "ios") {
  console.log("iOS Version:", Platform.Version);
  console.log("iPhone Model:", Platform.constants.systemName);
} else if (Platform.OS === "android") {
  console.log("Android API Level:", Platform.Version);
  console.log("Brand:", Platform.constants.Brand);
  console.log("Model:", Platform.constants.Model);
}
```

### Platform.select() för konditionell kod

```jsx
import { Platform, StyleSheet } from "react-native";

// Använd Platform.select för objekt-baserad plattformsval
const platformSpecificValue = Platform.select({
  ios: "iOS-värde",
  android: "Android-värde",
  default: "Fallback-värde", // För andra plattformar
});

// Praktiskt exempel med styling
const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: "#f2f2f2", // iOS ljusgrå
        paddingTop: 20, // Kompensera för status bar
      },
      android: {
        backgroundColor: "#ffffff", // Android vit
        paddingTop: 0, // Android hanterar status bar annorlunda
      },
    }),
  },

  // Texthantering skiljer sig mellan plattformarna
  text: {
    fontSize: 16,
    ...Platform.select({
      ios: {
        fontFamily: "Helvetica Neue",
        letterSpacing: 0.5,
      },
      android: {
        fontFamily: "Roboto",
        includeFontPadding: false, // Android-specifik
      },
    }),
  },

  // Skuggor implementeras olika
  card: {
    backgroundColor: "#fff",
    borderRadius: Platform.select({ ios: 8, android: 4 }),
    margin: 10,
    padding: 15,

    // iOS använder shadow-properties
    ...Platform.select({
      ios: {
        shadowColor: "#000",
        shadowOffset: {
          width: 0,
          height: 2,
        },
        shadowOpacity: 0.25,
        shadowRadius: 3.84,
      },
      // Android använder elevation
      android: {
        elevation: 5,
      },
    }),
  },
});
```

### Plattformsspecifika komponenter

```jsx
import React from "react";
import { Alert, Platform, Text, TouchableOpacity, View } from "react-native";

// Komponenter som beter sig olika på olika plattformar
function PlatformAwareButton({ title, onPress }) {
  const showAlert = () => {
    if (Platform.OS === "ios") {
      // iOS Alert med fler alternativ
      Alert.alert(
        "iOS Alert",
        "Detta är en iOS-stil alert",
        [
          { text: "Avbryt", style: "cancel" },
          { text: "Ta bort", style: "destructive" },
          { text: "OK", style: "default" },
        ],
        { cancelable: true },
      );
    } else {
      // Android Alert (enklare)
      Alert.alert(
        "Android Alert",
        "Detta är en Android-stil alert",
        [
          { text: "AVBRYT", style: "cancel" },
          { text: "OK", style: "default" },
        ],
      );
    }

    onPress && onPress();
  };

  return (
    <TouchableOpacity
      style={[
        styles.button,
        Platform.OS === "ios" ? styles.iOSButton : styles.androidButton,
      ]}
      onPress={showAlert}
    >
      <Text
        style={[
          styles.buttonText,
          Platform.OS === "ios"
            ? styles.iOSButtonText
            : styles.androidButtonText,
        ]}
      >
        {title}
      </Text>
    </TouchableOpacity>
  );
}

const styles = StyleSheet.create({
  button: {
    paddingVertical: 12,
    paddingHorizontal: 24,
    borderRadius: 6,
    alignItems: "center",
    justifyContent: "center",
    margin: 10,
  },
  iOSButton: {
    backgroundColor: "#007AFF",
    borderRadius: 8,
  },
  androidButton: {
    backgroundColor: "#2196F3",
    borderRadius: 4,
    elevation: 2,
  },
  buttonText: {
    fontSize: 16,
    fontWeight: "600",
  },
  iOSButtonText: {
    color: "#ffffff",
    fontFamily: "System",
  },
  androidButtonText: {
    color: "#ffffff",
    fontFamily: "Roboto",
    textTransform: "uppercase",
  },
});
```

### Plattformsspecifika filer

För större skillnader kan du skapa separata filer:

```jsx
// utils.ios.js
export const getStatusBarHeight = () => {
  // iOS-specifik implementation
  return 44; // Typisk höjd för iOS status bar + nav bar
};

export const hapticFeedback = (type) => {
  // iOS haptic feedback
  const { HapticFeedback } = require("react-native");
  HapticFeedback.trigger(type);
};

// utils.android.js
export const getStatusBarHeight = () => {
  // Android-specifik implementation
  const { StatusBar } = require("react-native");
  return StatusBar.currentHeight || 0;
};

export const hapticFeedback = (type) => {
  // Android vibration
  const { Vibration } = require("react-native");
  Vibration.vibrate(100);
};

// I din huvudkod
import { getStatusBarHeight, hapticFeedback } from "./utils";
// React Native väljer automatiskt rätt fil baserat på plattform
```

## Interaktion med andra applikationer

### Analogi: Appar som grannar i ett kvarter

Tänk dig att appar är som grannar i ett kvarter. De kan:

- **Skicka meddelanden** till varandra (intents/URL schemes)
- **Dela resurser** (filer, data)
- **Samarbeta** för att lösa uppgifter (öppna karta, skicka email)
- **Respektera gränser** (permissions och säkerhet)

### Linking API - Öppna andra appar

```jsx
import { Alert, Linking, Platform } from "react-native";

// Grundläggande länkning
const AppInteractionExample = () => {
  // Öppna URL i webbläsare
  const openWebsite = async (url) => {
    try {
      const supported = await Linking.canOpenURL(url);
      if (supported) {
        await Linking.openURL(url);
      } else {
        Alert.alert("Fel", `Kan inte öppna URL: ${url}`);
      }
    } catch (error) {
      console.error("Fel vid öppning av URL:", error);
    }
  };

  // Öppna telefon-appen
  const makePhoneCall = async (phoneNumber) => {
    const url = `tel:${phoneNumber}`;
    try {
      const supported = await Linking.canOpenURL(url);
      if (supported) {
        await Linking.openURL(url);
      } else {
        Alert.alert("Fel", "Kan inte ringa från denna enhet");
      }
    } catch (error) {
      console.error("Fel vid telefonsamtal:", error);
    }
  };

  // Öppna SMS-appen
  const sendSMS = async (phoneNumber, message) => {
    const url = Platform.select({
      ios: `sms:${phoneNumber}&body=${encodeURIComponent(message)}`,
      android: `sms:${phoneNumber}?body=${encodeURIComponent(message)}`,
    });

    try {
      await Linking.openURL(url);
    } catch (error) {
      console.error("Fel vid SMS:", error);
    }
  };

  // Öppna email-appen
  const sendEmail = async (to, subject, body) => {
    const url = `mailto:${to}?subject=${encodeURIComponent(subject)}&body=${
      encodeURIComponent(body)
    }`;

    try {
      const supported = await Linking.canOpenURL(url);
      if (supported) {
        await Linking.openURL(url);
      } else {
        Alert.alert("Fel", "Ingen email-app tillgänglig");
      }
    } catch (error) {
      console.error("Fel vid email:", error);
    }
  };

  // Öppna kart-appen
  const openMaps = async (address) => {
    const encodedAddress = encodeURIComponent(address);
    const url = Platform.select({
      ios: `maps:0,0?q=${encodedAddress}`,
      android: `geo:0,0?q=${encodedAddress}`,
    });

    try {
      const supported = await Linking.canOpenURL(url);
      if (supported) {
        await Linking.openURL(url);
      } else {
        // Fallback till web-baserad karta
        const webUrl = `https://maps.google.com/?q=${encodedAddress}`;
        await Linking.openURL(webUrl);
      }
    } catch (error) {
      console.error("Fel vid öppning av karta:", error);
    }
  };

  // Öppna specifika appar med URL schemes
  const openSpecificApp = async () => {
    const appURLs = Platform.select({
      ios: {
        spotify: "spotify://",
        whatsapp: "whatsapp://",
        instagram: "instagram://",
        facebook: "fb://",
      },
      android: {
        spotify: "spotify:",
        whatsapp: "whatsapp:",
        instagram: "instagram:",
        facebook: "facebook:",
      },
    });

    try {
      // Prova Spotify först
      const spotifySupported = await Linking.canOpenURL(appURLs.spotify);
      if (spotifySupported) {
        await Linking.openURL(appURLs.spotify);
      } else {
        Alert.alert("Info", "Spotify är inte installerat");
      }
    } catch (error) {
      console.error("Fel vid öppning av app:", error);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>App Interaktioner</Text>

      <TouchableOpacity
        style={styles.button}
        onPress={() => openWebsite("https://reactnative.dev")}
      >
        <Text style={styles.buttonText}>Öppna React Native webbsida</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() => makePhoneCall("+46701234567")}
      >
        <Text style={styles.buttonText}>Ring telefonnummer</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() =>
          sendSMS("+46701234567", "Hej från min React Native app!")}
      >
        <Text style={styles.buttonText}>Skicka SMS</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() =>
          sendEmail(
            "test@example.com",
            "Hälsningar från min app",
            "Detta meddelande skickades från min React Native-applikation!",
          )}
      >
        <Text style={styles.buttonText}>Skicka Email</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() => openMaps("Stockholm, Sverige")}
      >
        <Text style={styles.buttonText}>Öppna karta till Stockholm</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={openSpecificApp}
      >
        <Text style={styles.buttonText}>Öppna Spotify</Text>
      </TouchableOpacity>
    </View>
  );
};
```

### Share API - Dela innehåll

```jsx
import { Alert, Share } from "react-native";

const ShareExample = () => {
  // Grundläggande delning
  const shareText = async () => {
    try {
      const result = await Share.share({
        message: "Kolla in denna coola React Native app!",
        title: "Min App", // iOS endast
        url: "https://myapp.com", // iOS endast
      });

      if (result.action === Share.sharedAction) {
        if (result.activityType) {
          console.log("Delad med:", result.activityType);
        } else {
          console.log("Innehåll delat");
        }
      } else if (result.action === Share.dismissedAction) {
        console.log("Delning avbruten");
      }
    } catch (error) {
      console.error("Fel vid delning:", error);
    }
  };

  // Dela med anpassade alternativ
  const shareWithOptions = async () => {
    try {
      const result = await Share.share(
        {
          message: "Här är lite innehåll att dela!",
          title: "Dela denna information",
          url: "https://example.com",
        },
        {
          dialogTitle: "Välj hur du vill dela", // Android endast
          subject: "Email ämne", // Email ämne
          tintColor: "#007AFF", // iOS endast
        },
      );
    } catch (error) {
      Alert.alert("Fel", "Kunde inte dela innehållet");
    }
  };

  // Dela dynamiskt innehåll
  const shareUserData = async (userData) => {
    const shareContent = {
      message: `Kolla in ${userData.name}s profil: ${userData.description}`,
      title: `${userData.name}s Profil`,
    };

    // Lägg till URL på iOS
    if (Platform.OS === "ios") {
      shareContent.url = `https://myapp.com/profile/${userData.id}`;
    }

    try {
      await Share.share(shareContent);
    } catch (error) {
      console.error("Fel vid delning av användardata:", error);
    }
  };

  return (
    <View style={styles.container}>
      <TouchableOpacity style={styles.button} onPress={shareText}>
        <Text style={styles.buttonText}>Dela text</Text>
      </TouchableOpacity>

      <TouchableOpacity style={styles.button} onPress={shareWithOptions}>
        <Text style={styles.buttonText}>Dela med alternativ</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() =>
          shareUserData({
            id: "123",
            name: "Anna Andersson",
            description: "React Native utvecklare",
          })}
      >
        <Text style={styles.buttonText}>Dela användardata</Text>
      </TouchableOpacity>
    </View>
  );
};
```

## Filhantering och dokumenthantering

### Dokumenter och filsystem med Expo

```jsx
import * as DocumentPicker from "expo-document-picker";
import * as FileSystem from "expo-file-system";
import * as Sharing from "expo-sharing";
import { useState } from "react";

const FileHandlingExample = () => {
  const [selectedFile, setSelectedFile] = useState(null);

  // Välj dokument från enheten
  const pickDocument = async () => {
    try {
      const result = await DocumentPicker.getDocumentAsync({
        type: "*/*", // Alla filtyper
        copyToCacheDirectory: true,
        multiple: false,
      });

      if (result.type === "success") {
        setSelectedFile(result);
        console.log("Vald fil:", result);

        // Filens information
        console.log("Filnamn:", result.name);
        console.log("Storlek:", result.size);
        console.log("MIME-typ:", result.mimeType);
        console.log("URI:", result.uri);
      }
    } catch (error) {
      console.error("Fel vid filval:", error);
      Alert.alert("Fel", "Kunde inte välja fil");
    }
  };

  // Läs filinnehåll (för textfiler)
  const readFileContent = async () => {
    if (!selectedFile) {
      Alert.alert("Fel", "Ingen fil vald");
      return;
    }

    try {
      const content = await FileSystem.readAsStringAsync(selectedFile.uri);
      console.log("Filinnehåll:", content);
      Alert.alert("Filinnehåll", content.substring(0, 200) + "...");
    } catch (error) {
      console.error("Fel vid läsning av fil:", error);
      Alert.alert("Fel", "Kunde inte läsa filen");
    }
  };

  // Spara fil till enheten
  const saveFile = async () => {
    try {
      const content = "Detta är innehåll skapat av min React Native app!";
      const filename = "min-fil.txt";
      const fileUri = FileSystem.documentDirectory + filename;

      await FileSystem.writeAsStringAsync(fileUri, content);

      Alert.alert(
        "Fil sparad",
        `Fil sparad som ${filename}`,
        [
          { text: "OK", style: "default" },
          { text: "Dela fil", onPress: () => shareFile(fileUri) },
        ],
      );
    } catch (error) {
      console.error("Fel vid sparande av fil:", error);
      Alert.alert("Fel", "Kunde inte spara filen");
    }
  };

  // Dela fil med andra appar
  const shareFile = async (fileUri) => {
    try {
      const isAvailable = await Sharing.isAvailableAsync();
      if (isAvailable) {
        await Sharing.shareAsync(fileUri, {
          mimeType: "text/plain",
          dialogTitle: "Dela textfil",
        });
      } else {
        Alert.alert("Fel", "Delning inte tillgänglig på denna enhet");
      }
    } catch (error) {
      console.error("Fel vid delning av fil:", error);
    }
  };

  // Hämta filinformation
  const getFileInfo = async () => {
    if (!selectedFile) return;

    try {
      const info = await FileSystem.getInfoAsync(selectedFile.uri);
      console.log("Filinfo:", info);

      const infoText = `
        Fil: ${selectedFile.name}
        Storlek: ${Math.round(info.size / 1024)} KB
        Modifierad: ${
        new Date(info.modificationTime * 1000).toLocaleDateString()
      }
        Är mapp: ${info.isDirectory ? "Ja" : "Nej"}
      `;

      Alert.alert("Filinformation", infoText);
    } catch (error) {
      console.error("Fel vid hämtning av filinfo:", error);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Filhantering</Text>

      <TouchableOpacity style={styles.button} onPress={pickDocument}>
        <Text style={styles.buttonText}>Välj dokument</Text>
      </TouchableOpacity>

      {selectedFile && (
        <View style={styles.fileInfo}>
          <Text style={styles.fileName}>Vald fil: {selectedFile.name}</Text>
          <Text style={styles.fileSize}>
            Storlek: {Math.round(selectedFile.size / 1024)} KB
          </Text>
        </View>
      )}

      <TouchableOpacity
        style={[styles.button, !selectedFile && styles.disabledButton]}
        onPress={readFileContent}
        disabled={!selectedFile}
      >
        <Text style={styles.buttonText}>Läs filinnehåll</Text>
      </TouchableOpacity>

      <TouchableOpacity style={styles.button} onPress={saveFile}>
        <Text style={styles.buttonText}>Skapa och spara fil</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={[styles.button, !selectedFile && styles.disabledButton]}
        onPress={getFileInfo}
        disabled={!selectedFile}
      >
        <Text style={styles.buttonText}>Visa filinformation</Text>
      </TouchableOpacity>
    </View>
  );
};
```

## Enhetsspecifika funktioner med Expo

### Kamera och bildhantering

```jsx
import * as Camera from "expo-camera";
import * as ImagePicker from "expo-image-picker";
import { useEffect, useState } from "react";
import { Alert, Image, Text, TouchableOpacity, View } from "react-native";

const CameraExample = () => {
  const [image, setImage] = useState(null);
  const [hasPermission, setHasPermission] = useState(null);

  // Be om kameratillstånd
  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestCameraPermissionsAsync();
      setHasPermission(status === "granted");
    })();
  }, []);

  // Ta foto med kameran
  const takePhoto = async () => {
    try {
      // Kontrollera tillstånd
      const { status } = await ImagePicker.requestCameraPermissionsAsync();
      if (status !== "granted") {
        Alert.alert(
          "Tillstånd krävs",
          "Kameratillstånd krävs för att ta foton",
        );
        return;
      }

      // Öppna kameran
      const result = await ImagePicker.launchCameraAsync({
        mediaTypes: ImagePicker.MediaTypeOptions.Images,
        allowsEditing: true,
        aspect: [4, 3],
        quality: 1,
      });

      if (!result.cancelled) {
        setImage(result.uri);
        console.log("Foto taget:", result);
      }
    } catch (error) {
      console.error("Fel vid fotografering:", error);
      Alert.alert("Fel", "Kunde inte ta foto");
    }
  };

  // Välj bild från galleriet
  const pickImage = async () => {
    try {
      const { status } = await ImagePicker
        .requestMediaLibraryPermissionsAsync();
      if (status !== "granted") {
        Alert.alert("Tillstånd krävs", "Åtkomst till fotobiblioteket krävs");
        return;
      }

      const result = await ImagePicker.launchImageLibraryAsync({
        mediaTypes: ImagePicker.MediaTypeOptions.Images,
        allowsEditing: true,
        aspect: [4, 3],
        quality: 1,
      });

      if (!result.cancelled) {
        setImage(result.uri);
      }
    } catch (error) {
      console.error("Fel vid bildval:", error);
      Alert.alert("Fel", "Kunde inte välja bild");
    }
  };

  if (hasPermission === null) {
    return <Text>Begär kameratillstånd...</Text>;
  }
  if (hasPermission === false) {
    return <Text>Ingen åtkomst till kamera</Text>;
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Kamera & Bildhantering</Text>

      <TouchableOpacity style={styles.button} onPress={takePhoto}>
        <Text style={styles.buttonText}>Ta foto</Text>
      </TouchableOpacity>

      <TouchableOpacity style={styles.button} onPress={pickImage}>
        <Text style={styles.buttonText}>Välj från galleri</Text>
      </TouchableOpacity>

      {image && (
        <View style={styles.imageContainer}>
          <Image source={{ uri: image }} style={styles.image} />
          <TouchableOpacity
            style={styles.shareButton}
            onPress={() => shareFile(image)}
          >
            <Text style={styles.buttonText}>Dela bild</Text>
          </TouchableOpacity>
        </View>
      )}
    </View>
  );
};
```

### GPS och platshantering

```jsx
import * as Location from "expo-location";
import { useEffect, useState } from "react";

const LocationExample = () => {
  const [location, setLocation] = useState(null);
  const [errorMsg, setErrorMsg] = useState(null);
  const [address, setAddress] = useState(null);

  useEffect(() => {
    getCurrentLocation();
  }, []);

  const getCurrentLocation = async () => {
    try {
      // Be om platstillstånd
      const { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== "granted") {
        setErrorMsg("Platstillstånd nekades");
        return;
      }

      // Hämta nuvarande position
      const location = await Location.getCurrentPositionAsync({
        accuracy: Location.Accuracy.High,
      });

      setLocation(location);
      console.log("Plats:", location);

      // Omvandla koordinater till adress (reverse geocoding)
      const addressResult = await Location.reverseGeocodeAsync({
        latitude: location.coords.latitude,
        longitude: location.coords.longitude,
      });

      if (addressResult.length > 0) {
        setAddress(addressResult[0]);
      }
    } catch (error) {
      console.error("Fel vid platsinhämtning:", error);
      setErrorMsg("Kunde inte hämta plats");
    }
  };

  // Sök efter adress (geocoding)
  const searchAddress = async (searchText) => {
    try {
      const result = await Location.geocodeAsync(searchText);
      if (result.length > 0) {
        const foundLocation = result[0];
        console.log("Hittad plats:", foundLocation);

        // Visa på karta eller navigera dit
        const mapUrl =
          `https://maps.google.com/?q=${foundLocation.latitude},${foundLocation.longitude}`;
        Linking.openURL(mapUrl);
      }
    } catch (error) {
      console.error("Fel vid adressökning:", error);
    }
  };

  // Övervaka platsförändringar
  const watchLocation = async () => {
    const { status } = await Location.requestForegroundPermissionsAsync();
    if (status !== "granted") return;

    const subscription = await Location.watchPositionAsync(
      {
        accuracy: Location.Accuracy.High,
        timeInterval: 10000, // Uppdatera var 10:e sekund
        distanceInterval: 100, // Eller när användaren rört sig 100 meter
      },
      (newLocation) => {
        console.log("Ny plats:", newLocation);
        setLocation(newLocation);
      },
    );

    // Kom ihåg att stoppa prenumerationen senare
    // subscription.remove();
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>GPS & Platshantering</Text>

      <TouchableOpacity style={styles.button} onPress={getCurrentLocation}>
        <Text style={styles.buttonText}>Hämta nuvarande plats</Text>
      </TouchableOpacity>

      {errorMsg && <Text style={styles.error}>{errorMsg}</Text>}

      {location && (
        <View style={styles.locationInfo}>
          <Text style={styles.locationText}>
            Latitud: {location.coords.latitude.toFixed(6)}
          </Text>
          <Text style={styles.locationText}>
            Longitud: {location.coords.longitude.toFixed(6)}
          </Text>
          <Text style={styles.locationText}>
            Noggrannhet: {Math.round(location.coords.accuracy)} meter
          </Text>

          {address && (
            <View style={styles.addressInfo}>
              <Text style={styles.addressText}>
                {address.street} {address.streetNumber}
              </Text>
              <Text style={styles.addressText}>
                {address.postalCode} {address.city}
              </Text>
              <Text style={styles.addressText}>
                {address.country}
              </Text>
            </View>
          )}
        </View>
      )}

      <TouchableOpacity
        style={styles.button}
        onPress={() => searchAddress("Stockholm Central")}
      >
        <Text style={styles.buttonText}>Sök Stockholm Central</Text>
      </TouchableOpacity>
    </View>
  );
};
```

### Sensorer och enhetsinteraktion

```jsx
import * as Haptics from "expo-haptics";
import { DeviceMotion } from "expo-sensors";
import { useEffect, useState } from "react";

const SensorExample = () => {
  const [motionData, setMotionData] = useState({});
  const [subscription, setSubscription] = useState(null);

  // Börja lyssna på rörelsesensor
  const startMotionDetection = () => {
    DeviceMotion.setUpdateInterval(100); // Uppdatera 10 gånger per sekund

    const subscription = DeviceMotion.addListener((motionData) => {
      setMotionData(motionData);

      // Detektera skakningar
      const { x, y, z } = motionData.acceleration;
      const totalAcceleration = Math.sqrt(x * x + y * y + z * z);

      if (totalAcceleration > 2.0) { // Tröskel för skakning
        console.log("Enhet skakad!");
        triggerHapticFeedback();
      }
    });

    setSubscription(subscription);
  };

  // Sluta lyssna på sensorer
  const stopMotionDetection = () => {
    subscription && subscription.remove();
    setSubscription(null);
  };

  // Haptisk feedback (vibration)
  const triggerHapticFeedback = () => {
    // Olika typer av haptisk feedback
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  };

  const testHapticTypes = () => {
    // Lätt vibration
    setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light), 0);

    // Medium vibration
    setTimeout(
      () => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium),
      300,
    );

    // Stark vibration
    setTimeout(
      () => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy),
      600,
    );

    // Notifieringsfeedback
    setTimeout(
      () => Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success),
      900,
    );
  };

  useEffect(() => {
    return () => {
      // Städa upp när komponenten unmountas
      subscription && subscription.remove();
    };
  }, [subscription]);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Sensorer & Haptisk Feedback</Text>

      <TouchableOpacity
        style={styles.button}
        onPress={subscription ? stopMotionDetection : startMotionDetection}
      >
        <Text style={styles.buttonText}>
          {subscription ? "Stoppa" : "Starta"} rörelsedetektering
        </Text>
      </TouchableOpacity>

      <TouchableOpacity style={styles.button} onPress={testHapticTypes}>
        <Text style={styles.buttonText}>Testa vibrationsmönster</Text>
      </TouchableOpacity>

      {motionData.acceleration && (
        <View style={styles.sensorData}>
          <Text style={styles.sensorTitle}>Acceleration:</Text>
          <Text>X: {motionData.acceleration.x?.toFixed(3)}</Text>
          <Text>Y: {motionData.acceleration.y?.toFixed(3)}</Text>
          <Text>Z: {motionData.acceleration.z?.toFixed(3)}</Text>
        </View>
      )}

      {motionData.gyroscope && (
        <View style={styles.sensorData}>
          <Text style={styles.sensorTitle}>Gyroskop:</Text>
          <Text>X: {motionData.gyroscope.x?.toFixed(3)}</Text>
          <Text>Y: {motionData.gyroscope.y?.toFixed(3)}</Text>
          <Text>Z: {motionData.gyroscope.z?.toFixed(3)}</Text>
        </View>
      )}
    </View>
  );
};
```

## Komplett exempel - Plattformsmedveten resekamera-app

```jsx
import AsyncStorage from "@react-native-async-storage/async-storage";
import * as ImagePicker from "expo-image-picker";
import * as Location from "expo-location";
import * as Sharing from "expo-sharing";
import React, { useEffect, useState } from "react";
import {
  Alert,
  FlatList,
  Image,
  Platform,
  StyleSheet,
  Text,
  TouchableOpacity,
  View,
} from "react-native";

const TravelCameraApp = () => {
  const [photos, setPhotos] = useState([]);
  const [currentLocation, setCurrentLocation] = useState(null);

  useEffect(() => {
    loadSavedPhotos();
    getCurrentLocation();
  }, []);

  // Ladda sparade foton från lokal lagring
  const loadSavedPhotos = async () => {
    try {
      const savedPhotos = await AsyncStorage.getItem("travel_photos");
      if (savedPhotos) {
        setPhotos(JSON.parse(savedPhotos));
      }
    } catch (error) {
      console.error("Fel vid laddning av foton:", error);
    }
  };

  // Spara foton till lokal lagring
  const savePhotos = async (newPhotos) => {
    try {
      await AsyncStorage.setItem("travel_photos", JSON.stringify(newPhotos));
    } catch (error) {
      console.error("Fel vid sparande av foton:", error);
    }
  };

  // Hämta nuvarande plats
  const getCurrentLocation = async () => {
    try {
      const { status } = await Location.requestForegroundPermissionsAsync();
      if (status === "granted") {
        const location = await Location.getCurrentPositionAsync({
          accuracy: Location.Accuracy.High,
        });

        // Reverse geocoding för att få adress
        const address = await Location.reverseGeocodeAsync({
          latitude: location.coords.latitude,
          longitude: location.coords.longitude,
        });

        setCurrentLocation({
          coords: location.coords,
          address: address[0] || null,
        });
      }
    } catch (error) {
      console.error("Fel vid platsinhämtning:", error);
    }
  };

  // Ta foto med platsinformation
  const takePhotoWithLocation = async () => {
    try {
      const { status } = await ImagePicker.requestCameraPermissionsAsync();
      if (status !== "granted") {
        Alert.alert(
          "Tillstånd krävs",
          "Kameratillstånd krävs för att ta foton",
        );
        return;
      }

      const result = await ImagePicker.launchCameraAsync({
        mediaTypes: ImagePicker.MediaTypeOptions.Images,
        allowsEditing: true,
        aspect: [4, 3],
        quality: 0.8,
      });

      if (!result.cancelled) {
        const newPhoto = {
          id: Date.now().toString(),
          uri: result.uri,
          timestamp: new Date().toISOString(),
          location: currentLocation,
          // Plattformsspecifik metadata
          platform: Platform.OS,
          deviceInfo: Platform.select({
            ios: {
              version: Platform.Version,
              model: "iPhone", // Kunde hämtas från DeviceInfo
            },
            android: {
              apiLevel: Platform.Version,
              brand: Platform.constants.Brand,
              model: Platform.constants.Model,
            },
          }),
        };

        const updatedPhotos = [newPhoto, ...photos];
        setPhotos(updatedPhotos);
        savePhotos(updatedPhotos);

        // Plattformsspecifik feedback
        if (Platform.OS === "ios") {
          // iOS haptisk feedback
          const { Haptics } = require("expo-haptics");
          Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        }
      }
    } catch (error) {
      console.error("Fel vid fotografering:", error);
      Alert.alert("Fel", "Kunde inte ta foto");
    }
  };

  // Dela foto med platsinformation
  const sharePhoto = async (photo) => {
    try {
      let message = `Taget ${
        new Date(photo.timestamp).toLocaleDateString("sv-SE")
      }`;

      if (photo.location && photo.location.address) {
        const addr = photo.location.address;
        message += `\nPlats: ${addr.city || ""}, ${addr.country || ""}`;
      }

      // Plattformsspecifik delning
      if (Platform.OS === "ios") {
        await Sharing.shareAsync(photo.uri, {
          mimeType: "image/jpeg",
          dialogTitle: "Dela resefoto",
          UTI: "public.jpeg",
        });
      } else {
        await Sharing.shareAsync(photo.uri, {
          mimeType: "image/jpeg",
          dialogTitle: "Dela resefoto",
        });
      }
    } catch (error) {
      console.error("Fel vid delning:", error);
      Alert.alert("Fel", "Kunde inte dela foto");
    }
  };

  // Ta bort foto
  const deletePhoto = (photoId) => {
    Alert.alert(
      "Ta bort foto",
      "Är du säker på att du vill ta bort detta foto?",
      [
        { text: "Avbryt", style: "cancel" },
        {
          text: Platform.OS === "ios" ? "Ta bort" : "TA BORT",
          style: "destructive",
          onPress: () => {
            const updatedPhotos = photos.filter(photo => photo.id !== photoId);
            setPhotos(updatedPhotos);
            savePhotos(updatedPhotos);
          },
        },
      ],
    );
  };

  // Rendera fotoobjekt
  const renderPhoto = ({ item: photo }) => {
    const locationText = photo.location?.address
      ? `${photo.location.address.city || "Okänd stad"}, ${
        photo.location.address.country || "Okänt land"
      }`
      : "Plats okänd";

    return (
      <View style={styles.photoCard}>
        <Image source={{ uri: photo.uri }} style={styles.photoImage} />

        <View style={styles.photoInfo}>
          <Text style={styles.photoDate}>
            {new Date(photo.timestamp).toLocaleDateString("sv-SE")}
            {new Date(photo.timestamp).toLocaleTimeString("sv-SE", {
              hour: "2-digit",
              minute: "2-digit",
            })}
          </Text>

          <Text style={styles.photoLocation}>{locationText}</Text>

          <Text style={styles.photoPlatform}>
            Taget på {photo.platform === "ios" ? "iPhone" : "Android"}
          </Text>
        </View>

        <View style={styles.photoActions}>
          <TouchableOpacity
            style={[styles.actionButton, styles.shareButton]}
            onPress={() => sharePhoto(photo)}
          >
            <Text style={styles.actionButtonText}>Dela</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.actionButton, styles.deleteButton]}
            onPress={() => deletePhoto(photo.id)}
          >
            <Text style={styles.actionButtonText}>Ta bort</Text>
          </TouchableOpacity>
        </View>
      </View>
    );
  };

  return (
    <View style={styles.container}>
      {/* Header med plattformsspecifik styling */}
      <View
        style={[
          styles.header,
          Platform.OS === "ios" ? styles.iOSHeader : styles.androidHeader,
        ]}
      >
        <Text style={styles.headerTitle}>Resekamera</Text>
        {currentLocation && (
          <Text style={styles.headerSubtitle}>
            📍 {currentLocation.address?.city || "Nuvarande plats"}
          </Text>
        )}
      </View>

      {/* Kameraknapp */}
      <TouchableOpacity
        style={[
          styles.cameraButton,
          Platform.OS === "ios"
            ? styles.iOSCameraButton
            : styles.androidCameraButton,
        ]}
        onPress={takePhotoWithLocation}
      >
        <Text style={styles.cameraButtonText}>📷 Ta foto med plats</Text>
      </TouchableOpacity>

      {/* Fotogalleri */}
      <FlatList
        data={photos}
        renderItem={renderPhoto}
        keyExtractor={(item) => item.id}
        contentContainerStyle={styles.photoList}
        ListEmptyComponent={
          <View style={styles.emptyState}>
            <Text style={styles.emptyText}>Inga foton ännu</Text>
            <Text style={styles.emptySubtext}>Ta ditt första resefoto!</Text>
          </View>
        }
      />
    </View>
  );
};

// Plattformsspecifika stilar
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#f5f5f5",
  },
  header: {
    padding: 20,
    paddingTop: Platform.OS === "ios" ? 50 : 20,
    alignItems: "center",
  },
  iOSHeader: {
    backgroundColor: "#007AFF",
  },
  androidHeader: {
    backgroundColor: "#2196F3",
    elevation: 4,
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: "bold",
    color: "#fff",
    ...Platform.select({
      ios: {
        fontFamily: "System",
      },
      android: {
        fontFamily: "Roboto",
      },
    }),
  },
  headerSubtitle: {
    fontSize: 14,
    color: "#fff",
    marginTop: 5,
    opacity: 0.9,
  },
  cameraButton: {
    margin: 20,
    padding: 15,
    alignItems: "center",
    justifyContent: "center",
  },
  iOSCameraButton: {
    backgroundColor: "#34C759",
    borderRadius: 10,
  },
  androidCameraButton: {
    backgroundColor: "#4CAF50",
    borderRadius: 6,
    elevation: 3,
  },
  cameraButtonText: {
    color: "#fff",
    fontSize: 18,
    fontWeight: "600",
  },
  photoList: {
    padding: 15,
  },
  photoCard: {
    backgroundColor: "#fff",
    borderRadius: Platform.OS === "ios" ? 12 : 8,
    marginBottom: 15,
    overflow: "hidden",
    ...Platform.select({
      ios: {
        shadowColor: "#000",
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.1,
        shadowRadius: 4,
      },
      android: {
        elevation: 3,
      },
    }),
  },
  photoImage: {
    width: "100%",
    height: 200,
  },
  photoInfo: {
    padding: 15,
  },
  photoDate: {
    fontSize: 16,
    fontWeight: "600",
    color: "#333",
    marginBottom: 5,
  },
  photoLocation: {
    fontSize: 14,
    color: "#666",
    marginBottom: 5,
  },
  photoPlatform: {
    fontSize: 12,
    color: "#999",
  },
  photoActions: {
    flexDirection: "row",
    padding: 15,
    paddingTop: 0,
  },
  actionButton: {
    flex: 1,
    padding: 10,
    alignItems: "center",
    marginHorizontal: 5,
    borderRadius: Platform.OS === "ios" ? 8 : 4,
  },
  shareButton: {
    backgroundColor: "#007AFF",
  },
  deleteButton: {
    backgroundColor: "#FF3B30",
  },
  actionButtonText: {
    color: "#fff",
    fontWeight: "600",
    fontSize: Platform.OS === "android" ? 12 : 14,
    textTransform: Platform.OS === "android" ? "uppercase" : "none",
  },
  emptyState: {
    padding: 40,
    alignItems: "center",
  },
  emptyText: {
    fontSize: 18,
    color: "#999",
    marginBottom: 10,
  },
  emptySubtext: {
    fontSize: 14,
    color: "#ccc",
  },
});

export default TravelCameraApp;
```

## Sammanfattning av Vecka 3

### Vad har du lärt dig?

**Plattformsfördjupning:**
✅ **Arkitekturella skillnader** - Android är mer öppen och anpassningsbar, iOS mer kontrollerad och konsistent
✅ **Plattformsdetektering** - Använd Platform.OS, Platform.select() och plattformsspecifika filer
✅ **Design skillnader** - Material Design vs Human Interface Guidelines
✅ **Tekniska skillnader** - Navigation, filsystem, notifikationer

**App-interaktion:**
✅ **Linking API** - Öppna andra appar, webbsidor, telefonsamtal, email
✅ **Share API** - Dela innehåll mellan appar på ett native sätt
✅ **URL Schemes** - Djuplänkar och app-specifika protokoll

**Filhantering:**
✅ **DocumentPicker** - Låt användare välja filer från enheten
✅ **FileSystem** - Läsa, skriva och hantera filer
✅ **Sharing** - Dela filer med andra appar

**Enhetsintegration:**
✅ **Kamera och ImagePicker** - Ta foton och välj bilder
✅ **GPS och Location** - Hämta position, geocoding, platsövervakning
✅ **Sensorer** - Rörelsedetektering, haptisk feedback
✅ **Permissions** - Hantera användarrättigheter på ett smidigt sätt

### Nästa steg

I **Vecka 4** kommer vi att fokusera på avancerade funktioner:

- API-anrop och nätverkskommunikation
- Säkerhet och autentisering
- Datalagring och persistens
- Prestandaoptimering
- Tillgänglighet (Accessibility)

### Praktiska övningsförslag

**1. Plattformsspecifik app:**

- Skapa samma app för både iOS och Android med olika design
- Använd Platform.select() för olika beteenden
- Implementera plattformsspecifik navigation

**2. Delnings-app:**

- Bygg en app som kan dela olika typer av innehåll
- Implementera delning av text, bilder och filer
- Lägg till möjlighet att öppna andra appar

**3. Lokaliseringsapp:**

- Skapa en app som spårar användarens rörelser
- Visa platshistorik på en karta
- Implementera geofencing (notifikationer vid specifika platser)

**4. Kamera-dagbok:**

- Bygg en dagbok som kombinerar foton med text
- Lägg till automatisk platstagging
- Implementera delningsfunktionalitet

### Viktiga takeaways

🏗️ **Plattformsskillnader är viktiga** - Förstå kulturen och förväntningarna för varje plattform

🔗 **Appar är inte isolerade** - Modern mobilutveckling handlar om integration och samarbete

📂 **Filhantering öppnar möjligheter** - Låt användare arbeta med sitt eget innehåll

📱 **Enhetsfunktioner skapar värde** - Kamera, GPS och sensorer gör appar mer användbara

🔐 **Permissions kräver respekt** - Be bara om de rättigheter du verkligen behöver

🎯 **Användarupplevelse först** - Tekniska möjligheter ska tjäna användarens behov

---

_Nästa: [Vecka 4 - Avancerade funktioner](./vecka-4.md) →_
