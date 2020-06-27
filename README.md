# Recoil Async Persist

Tools for asynchronous state persistence using Recoil

##### Why?
Recoil doesn't currently have built in support for loading stored state asynchronously, this presents a problem for environments like React Native where even local state is fetched asynchronously.

This library also allows you to load your atoms from remote state.

### Install

```bash
yarn add recoil-async-persist
```

### Usage
There are 3 ways of interacting with your stored state, 2 hooks for setting and getting state,  and an app wrapper that will display a fallback until the given atom was loaded from storage.

Note: all storage objects used to interact with persisted state should have `get` and `set` properties. You can pass these as properties on an object, e.g. `storage.get`, or use the `createStorage` utility provided.

##### RecoilPersistGate
This will display a loading component while your storage is being fetched. Useful for display-critical state, such as light/dark themes, since your app will not be visible until loading is complete.

Inside `App.js`:
```js
<RecoilPersistGate atom={themeAtom} storage={themeStorage} loading={<Spinner />}>
  <App />
</RecoilPersistGate>
```

##### useRecoilPersistLoadStorage
A hook similar to `RecoilPersistGate`. Accepts an atom and storage, will fetch state and return a loading boolean as well as the given atom's value post-initialisation.

```js
const [loaded, atomState] = useRecoilPersistLoadStorage({ atom: myAtom, storage: myStorage });
```

##### useRecoilPersistState
A wrapper around `useRecoilState` that will automatically sync any state updates to storage.

```js
const [state, setState] = useRecoilPersistState({ atom, storage });
```

##### createStorage
A utility used to create `storage` objects. Not necessary but can help simplify persisting state.

Requires the given getter and setter to be promise-based.

Note: depending on your chosen storage library you may want to include any `JSON.stringify/JSON.parse` logic in the provided getter and setter.

```js
const getTheme = () => AsyncStorage.getItem(themeKey);
const themeSetter = (value) => AsyncStorage.setItem(themeKey, value);
const themeStorage = createStorage({ get: getTheme, set: setTheme });
```

Now just pass the created storage object to anything that requires a `storage` property, e.g. `useRecoilPersisState`

```js
import { themeAtom, themeStorage } from './atoms/theme';

//...

function SomeComponent() {
  const [theme, setTheme] = useRecoilPersistState({ atom: themeAtom, storage: themeStorage });
```
