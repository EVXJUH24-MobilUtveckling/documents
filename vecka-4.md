# Vecka 4: Avancerade funktioner

## Lärandemål för veckan

Efter denna vecka ska du kunna:

- Utföra säkra API-anrop och hantera nätverkskommunikation
- Implementera autentisering och säkerhetsmekanismer
- Hantera datalagring och persistens på ett effektivt sätt
- Optimera prestanda i React Native-applikationer
- Implementera tillgänglighet (accessibility) för alla användare
- Hantera felhantering och användarfeedback på ett professionellt sätt

## API-anrop och nätverkskommunikation

### Analogi: API som en restaurang

Tänk dig att ett API är som en restaurang:

- **Menyn** (API-dokumentation) visar vad som finns tillgängligt
- **Beställningen** (API-anrop) specificerar vad du vill ha
- **Kocken** (servern) förbereder din beställning
- **Servitören** (HTTP-protokollet) levererar resultatet
- **Notan** (API-kostnader/rate limits) visar vad det kostar

### Grundläggande API-anrop med fetch()

```jsx
import React, { useEffect, useState } from "react";
import {
  ActivityIndicator,
  Alert,
  FlatList,
  RefreshControl,
  StyleSheet,
  Text,
  View,
} from "react-native";

const APIExample = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [refreshing, setRefreshing] = useState(false);

  // Grundläggande API-anrop
  const fetchData = async () => {
    setLoading(true);
    setError(null);

    try {
      // Enkel GET-förfrågan
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts",
      );

      // Kontrollera om förfrågan lyckades
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      // Parsa JSON-data
      const result = await response.json();
      setData(result.slice(0, 10)); // Begränsa till 10 poster
    } catch (error) {
      console.error("API Error:", error);
      setError(error.message);

      // Visa användarvänligt felmeddelande
      Alert.alert(
        "Nätverksfel",
        "Kunde inte hämta data. Kontrollera din internetanslutning och försök igen.",
        [{ text: "OK" }],
      );
    } finally {
      setLoading(false);
    }
  };

  // Avancerat API-anrop med headers och konfiguration
  const fetchWithOptions = async () => {
    setLoading(true);

    try {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts",
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
            "Accept": "application/json",
            "User-Agent": "MyReactNativeApp/1.0",
            // 'Authorization': 'Bearer YOUR_TOKEN_HERE', // Om autentisering krävs
          },
          // Timeout-hantering (inte direkt stödd av fetch, men kan implementeras)
          signal: AbortSignal.timeout(10000), // 10 sekunder timeout (modern browsers)
        },
      );

      if (!response.ok) {
        // Hantera olika HTTP-statuskoder
        switch (response.status) {
          case 401:
            throw new Error(
              "Obehörig åtkomst - kontrollera dina inloggningsuppgifter",
            );
          case 403:
            throw new Error("Åtkomst nekad");
          case 404:
            throw new Error("Data inte hittad");
          case 500:
            throw new Error("Serverfel - försök igen senare");
          default:
            throw new Error(`HTTP error! status: ${response.status}`);
        }
      }

      const result = await response.json();
      setData(result);
    } catch (error) {
      if (error.name === "AbortError") {
        setError("Förfrågan tog för lång tid");
      } else {
        setError(error.message);
      }
    } finally {
      setLoading(false);
    }
  };

  // POST-anrop för att skicka data
  const createPost = async (postData) => {
    try {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            title: postData.title,
            body: postData.body,
            userId: postData.userId,
          }),
        },
      );

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();
      console.log("Post skapad:", result);

      // Uppdatera lokal data
      setData(prevData => [result, ...prevData]);

      Alert.alert("Framgång", "Post skapad!");
    } catch (error) {
      console.error("Error creating post:", error);
      Alert.alert("Fel", "Kunde inte skapa post");
    }
  };

  // PUT-anrop för att uppdatera data
  const updatePost = async (postId, updatedData) => {
    try {
      const response = await fetch(
        `https://jsonplaceholder.typicode.com/posts/${postId}`,
        {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(updatedData),
        },
      );

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();

      // Uppdatera lokal data
      setData(prevData =>
        prevData.map(item => item.id === postId ? result : item)
      );
    } catch (error) {
      console.error("Error updating post:", error);
    }
  };

  // DELETE-anrop
  const deletePost = async (postId) => {
    try {
      const response = await fetch(
        `https://jsonplaceholder.typicode.com/posts/${postId}`,
        {
          method: "DELETE",
        },
      );

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      // Ta bort från lokal data
      setData(prevData => prevData.filter(item => item.id !== postId));

      Alert.alert("Framgång", "Post borttagen!");
    } catch (error) {
      console.error("Error deleting post:", error);
      Alert.alert("Fel", "Kunde inte ta bort post");
    }
  };

  // Pull-to-refresh funktionalitet
  const onRefresh = async () => {
    setRefreshing(true);
    await fetchData();
    setRefreshing(false);
  };

  // Hämta data när komponenten laddas
  useEffect(() => {
    fetchData();
  }, []);

  // Rendera posts
  const renderPost = ({ item }) => (
    <View style={styles.postCard}>
      <Text style={styles.postTitle}>{item.title}</Text>
      <Text style={styles.postBody}>{item.body}</Text>
      <View style={styles.postActions}>
        <TouchableOpacity
          onPress={() =>
            updatePost(item.id, {
              ...item,
              title: item.title + " (Uppdaterad)",
            })}
          style={styles.updateButton}
        >
          <Text style={styles.buttonText}>Uppdatera</Text>
        </TouchableOpacity>
        <TouchableOpacity
          onPress={() => deletePost(item.id)}
          style={styles.deleteButton}
        >
          <Text style={styles.buttonText}>Ta bort</Text>
        </TouchableOpacity>
      </View>
    </View>
  );

  // Visa laddningsindikator
  if (loading && !refreshing) {
    return (
      <View style={styles.centerContainer}>
        <ActivityIndicator size="large" color="#007AFF" />
        <Text style={styles.loadingText}>Hämtar data...</Text>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <TouchableOpacity
        style={styles.createButton}
        onPress={() =>
          createPost({
            title: "Ny post från React Native",
            body: "Detta är en post skapad från min app!",
            userId: 1,
          })}
      >
        <Text style={styles.buttonText}>Skapa ny post</Text>
      </TouchableOpacity>

      <FlatList
        data={data}
        renderItem={renderPost}
        keyExtractor={(item) => item.id.toString()}
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
        }
        ListEmptyComponent={
          <View style={styles.centerContainer}>
            <Text style={styles.emptyText}>
              {error ? `Fel: ${error}` : "Ingen data tillgänglig"}
            </Text>
          </View>
        }
      />
    </View>
  );
};
```

### Avancerad API-hantering med Axios

```jsx
// Installera först: npm install axios
import axios from "axios";
import { useState } from "react";

