# Persisting Auth State with Context API & AsyncStorage

In mobile development, a common challenge is maintaining the user's login session across app restarts. In my recent React Native project, I implemented a robust authentication flow using **Context API** for global state management and **AsyncStorage** for local persistence.

### 1. The Problem
Using local component state (e.g., `useState` inside `App.js`) creates two issues:
1.  **Prop Drilling:** Passing `user` and `login()` props down through every screen is messy.
2.  **Data Loss:** When the app closes, the state is reset, forcing the user to log in again.

### 2. The Solution: AuthProvider Pattern
I encapsulated the logic within a custom `AuthProvider`. This component syncs the in-memory state with the device's storage.

```javascript
import { createContext, useContext, useState, useEffect } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

const AuthContext = createContext(undefined);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true); // Prevents "flashing" login screen

  // 1. Init: Load user from storage when app starts
  useEffect(() => {
    const loadUser = async () => {
      try {
        const storedUser = await AsyncStorage.getItem('user');
        if (storedUser) setUser(JSON.parse(storedUser));
      } catch (e) {
        console.error(e);
      } finally {
        setLoading(false);
      }
    };
    loadUser();
  }, []);

  // 2. Sync: Update storage whenever 'user' state changes
  useEffect(() => {
    if (loading) return; // Skip initial load
    
    if (user) {
      AsyncStorage.setItem('user', JSON.stringify(user));
    } else {
      AsyncStorage.removeItem('user'); // Handle logout
    }
  }, [user, loading]);

  const login = (username) => setUser({ username });
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => useContext(AuthContext);
```

### 3. Key Benefits
**Separation of Concerns:**  Screens (UI) don't need to know about  `AsyncStorage`. They just call  `useAuth()`.
**Reactive Storage:**  By adding  `[user]`  to the dependency array of  `useEffect`, any change to the user state automatically updates the local storage.
**Loading State:**  Handling the  `loading`  flag is crucial to prevent the Login screen from briefly appearing (flashing) for an already authenticated user.
