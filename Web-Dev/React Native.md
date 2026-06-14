---
id: a1b2c3d4-1196-4000-8000-000000000196
title: React Native
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001196
---
# React Native

React Native builds native mobile apps using React and JavaScript. It compiles to native UI components rather than web views.

## Architecture

```
React Native Bridge (legacy architecture):
  JavaScript Thread ──► Bridge (async, serialized) ──► Native Thread
  (React, logic)                                            (UI, platform APIs)

New Architecture (Fabric + TurboModules):
  JavaScript ──► JSI (synchronous, direct) ──► Native (C++ layer)
  (Fabric renderer, TurboModules, Codegen)

Key improvements in new architecture:
  - Synchronous native calls
  - Less serialization overhead
  - Type-safe code generation
  - Better startup performance
```

## Basic Component

```javascript
import React from 'react';
import {
    View,
    Text,
    StyleSheet,
    TouchableOpacity,
    TextInput,
    FlatList,
} from 'react-native';

export default function App() {
    const [items, setItems] = React.useState([]);
    const [text, setText] = React.useState('');

    const addItem = () => {
        if (text.trim()) {
            setItems([...items, { id: Date.now(), text: text.trim() }]);
            setText('');
        }
    };

    return (
        <View style={styles.container}>
            <Text style={styles.title}>My List</Text>

            <View style={styles.inputRow}>
                <TextInput
                    style={styles.input}
                    value={text}
                    onChangeText={setText}
                    placeholder="Add item..."
                />
                <TouchableOpacity style={styles.button} onPress={addItem}>
                    <Text style={styles.buttonText}>Add</Text>
                </TouchableOpacity>
            </View>

            <FlatList
                data={items}
                keyExtractor={(item) => item.id.toString()}
                renderItem={({ item }) => (
                    <Text style={styles.item}>{item.text}</Text>
                )}
            />
        </View>
    );
}

const styles = StyleSheet.create({
    container: { flex: 1, padding: 20, backgroundColor: '#fff' },
    title: { fontSize: 24, fontWeight: 'bold', marginBottom: 20 },
    inputRow: { flexDirection: 'row', marginBottom: 20 },
    input: { flex: 1, borderWidth: 1, borderColor: '#ddd', padding: 10, borderRadius: 8 },
    button: { backgroundColor: '#007AFF', padding: 12, borderRadius: 8, marginLeft: 8 },
    buttonText: { color: '#fff', fontWeight: '600' },
    item: { padding: 12, borderBottomWidth: 1, borderBottomColor: '#eee' },
});
```

## Navigation

```javascript
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

function HomeStack() {
    return (
        <Stack.Navigator>
            <Stack.Screen name="Home" component={HomeScreen} />
            <Stack.Screen name="Profile" component={ProfileScreen} />
            <Stack.Screen name="Settings" component={SettingsScreen} />
        </Stack.Navigator>
    );
}

export default function App() {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen name="Home" component={HomeStack} />
                <Tab.Screen name="Search" component={SearchScreen} />
                <Tab.Screen name="Cart" component={CartScreen} />
            </Tab.Navigator>
        </NavigationContainer>
    );
}
```

## Platform APIs

```javascript
import { Platform, PermissionsAndroid, Alert } from 'react-native';
import Geolocation from '@react-native-community/geolocation';
import CameraRoll from '@react-native-camera-roll/camera-roll';

async function requestLocation() {
    if (Platform.OS === 'android') {
        const granted = await PermissionsAndroid.request(
            PermissionsAndroid.PERMISSIONS.ACCESS_FINE_LOCATION
        );
        if (granted !== PermissionsAndroid.RESULTS.GRANTED) {
            Alert.alert('Permission denied');
            return;
        }
    }

    Geolocation.getCurrentPosition(
        (position) => console.log(position.coords),
        (error) => console.error(error),
        { enableHighAccuracy: true, timeout: 15000 }
    );
}
```

## Networking

```javascript
import { useEffect, useState } from 'react';

function useAPI(endpoint) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const controller = new AbortController();

        async function fetchData() {
            try {
                setLoading(true);
                const response = await fetch(endpoint, {
                    signal: controller.signal,
                    headers: { 'Content-Type': 'application/json' }
                });
                if (!response.ok) throw new Error(`HTTP ${response.status}`);
                const result = await response.json();
                setData(result);
            } catch (err) {
                if (err.name !== 'AbortError') setError(err);
            } finally {
                setLoading(false);
            }
        }

        fetchData();
        return () => controller.abort();
    }, [endpoint]);

    return { data, loading, error };
}
```

## Performance Optimization

```javascript
import React, { memo, useMemo, useCallback } from 'react';

// Memoize component to prevent re-renders
const ExpensiveItem = memo(({ item, onPress }) => {
    return (
        <TouchableOpacity onPress={() => onPress(item.id)}>
            <Text>{item.name}</Text>
        </TouchableOpacity>
    );
});

function List({ items }) {
    // Memoize computed values
    const sortedItems = useMemo(() =>
        [...items].sort((a, b) => a.name.localeCompare(b.name)),
        [items]
    );

    // Memoize callbacks
    const handlePress = useCallback((id) => {
        console.log('Pressed:', id);
    }, []);

    return (
        <FlatList
            data={sortedItems}
            keyExtractor={(item) => item.id}
            renderItem={({ item }) => (
                <ExpensiveItem item={item} onPress={handlePress} />
            )}
            // Virtualization props
            windowSize={5}
            maxToRenderPerBatch={10}
            removeClippedSubviews={true}
        />
    );
}
```

## Expo (Managed Workflow)

```bash
# Create Expo project
npx create-expo-app my-app
cd my-app
npx expo start

# Advantages over bare React Native:
# - No Xcode/Android Studio needed for development
# - OTA updates (no app store submission for JS changes)
# - Built-in libraries (camera, location, notifications)
# - EAS Build for native builds

# Key libraries
npx expo install expo-camera expo-location expo-notifications
```

```javascript
import { Camera } from 'expo-camera';
import * as Location from 'expo-location';
import * as Notifications from 'expo-notifications';

async function takePhoto() {
    const { status } = await Camera.requestCameraPermissionsAsync();
    if (status !== 'granted') return;

    const photo = await Camera.takePictureAsync();
    console.log(photo.uri);
}
```