// Konfigurera Axios-instans
const apiClient = axios.create({
  baseURL: "https://jsonplaceholder.typicode.com",
  timeout: 10000, // 10 sekunder timeout
  headers: {
    "Content-Type": "application/json",
    "Accept": "application/json",
  },
});

// Lägg till request interceptor för att hantera autentisering
apiClient.interceptors.request.use(
  (config) => {
    // Lägg till auth token om det finns
    const token = getAuthToken(); // Din funktion för att hämta token
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    console.log("API Request:", config.method?.toUpperCase(), config.url);
    return config;
  },
  (error) => {
    console.error("Request Error:", error);
    return Promise.reject(error);
  },
);

// Lägg till response interceptor för felhantering
apiClient.interceptors.response.use(
  (response) => {
    console.log("API Response:", response.status, response.config.url);
    return response;
  },
  (error) => {
    console.error("API Error:", error.response?.status, error.message);

    // Hantera specifika felkoder
    if (error.response?.status === 401) {
      // Logout användaren eller förnya token
      handleUnauthorized();
    }

    return Promise.reject(error);
  },
);

const AxiosExample = () => {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(false);

  // GET med Axios
  const fetchPosts = async () => {
    setLoading(true);
    try {
      const response = await apiClient.get("/posts?_limit=10");
      setPosts(response.data);
    } catch (error) {
      handleApiError(error);
    } finally {
      setLoading(false);
    }
  };

  // POST med Axios
  const createPost = async (postData) => {
    try {
      const response = await apiClient.post("/posts", postData);
      setPosts(prev => [response.data, ...prev]);
      Alert.alert("Framgång", "Post skapad!");
    } catch (error) {
      handleApiError(error);
    }
  };

  // Centraliserad felhantering
  const handleApiError = (error) => {
    let message = "Ett oväntat fel uppstod";

    if (error.response) {
      // Servern svarade med felkod
      switch (error.response.status) {
        case 400:
          message = "Felaktig förfrågan";
          break;
        case 401:
          message = "Du måste logga in igen";
          break;
        case 403:
          message = "Du har inte behörighet för denna åtgärd";
          break;
        case 404:
          message = "Resursen hittades inte";
          break;
        case 500:
          message = "Serverfel - försök igen senare";
          break;
        default:
          message =
            `Fel ${error.response.status}: ${error.response.statusText}`;
      }
    } else if (error.request) {
      // Förfrågan skickades men inget svar mottogs
      message =
        "Ingen anslutning till servern. Kontrollera din internetanslutning.";
    }

    Alert.alert("Fel", message);
  };

  // Batch requests (flera API-anrop samtidigt)
  const fetchMultipleData = async () => {
    setLoading(true);
    try {
      const [postsResponse, usersResponse, commentsResponse] = await Promise
        .all([
          apiClient.get("/posts?_limit=5"),
          apiClient.get("/users?_limit=5"),
          apiClient.get("/comments?_limit=10"),
        ]);

      console.log("Posts:", postsResponse.data);
      console.log("Users:", usersResponse.data);
      console.log("Comments:", commentsResponse.data);
    } catch (error) {
      handleApiError(error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View style={styles.container}>
      {/* Din UI här */}
    </View>
  );
};
```

## Säkerhet och autentisering

### Analogi: Säkerhet som husbevakning

Mobilappsäkerhet är som att bevaka ett hus:

- **Lås på dörrarna** (Autentisering) - Vem får komma in?
- **Larm och kameror** (Övervakning) - Vad händer?
- **Säkerhetsbox** (Säker lagring) - Var förvaras värdesaker?
- **Besöksregister** (Logging) - Vem var här och när?

### Säker autentisering

```jsx
import AsyncStorage from "@react-native-async-storage/async-storage";
import * as Crypto from "expo-crypto";
import * as SecureStore from "expo-secure-store";

class AuthService {
  constructor() {
    this.baseURL = "https://your-api.com";
  }

  // Säker inloggning
  async login(email, password) {
    try {
      // Hash lösenordet innan det skickas (extra säkerhet)
      const hashedPassword = await Crypto.digestStringAsync(
        Crypto.CryptoDigestAlgorithm.SHA256,
        password + "your-salt-here", // Lägg till salt för extra säkerhet
      );

      const response = await fetch(`${this.baseURL}/auth/login`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          email,
          password: hashedPassword, // Skicka aldrig klartext-lösenord
        }),
      });

      if (!response.ok) {
        throw new Error("Inloggning misslyckades");
      }

      const data = await response.json();

      // Spara tokens säkert
      await this.storeTokens(data.accessToken, data.refreshToken);

      // Spara användarinfo (icke-känslig data)
      await AsyncStorage.setItem("user", JSON.stringify(data.user));

      return data;
    } catch (error) {
      console.error("Login error:", error);
      throw error;
    }
  }

  // Säker lagring av tokens
  async storeTokens(accessToken, refreshToken) {
    try {
      // Använd SecureStore för känslig data
      await SecureStore.setItemAsync("accessToken", accessToken);
      await SecureStore.setItemAsync("refreshToken", refreshToken);

      // Spara timestamp för när token skapades
      await AsyncStorage.setItem("tokenCreatedAt", Date.now().toString());
    } catch (error) {
      console.error("Error storing tokens:", error);
      throw new Error("Kunde inte spara inloggningsinformation");
    }
  }

  // Hämta säkert lagrad token
  async getAccessToken() {
    try {
      const token = await SecureStore.getItemAsync("accessToken");

      // Kontrollera om token fortfarande är giltig
      if (token && await this.isTokenValid()) {
        return token;
      }

      // Försök förnya token om den är utgången
      return await this.refreshAccessToken();
    } catch (error) {
      console.error("Error getting access token:", error);
      return null;
    }
  }

  // Kontrollera token-validitet
  async isTokenValid() {
    try {
      const tokenCreatedAt = await AsyncStorage.getItem("tokenCreatedAt");
      if (!tokenCreatedAt) return false;

      const createdTime = parseInt(tokenCreatedAt);
      const currentTime = Date.now();
      const tokenAge = currentTime - createdTime;

      // Token är giltig i 1 timme (3600000 ms)
      return tokenAge < 3600000;
    } catch (error) {
      return false;
    }
  }

  // Förnya access token med refresh token
  async refreshAccessToken() {
    try {
      const refreshToken = await SecureStore.getItemAsync("refreshToken");
      if (!refreshToken) {
        throw new Error("Ingen refresh token");
      }

      const response = await fetch(`${this.baseURL}/auth/refresh`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "Authorization": `Bearer ${refreshToken}`,
        },
      });

      if (!response.ok) {
        // Refresh token är ogiltig, användaren måste logga in igen
        await this.logout();
        throw new Error("Session utgången");
      }

      const data = await response.json();
      await this.storeTokens(data.accessToken, data.refreshToken);

      return data.accessToken;
    } catch (error) {
      console.error("Token refresh failed:", error);
      await this.logout();
      throw error;
    }
  }

  // Säker utloggning
  async logout() {
    try {
      // Ta bort all sparad data
      await SecureStore.deleteItemAsync("accessToken");
      await SecureStore.deleteItemAsync("refreshToken");
      await AsyncStorage.removeItem("user");
      await AsyncStorage.removeItem("tokenCreatedAt");

      // Informera servern om utloggning (om API stödjer det)
      const token = await SecureStore.getItemAsync("accessToken");
      if (token) {
        fetch(`${this.baseURL}/auth/logout`, {
          method: "POST",
          headers: {
            "Authorization": `Bearer ${token}`,
          },
        }).catch(console.error); // Ignorera fel här
      }
    } catch (error) {
      console.error("Logout error:", error);
    }
  }

  // Kontrollera om användaren är inloggad
  async isLoggedIn() {
    try {
      const token = await this.getAccessToken();
      return token !== null;
    } catch (error) {
      return false;
    }
  }
}

