# TimeTrackerApp Local Run Instructions

Run the app without backend server using:
```bash
expo start
```

## Detailed Implementation Steps:

1. **Remove Authentication**
   - Delete `screens/LoginScreen.tsx` and `screens/RegisterScreen.tsx`
   - Remove `contexts/AuthContext.tsx`
   - In `App.tsx`, replace:
     ```tsx
     <AuthProvider>
       <MainNavigator />
     </AuthProvider>
     ```
     with:
     ```tsx
     <MainNavigator />
     ```

2. **Implement Local Storage**
   - Install dependency: `npx expo install @react-native-async-storage/async-storage`
   - Create `services/LocalStorageService.ts`:
     ```ts
     import AsyncStorage from '@react-native-async-storage/async-storage';
     
     export const storeData = async (key: string, value: any) => {
       try {
         await AsyncStorage.setItem(key, JSON.stringify(value));
       } catch (e) { /* handle error */ }
     };
     
     export const getData = async (key: string) => {
       try {
         const value = await AsyncStorage.getItem(key);
         return value ? JSON.parse(value) : null;
       } catch (e) { /* handle error */ }
     };
     ```

3. **Replace API Calls**
   - In all service files (`services/*.ts`), replace API calls with:
     ```ts
     import { storeData, getData } from './LocalStorageService';
     
     // Example for time entries
     export const getTimeEntries = async () => {
       return await getData('timeEntries') || [];
     };
     
     export const addTimeEntry = async (entry) => {
       const entries = await getData('timeEntries') || [];
       entries.push(entry);
       await storeData('timeEntries', entries);
       return entry;
     };
     ```

4. **Update Navigation**
   - In `navigation/MainNavigator.tsx`, remove all authentication checks:
     ```diff
     - <Stack.Screen name="Login" component={LoginScreen} />
     - <Stack.Screen name="Register" component={RegisterScreen} />
     + {/* Directly show main app screens */}
     ```

## Prerequisites
1. Install dependencies:
```bash
npm install @react-native-async-storage/async-storage
```

## Implementation Steps

### 1. Remove Authentication
- Delete auth-related files:
  - `screens/LoginScreen.tsx`
  - `screens/RegisterScreen.tsx`
  - `contexts/AuthContext.tsx`

- Update `App.tsx`:
```diff
- import { AuthProvider } from './contexts/AuthContext';

return (
-  <AuthProvider>
    <MainNavigator />
-  </AuthProvider>
);
```

### 2. Implement Local Storage Service
Create `services/LocalStorage.ts`:
```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';

export const storeData = async (key: string, value: any) => {
  try {
    await AsyncStorage.setItem(key, JSON.stringify(value));
  } catch (e) {
    console.error('Error storing data', e);
  }
};

export const getData = async (key: string) => {
  try {
    const value = await AsyncStorage.getItem(key);
    return value ? JSON.parse(value) : null;
  } catch (e) {
    console.error('Error reading data', e);
    return null;
  }
};
```

### 3. Replace API Services
Modify time entry service (`services/TimeEntryService.ts`):
```typescript
import { storeData, getData } from './LocalStorage';

// Replace API calls with local storage operations
export const getTimeEntries = async () => {
  return await getData('timeEntries') || [];
};

export const createTimeEntry = async (entry) => {
  const entries = await getData('timeEntries') || [];
  const newEntry = { ...entry, id: Date.now().toString() };
  await storeData('timeEntries', [...entries, newEntry]);
  return newEntry;
};
```

### 4. Update Navigation
Modify `navigation/MainNavigator.tsx`:
```diff
- <Stack.Screen name="Login" component={LoginScreen} />
- <Stack.Screen name="Register" component={RegisterScreen} />
+ {/* Directly show main app screens */}
```

### 5. Initialize Sample Data
Add data initialization in `App.tsx`:
```typescript
useEffect(() => {
  const initSampleData = async () => {
    const hasData = await getData('initialized');
    if (!hasData) {
      await storeData('timeEntries', [
        { id: '1', project: 'Project Alpha', start: '2023-10-18T09:00', end: '2023-10-18T10:30' }
      ]);
      await storeData('initialized', true);
    }
  };
  initSampleData();
}, []);

## Key Modifications Made:

1. **Removed Authentication**
   - Deleted login/signup screens
   - Removed token handling
   - Removed auth context provider

2. **Local Data Storage**
   - Using AsyncStorage for all data persistence
   - Implemented CRUD operations for:
     - Time entries
     - Projects
     - User preferences

3. **API Call Removal**
   - Removed all Axios/fetch calls to backend
   - Replaced with local storage operations

4. **Navigation Simplification**
   - Removed protected route wrappers
   - Direct access to all screens

## Dependencies Added:
```json
"@react-native-async-storage/async-storage": "^1.21.0"
```

## Files Modified:
- `App.tsx` (removed auth provider)
- `navigation/MainNavigator.tsx` (removed auth flow)
- `services/` (replaced all API services with AsyncStorage)
- `contexts/AuthContext.tsx` (removed)
- `screens/LoginScreen.tsx` (removed)
- `screens/RegisterScreen.tsx` (removed)

## Key Files to Modify:
1. `App.tsx`
2. `navigation/MainNavigator.tsx`
3. All files in `services/` directory
4. `contexts/AuthContext.tsx` (delete)
5. `screens/LoginScreen.tsx` (delete)
6. `screens/RegisterScreen.tsx` (delete)

## Data Storage Structure:
```json
{
  "timeEntries": [
    {"id": 1, "project": "Project A", "start": "2023-10-18T09:00", "end": "2023-10-18T10:30"}
  ],
  "projects": [
    {"id": 1, "name": "Project A", "color": "#FF0000"}
  ],
  "settings": {
    "theme": "light",
    "hourlyRate": 25
  }
}

## Run the App
```bash
expo start