// Användning av AuthService
const authService = new AuthService();

const LoginScreen = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loading, setLoading] = useState(false);

  const handleLogin = async () => {
    if (!email || !password) {
      Alert.alert("Fel", "Alla fält måste fyllas i");
      return;
    }

    // Grundläggande email-validering
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(email)) {
      Alert.alert("Fel", "Ange en giltig email-adress");
      return;
    }

    setLoading(true);
    try {
      await authService.login(email, password);

      // Navigera till huvudskärmen
      navigation.replace("Home");
    } catch (error) {
      Alert.alert("Inloggning misslyckades", error.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Logga in</Text>

      <TextInput
        style={styles.input}
        placeholder="Email"
        value={email}
        onChangeText={setEmail}
        keyboardType="email-address"
        autoCapitalize="none"
        autoCorrect={false}
      />

      <TextInput
        style={styles.input}
        placeholder="Lösenord"
        value={password}
        onChangeText={setPassword}
        secureTextEntry={true}
        autoCapitalize="none"
        autoCorrect={false}
      />

      <TouchableOpacity
        style={styles.loginButton}
        onPress={handleLogin}
        disabled={loading}
      >
        {loading
          ? <ActivityIndicator color="#fff" />
          : <Text style={styles.buttonText}>Logga in</Text>}
      </TouchableOpacity>
    </View>
  );
};
```

### Säkra API-anrop med autentisering

```jsx
class SecureAPIClient {
  constructor(authService) {
    this.authService = authService;
    this.baseURL = "https://your-api.com/api";
  }

  // Säkert API-anrop med automatisk token-hantering
  async request(endpoint, options = {}) {
    try {
      // Hämta giltigt access token
      const token = await this.authService.getAccessToken();
      if (!token) {
        throw new Error("Ingen giltig autentisering");
      }

      // Sätt upp headers
      const headers = {
        "Content-Type": "application/json",
        "Authorization": `Bearer ${token}`,
        ...options.headers,
      };

      const response = await fetch(`${this.baseURL}${endpoint}`, {
        ...options,
        headers,
      });

      if (response.status === 401) {
        // Token är ogiltig, försök förnya
        const newToken = await this.authService.refreshAccessToken();

        // Gör om förfrågan med nytt token
        return await fetch(`${this.baseURL}${endpoint}`, {
          ...options,
          headers: {
            ...headers,
            "Authorization": `Bearer ${newToken}`,
          },
        });
      }

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      return await response.json();
    } catch (error) {
      console.error("Secure API request failed:", error);
      throw error;
    }
  }

  // Convenience methods
  async get(endpoint) {
    return await this.request(endpoint, { method: "GET" });
  }

  async post(endpoint, data) {
    return await this.request(endpoint, {
      method: "POST",
      body: JSON.stringify(data),
    });
  }

  async put(endpoint, data) {
    return await this.request(endpoint, {
      method: "PUT",
      body: JSON.stringify(data),
    });
  }

  async delete(endpoint) {
    return await this.request(endpoint, { method: "DELETE" });
  }
}

// Användning
const apiClient = new SecureAPIClient(authService);

const UserProfile = () => {
  const [profile, setProfile] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    loadProfile();
  }, []);

  const loadProfile = async () => {
    try {
      const profileData = await apiClient.get("/user/profile");
      setProfile(profileData);
    } catch (error) {
      Alert.alert("Fel", "Kunde inte hämta profil");
    } finally {
      setLoading(false);
    }
  };

  const updateProfile = async (updatedData) => {
    try {
      const result = await apiClient.put("/user/profile", updatedData);
      setProfile(result);
      Alert.alert("Framgång", "Profil uppdaterad");
    } catch (error) {
      Alert.alert("Fel", "Kunde inte uppdatera profil");
    }
  };

  // ... UI rendering
};
```

## Datalagring och persistens

### Analogi: Olika typer av lagring som förvaring hemma

- **AsyncStorage** = Vardagsrummet (lättåtkomligt, synligt)
- **SecureStore** = Säkerhetsskåp (säkert för värdesaker)
- **FileSystem** = Källare/vind (stora mängder, organiserat)
- **SQLite** = Bibliotek (strukturerat, sökbart)

### AsyncStorage för enkel datalagring

```jsx
import AsyncStorage from "@react-native-async-storage/async-storage";

class DataManager {
  // Spara enkel data
  async saveData(key, value) {
    try {
      const jsonValue = JSON.stringify(value);
      await AsyncStorage.setItem(key, jsonValue);
      console.log("Data sparad:", key);
    } catch (error) {
      console.error("Error saving data:", error);
      throw new Error("Kunde inte spara data");
    }
  }

  // Hämta data
  async getData(key) {
    try {
      const jsonValue = await AsyncStorage.getItem(key);
      return jsonValue ? JSON.parse(jsonValue) : null;
    } catch (error) {
      console.error("Error getting data:", error);
      return null;
    }
  }

  // Ta bort data
  async removeData(key) {
    try {
      await AsyncStorage.removeItem(key);
    } catch (error) {
      console.error("Error removing data:", error);
    }
  }

  // Hämta alla nycklar
  async getAllKeys() {
    try {
      return await AsyncStorage.getAllKeys();
    } catch (error) {
      console.error("Error getting keys:", error);
      return [];
    }
  }

  // Rensa all data
  async clearAll() {
    try {
      await AsyncStorage.clear();
      console.log("All data cleared");
    } catch (error) {
      console.error("Error clearing data:", error);
    }
  }

  // Batch-operationer
  async saveMultiple(dataArray) {
    try {
      const pairs = dataArray.map((
        [key, value],
      ) => [key, JSON.stringify(value)]);
      await AsyncStorage.multiSet(pairs);
    } catch (error) {
      console.error("Error saving multiple:", error);
    }
  }

  // Hämta flera värden samtidigt
  async getMultiple(keys) {
    try {
      const pairs = await AsyncStorage.multiGet(keys);
      const result = {};
      pairs.forEach(([key, value]) => {
        result[key] = value ? JSON.parse(value) : null;
      });
      return result;
    } catch (error) {
      console.error("Error getting multiple:", error);
      return {};
    }
  }
}

// Användningsexempel
const dataManager = new DataManager();

const SettingsScreen = () => {
  const [settings, setSettings] = useState({
    notifications: true,
    darkMode: false,
    language: "sv",
    fontSize: 16,
  });

  // Ladda sparade inställningar
  useEffect(() => {
    loadSettings();
  }, []);

  const loadSettings = async () => {
    try {
      const savedSettings = await dataManager.getData("app_settings");
      if (savedSettings) {
        setSettings(savedSettings);
      }
    } catch (error) {
      console.error("Error loading settings:", error);
    }
  };

  // Spara inställningar när de ändras
  const updateSetting = async (key, value) => {
    const newSettings = { ...settings, [key]: value };
    setSettings(newSettings);

    try {
      await dataManager.saveData("app_settings", newSettings);
    } catch (error) {
      Alert.alert("Fel", "Kunde inte spara inställningar");
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Inställningar</Text>

      <View style={styles.settingRow}>
        <Text style={styles.settingLabel}>Notifikationer</Text>
        <Switch
          value={settings.notifications}
          onValueChange={(value) => updateSetting("notifications", value)}
        />
      </View>

      <View style={styles.settingRow}>
        <Text style={styles.settingLabel}>Mörkt tema</Text>
        <Switch
          value={settings.darkMode}
          onValueChange={(value) => updateSetting("darkMode", value)}
        />
      </View>

      {/* Fler inställningar... */}
    </View>
  );
};
```

### Offline-support och cache-hantering

```jsx
import NetInfo from "@react-native-community/netinfo";

class OfflineManager {
  constructor(dataManager, apiClient) {
    this.dataManager = dataManager;
    this.apiClient = apiClient;
    this.isOnline = true;
    this.pendingRequests = [];

    // Lyssna på nätverksstatus
    this.setupNetworkListener();
  }

  setupNetworkListener() {
    NetInfo.addEventListener(state => {
      console.log("Network state:", state);
      this.isOnline = state.isConnected;

      if (this.isOnline) {
        this.processPendingRequests();
      }
    });
  }

  // Hämta data med offline-support
  async fetchWithCache(endpoint, cacheKey, maxAge = 3600000) { // 1 timme default
    try {
      // Försök hämta från cache först
      const cachedData = await this.dataManager.getData(cacheKey);
      const cacheTime = await this.dataManager.getData(`${cacheKey}_time`);

      const now = Date.now();
      const isCacheValid = cacheTime && (now - cacheTime < maxAge);

      // Om offline, returnera cache (om den finns)
      if (!this.isOnline) {
        if (cachedData) {
          console.log("Returning cached data (offline):", cacheKey);
          return { data: cachedData, fromCache: true };
        } else {
          throw new Error("Ingen internetanslutning och ingen cachad data");
        }
      }

      // Om cache är giltig, returnera den (men hämta ny data i bakgrunden)
      if (isCacheValid && cachedData) {
        console.log("Returning cached data:", cacheKey);

        // Hämta ny data i bakgrunden
        this.fetchAndCache(endpoint, cacheKey).catch(console.error);

        return { data: cachedData, fromCache: true };
      }

      // Hämta från API
      const response = await this.apiClient.get(endpoint);

      // Cacha resultatet
      await this.dataManager.saveData(cacheKey, response);
      await this.dataManager.saveData(`${cacheKey}_time`, now);

      return { data: response, fromCache: false };
    } catch (error) {
      console.error("Fetch with cache error:", error);

      // Fallback till cache även om den är gammal
      const cachedData = await this.dataManager.getData(cacheKey);
      if (cachedData) {
        console.log("Returning stale cached data:", cacheKey);
        return { data: cachedData, fromCache: true, stale: true };
      }

      throw error;
    }
  }

  // Hämta och cacha data
  async fetchAndCache(endpoint, cacheKey) {
    try {
      const response = await this.apiClient.get(endpoint);
      await this.dataManager.saveData(cacheKey, response);
      await this.dataManager.saveData(`${cacheKey}_time`, Date.now());
      return response;
    } catch (error) {
      console.error("Background fetch failed:", error);
    }
  }

  // Skicka data med offline-support
  async postWithQueue(endpoint, data, queueKey) {
    if (this.isOnline) {
      try {
        const response = await this.apiClient.post(endpoint, data);
        return response;
      } catch (error) {
        // Om förfrågan misslyckas, lägg till i kön
        await this.addToQueue(endpoint, "POST", data, queueKey);
        throw error;
      }
    } else {
      // Offline - lägg till i kön
      await this.addToQueue(endpoint, "POST", data, queueKey);
      throw new Error("Offline - data lagd i kön");
    }
  }

  // Lägg till i pending-kö
  async addToQueue(endpoint, method, data, queueKey) {
    const request = {
      id: Date.now(),
      endpoint,
      method,
      data,
      timestamp: Date.now(),
    };

    this.pendingRequests.push(request);

    // Spara kön permanent
    await this.dataManager.saveData("pending_requests", this.pendingRequests);

    console.log("Added to queue:", request);
  }

  // Bearbeta väntande förfrågningar
  async processPendingRequests() {
    console.log("Processing pending requests...");

    // Ladda sparade förfrågningar
    const savedRequests = await this.dataManager.getData("pending_requests");
    if (savedRequests) {
      this.pendingRequests = savedRequests;
    }

    const successfulRequests = [];

    for (const request of this.pendingRequests) {
      try {
        console.log("Processing request:", request.id);

        if (request.method === "POST") {
          await this.apiClient.post(request.endpoint, request.data);
        }
        // Lägg till fler metoder vid behov

        successfulRequests.push(request.id);
      } catch (error) {
        console.error("Failed to process request:", request.id, error);

        // Ta bort gamla förfrågningar (äldre än 24 timmar)
        if (Date.now() - request.timestamp > 86400000) {
          successfulRequests.push(request.id);
          console.log("Removing old request:", request.id);
        }
      }
    }

    // Ta bort processade förfrågningar
    this.pendingRequests = this.pendingRequests.filter(
      req => !successfulRequests.includes(req.id),
    );

    await this.dataManager.saveData("pending_requests", this.pendingRequests);

    console.log(
      "Pending requests processed. Remaining:",
      this.pendingRequests.length,
    );
  }

  // Rensa cache
  async clearCache(pattern = null) {
    try {
      const keys = await this.dataManager.getAllKeys();

      let keysToRemove = [];
      if (pattern) {
        keysToRemove = keys.filter(key => key.includes(pattern));
      } else {
        // Rensa bara cache-nycklar (inte andra app-data)
        keysToRemove = keys.filter(key =>
          key.endsWith("_cache") || key.endsWith("_time")
        );
      }

      for (const key of keysToRemove) {
        await this.dataManager.removeData(key);
      }

      console.log("Cache cleared:", keysToRemove.length, "items");
    } catch (error) {
      console.error("Error clearing cache:", error);
    }
  }
}

// Användningsexempel
const offlineManager = new OfflineManager(dataManager, apiClient);

const PostsScreen = () => {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(false);
  const [isOffline, setIsOffline] = useState(false);

  useEffect(() => {
    loadPosts();

    // Lyssna på nätverksstatus
    const unsubscribe = NetInfo.addEventListener(state => {
      setIsOffline(!state.isConnected);
    });

    return () => unsubscribe();
  }, []);

  const loadPosts = async () => {
    setLoading(true);
    try {
      const result = await offlineManager.fetchWithCache(
        "/posts",
        "posts_cache",
      );
      setPosts(result.data);

      if (result.fromCache) {
        // Visa indikation att data kommer från cache
        console.log("Data loaded from cache");
      }
    } catch (error) {
      Alert.alert("Fel", error.message);
    } finally {
      setLoading(false);
    }
  };

  const createPost = async (postData) => {
    try {
      await offlineManager.postWithQueue("/posts", postData, "create_post");
      Alert.alert("Framgång", "Post skapad!");
      loadPosts(); // Ladda om posts
    } catch (error) {
      if (error.message.includes("Offline")) {
        Alert.alert("Offline", "Post kommer att skickas när du är online igen");
      } else {
        Alert.alert("Fel", "Kunde inte skapa post");
      }
    }
  };

  return (
    <View style={styles.container}>
      {isOffline && (
        <View style={styles.offlineNotice}>
          <Text style={styles.offlineText}>📵 Offline-läge</Text>
        </View>
      )}

      {/* Posts-lista */}
    </View>
  );
};
```

## Prestandaoptimering

### Analogi: Prestanda som bilens effektivitet

Prestanda i React Native är som bilens bränsleeffektivitet:

- **Minne** = Bensintank (begränsat utrymme)
- **CPU** = Motor (begränsad kraft)
- **Rendering** = Väg framför (måste vara smidig)
- **Optimeringar** = Tuning (få ut mer av resurserna)

### React.memo och optimerade komponenter

```jsx
import React, { memo, useCallback, useMemo } from "react";
import { FlatList, Image } from "react-native";

// Optimerad komponent med React.memo
const PostItem = memo(({ post, onPress, onLike }) => {
  // useMemo för beräkningar som inte behöver köras om
  const formattedDate = useMemo(() => {
    return new Date(post.createdAt).toLocaleDateString("sv-SE");
  }, [post.createdAt]);

  const postSummary = useMemo(() => {
    return post.content.length > 100
      ? post.content.substring(0, 100) + "..."
      : post.content;
  }, [post.content]);

  return (
    <TouchableOpacity
      style={styles.postItem}
      onPress={() => onPress(post.id)}
      activeOpacity={0.7}
    >
      {/* Optimerad bildladdning */}
      <Image
        source={{ uri: post.imageUrl }}
        style={styles.postImage}
        resizeMode="cover"
        // Lazy loading för bilder
        defaultSource={require("./placeholder.png")}
      />

      <View style={styles.postContent}>
        <Text style={styles.postTitle} numberOfLines={2}>
          {post.title}
        </Text>
        <Text style={styles.postSummary} numberOfLines={3}>
          {postSummary}
        </Text>
        <Text style={styles.postDate}>{formattedDate}</Text>
      </View>

      <TouchableOpacity
        style={styles.likeButton}
        onPress={() => onLike(post.id)}
      >
        <Text style={styles.likeCount}>{post.likes}</Text>
        <Text>❤️</Text>
      </TouchableOpacity>
    </TouchableOpacity>
  );
});

// Huvudkomponent med optimerad lista
const PostsList = () => {
  const [posts, setPosts] = useState([]);
  const [refreshing, setRefreshing] = useState(false);

  // useCallback för att undvika onödiga re-renders
  const handlePostPress = useCallback((postId) => {
    navigation.navigate("PostDetails", { postId });
  }, [navigation]);

  const handleLike = useCallback((postId) => {
    setPosts(prevPosts =>
      prevPosts.map(post =>
        post.id === postId
          ? { ...post, likes: post.likes + 1 }
          : post
      )
    );
  }, []);

  const handleRefresh = useCallback(async () => {
    setRefreshing(true);
    try {
      // Hämta nya posts
      const newPosts = await fetchPosts();
      setPosts(newPosts);
    } catch (error) {
      console.error("Refresh failed:", error);
    } finally {
      setRefreshing(false);
    }
  }, []);

  // Optimerad renderItem med useCallback
  const renderPost = useCallback(({ item }) => (
    <PostItem
      post={item}
      onPress={handlePostPress}
      onLike={handleLike}
    />
  ), [handlePostPress, handleLike]);

  // Key extractor
  const keyExtractor = useCallback((item) => item.id.toString(), []);

  return (
    <FlatList
      data={posts}
      renderItem={renderPost}
      keyExtractor={keyExtractor}
      // Prestandaoptimeringar för FlatList
      removeClippedSubviews={true} // Ta bort komponenter utanför skärmen
      maxToRenderPerBatch={5} // Rendera max 5 items åt gången
      windowSize={10} // Behåll 10 skärmar i minnet
      initialNumToRender={10} // Rendera 10 items initialt
      getItemLayout={(data, index) => ({
        length: 120, // Fast höjd på items för bättre prestanda
        offset: 120 * index,
        index,
      })}
      // Refresh control
      refreshControl={
        <RefreshControl
          refreshing={refreshing}
          onRefresh={handleRefresh}
        />
      }
      // Loading states
      ListFooterComponent={refreshing
        ? <ActivityIndicator style={{ padding: 20 }} />
        : null}
    />
  );
};
```

### Bildoptimering och lazy loading

```jsx
import { useEffect, useRef, useState } from "react";
import { Dimensions, PixelRatio } from "react-native";

// Optimerad bildkomponent
const OptimizedImage = ({ source, style, ...props }) => {
  const [loaded, setLoaded] = useState(false);
  const [error, setError] = useState(false);
  const isMounted = useRef(true);

  useEffect(() => {
    return () => {
      isMounted.current = false;
    };
  }, []);

  // Beräkna optimal bildstorlek
  const optimizedSource = useMemo(() => {
    if (typeof source === "string") {
      const { width } = Dimensions.get("window");
      const pixelRatio = PixelRatio.get();
      const optimalWidth = Math.round(width * pixelRatio);

      // Lägg till storlek i URL om möjligt (för bildservrar som stöder det)
      return source.includes("?")
        ? `${source}&w=${optimalWidth}&q=80`
        : `${source}?w=${optimalWidth}&q=80`;
    }
    return source;
  }, [source]);

  const handleLoad = () => {
    if (isMounted.current) {
      setLoaded(true);
    }
  };

  const handleError = () => {
    if (isMounted.current) {
      setError(true);
    }
  };

  return (
    <View style={[style, styles.imageContainer]}>
      {/* Placeholder medan bilden laddar */}
      {!loaded && !error && (
        <View style={[style, styles.placeholder]}>
          <ActivityIndicator size="small" color="#ccc" />
        </View>
      )}

      {/* Felbild om laddning misslyckades */}
      {error && (
        <View style={[style, styles.errorPlaceholder]}>
          <Text style={styles.errorText}>📷</Text>
        </View>
      )}

      {/* Faktiska bilden */}
      <Image
        {...props}
        source={{ uri: optimizedSource }}
        style={[
          style,
          { opacity: loaded ? 1 : 0 },
        ]}
        onLoad={handleLoad}
        onError={handleError}
        // Viktigt för prestanda
        resizeMode="cover"
        fadeDuration={300} // Smooth fade-in
      />
    </View>
  );
};

// Lazy loading-komponent för stora listor
const LazyImage = ({ source, style, threshold = 100 }) => {
  const [shouldLoad, setShouldLoad] = useState(false);
  const viewRef = useRef();

  useEffect(() => {
    const checkVisibility = () => {
      if (viewRef.current) {
        viewRef.current.measure((x, y, width, height, pageX, pageY) => {
          const screenHeight = Dimensions.get("window").height;
          const isVisible = pageY + height >= -threshold
            && pageY <= screenHeight + threshold;

          if (isVisible && !shouldLoad) {
            setShouldLoad(true);
          }
        });
      }
    };

    // Kontrollera synlighet vid first render
    const timer = setTimeout(checkVisibility, 100);

    return () => clearTimeout(timer);
  }, [threshold, shouldLoad]);

  return (
    <View ref={viewRef} style={style}>
      {shouldLoad
        ? <OptimizedImage source={source} style={style} />
        : (
          <View style={[style, styles.lazyPlaceholder]}>
            <Text style={styles.lazyText}>📷</Text>
          </View>
        )}
    </View>
  );
};
```

## Tillgänglighet (Accessibility)

### Analogi: Tillgänglighet som ramper och hissar

Tillgänglighet i appar är som fysisk tillgänglighet i byggnader:

- **Rampor** (Screen readers) - Alternativa vägar till innehåll
- **Hissar** (Voice control) - Andra sätt att navigera
- **Tydlig skyltning** (Semantic labels) - Hjälper alla förstå
- **Ljus och kontrast** (Visual accessibility) - Gör det lättare att se

### Grundläggande tillgänglighet

```jsx
import { AccessibilityInfo } from "react-native";

const AccessibleButton = ({
  title,
  onPress,
  disabled = false,
  accessibilityLabel,
  accessibilityHint,
  accessibilityRole = "button",
}) => {
  return (
    <TouchableOpacity
      style={[
        styles.button,
        disabled && styles.disabledButton,
      ]}
      onPress={onPress}
      disabled={disabled}
      // Grundläggande accessibility props
      accessible={true}
      accessibilityLabel={accessibilityLabel || title}
      accessibilityHint={accessibilityHint}
      accessibilityRole={accessibilityRole}
      accessibilityState={{ disabled }}
      // Viktigt för navigation med tab
      focusable={!disabled}
    >
      <Text
        style={[
          styles.buttonText,
          disabled && styles.disabledText,
        ]}
      >
        {title}
      </Text>
    </TouchableOpacity>
  );
};

// Tillgänglig lista
const AccessiblePostsList = ({ posts, onPostPress }) => {
  const renderPost = ({ item, index }) => (
    <TouchableOpacity
      style={styles.postItem}
      onPress={() => onPostPress(item)}
      // Detaljerad accessibility information
      accessible={true}
      accessibilityLabel={`Post ${index + 1} av ${posts.length}: ${item.title}`}
      accessibilityHint="Dubbeltryck för att öppna posten"
      accessibilityRole="button"
      // Gruppera relaterat innehåll
      accessibilityActions={[
        { name: "activate", label: "Öppna post" },
        { name: "longpress", label: "Visa alternativ" },
      ]}
      onAccessibilityAction={(event) => {
        switch (event.nativeEvent.actionName) {
          case "activate":
            onPostPress(item);
            break;
          case "longpress":
            showPostOptions(item);
            break;
        }
      }}
    >
      <View accessible={false}>
        {/* Gruppera visuella element */}
        <Text style={styles.postTitle}>{item.title}</Text>
        <Text style={styles.postSummary}>{item.summary}</Text>
        <Text style={styles.postMeta}>
          {item.author} • {item.readTime} min läsning
        </Text>
      </View>
    </TouchableOpacity>
  );

  return (
    <FlatList
      data={posts}
      renderItem={renderPost}
      keyExtractor={(item) => item.id}
      // Hjälptext för listan
      accessibilityLabel={`Lista med ${posts.length} blogginlägg`}
      accessibilityHint="Navigera genom inläggen med svep"
    />
  );
};

// Tillgänglig form
const AccessibleForm = ({ onSubmit }) => {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [errors, setErrors] = useState({});

  const validateForm = () => {
    const newErrors = {};

    if (!name.trim()) {
      newErrors.name = "Namn är obligatoriskt";
    }

    if (!email.trim()) {
      newErrors.email = "Email är obligatorisk";
    } else if (!/\S+@\S+\.\S+/.test(email)) {
      newErrors.email = "Ange en giltig email-adress";
    }

    setErrors(newErrors);

    // Läs upp fel för skärmläsare
    if (Object.keys(newErrors).length > 0) {
      const errorMessages = Object.values(newErrors).join(". ");
      AccessibilityInfo.announceForAccessibility(
        `Valideringsfel: ${errorMessages}`,
      );
    }

    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = () => {
    if (validateForm()) {
      onSubmit({ name, email });
      AccessibilityInfo.announceForAccessibility(
        "Formulär skickat framgångsrikt",
      );
    }
  };

  return (
    <View style={styles.form}>
      {/* Rubrik med semantisk struktur */}
      <Text
        style={styles.formTitle}
        accessibilityRole="header"
        accessibilityLevel={1}
      >
        Kontaktformulär
      </Text>

      {/* Namn-fält */}
      <View style={styles.inputContainer}>
        <Text
          style={styles.label}
          // Koppla label till input
          nativeID="nameLabel"
        >
          Namn *
        </Text>
        <TextInput
          style={[
            styles.input,
            errors.name && styles.inputError,
          ]}
          value={name}
          onChangeText={setName}
          placeholder="Ange ditt namn"
          // Accessibility för input
          accessible={true}
          accessibilityLabel="Namn, obligatoriskt fält"
          accessibilityLabelledBy="nameLabel"
          accessibilityRequired={true}
          // Visa fel för skärmläsare
          accessibilityInvalid={!!errors.name}
          accessibilityErrorMessage={errors.name}
          // Tangentbordstyp
          autoCapitalize="words"
          autoCorrect={false}
        />
        {errors.name && (
          <Text
            style={styles.errorText}
            accessibilityRole="alert"
            accessibilityLiveRegion="assertive"
          >
            {errors.name}
          </Text>
        )}
      </View>

      {/* Email-fält */}
      <View style={styles.inputContainer}>
        <Text style={styles.label} nativeID="emailLabel">
          Email *
        </Text>
        <TextInput
          style={[styles.input, errors.email && styles.inputError]}
          value={email}
          onChangeText={setEmail}
          placeholder="din@email.com"
          keyboardType="email-address"
          autoCapitalize="none"
          autoCorrect={false}
          accessible={true}
          accessibilityLabel="Email-adress, obligatoriskt fält"
          accessibilityLabelledBy="emailLabel"
          accessibilityRequired={true}
          accessibilityInvalid={!!errors.email}
          accessibilityErrorMessage={errors.email}
        />
        {errors.email && (
          <Text
            style={styles.errorText}
            accessibilityRole="alert"
            accessibilityLiveRegion="assertive"
          >
            {errors.email}
          </Text>
        )}
      </View>

      {/* Submit-knapp */}
      <AccessibleButton
        title="Skicka"
        onPress={handleSubmit}
        accessibilityHint="Skickar formuläret om alla fält är korrekt ifyllda"
      />
    </View>
  );
};

// Accessibility helpers
const AccessibilityHelpers = {
  // Kontrollera om skärmläsare är aktiverad
  async isScreenReaderEnabled() {
    return await AccessibilityInfo.isScreenReaderEnabled();
  },

  // Meddela skärmläsare
  announce(message) {
    AccessibilityInfo.announceForAccessibility(message);
  },

  // Fokusera på element programmatiskt
  setAccessibilityFocus(reactTag) {
    AccessibilityInfo.setAccessibilityFocus(reactTag);
  },

  // Kontrollera om bold text är aktiverat
  async isBoldTextEnabled() {
    return await AccessibilityInfo.isBoldTextEnabled();
  },

  // Kontrollera reducerad rörelse-inställning
  async isReduceMotionEnabled() {
    return await AccessibilityInfo.isReduceMotionEnabled();
  },
};

// Exempel på användning av accessibility helpers
const AccessibilityAwareComponent = () => {
  const [screenReaderEnabled, setScreenReaderEnabled] = useState(false);
  const [reduceMotion, setReduceMotion] = useState(false);

  useEffect(() => {
    const checkAccessibilitySettings = async () => {
      const isScreenReader = await AccessibilityHelpers.isScreenReaderEnabled();
      const isReduceMotion = await AccessibilityHelpers.isReduceMotionEnabled();

      setScreenReaderEnabled(isScreenReader);
      setReduceMotion(isReduceMotion);
    };

    checkAccessibilitySettings();

    // Lyssna på förändringar
    const screenReaderChangedSubscription = AccessibilityInfo.addEventListener(
      "screenReaderChanged",
      setScreenReaderEnabled,
    );

    const reduceMotionChangedSubscription = AccessibilityInfo.addEventListener(
      "reduceMotionChanged",
      setReduceMotion,
    );

    return () => {
      screenReaderChangedSubscription.remove();
      reduceMotionChangedSubscription.remove();
    };
  }, []);

  const handleSuccessAction = () => {
    // Visa visuell feedback
    showVisualSuccess();

    // Meddela skärmläsare
    AccessibilityHelpers.announce("Åtgärd genomförd framgångsrikt");
  };

  return (
    <View style={styles.container}>
      {/* Anpassa animationer baserat på inställningar */}
      <Animated.View
        style={[
          styles.animatedElement,
          {
            // Inaktivera animationer om användaren föredrar det
            transform: reduceMotion ? [] : [{ scale: animationScale }],
          },
        ]}
      >
        <Text>Animerat innehåll</Text>
      </Animated.View>
    </View>
  );
};
```

## Sammanfattning av Vecka 4

### Vad har du lärt dig?

**API-kommunikation:**
✅ **Säkra API-anrop** - Hantera HTTP-metoder, headers, felhantering och timeouts
✅ **Avancerad felhantering** - Specifika HTTP-status koder och användarfeedback
✅ **Batch requests** - Effektiv hantering av flera API-anrop
✅ **Request/Response interceptors** - Automatisk token-hantering

**Säkerhet:**
✅ **Autentisering** - Säker inloggning, token-hantering och refresh-mekanismer
✅ **Säker lagring** - SecureStore för känslig data, AsyncStorage för vanlig data
✅ **API-säkerhet** - Headers, authorization och säkra anrop

**Offline-support:**
✅ **Cache-strategier** - Smart cachning med ålderskontroll
✅ **Offline-kö** - Hantering av förfrågningar när internet saknas
✅ **Nätverksstatus** - Detektera online/offline och agera därefter

**Prestanda:**
✅ **React.memo och hooks** - Förhindra onödiga re-renders
✅ **FlatList-optimering** - Effektiva listor med stora dataset
✅ **Bildoptimering** - Lazy loading och responsiva bilder

**Tillgänglighet:**
✅ **Screen reader-stöd** - Labels, hints och semantic markup
✅ **Navigering** - Fokushantering och tangentbordsnavigation
✅ **Accessibility-inställningar** - Anpassning för användarpreferenser

### Nästa steg

I **Vecka 5** kommer vi att fokusera på helhetsutveckling och AI-integration:

- Byggprocess för Android och iOS
- Publicering och distribution
- AI-verktyg för utveckling och debugging
- Testning och kvalitetssäkring
- Projekthantering och best practices

### Praktiska övningsförslag

**1. API-integration app:**

- Bygg en app som konsumerar ett riktigt API (t.ex. väder, nyheter)
- Implementera offline-support med cache
- Lägg till säker autentisering

**2. Prestandaoptimerad lista:**

- Skapa en app med tusentals poster
- Optimera med React.memo, useCallback och FlatList-props
- Implementera lazy loading för bilder

**3. Tillgänglig e-handelsapp:**

- Bygg en produktlista med fullständig accessibility-support
- Testa med VoiceOver (iOS) eller TalkBack (Android)
- Implementera tangentbordsnavigation

**4. Offline-först app:**

- Skapa en antecknings-app som fungerar offline
- Synkronisera data när internet är tillgängligt
- Hantera konflikter när samma data ändrats offline och online

### Viktiga takeaways

🔒 **Säkerhet först** - Hantera känslig data med största försiktighet

📡 **Nätverket är opålitligt** - Bygg alltid för offline-scenarier

⚡ **Prestanda påverkar användarupplevelse** - Optimera tidigt och ofta

♿ **Tillgänglighet är grundläggande** - Alla användare ska kunna använda din app

🔄 **Felhantering är kritiskt** - Ge användarna tydlig feedback i alla situationer

📊 **Mätning leder till förbättring** - Övervaka prestanda och användarinteraktioner

---

_Nästa: [Vecka 5 - Helhetsutveckling och AI-integration](./vecka-5.md) →_
