Redux persist helps you store your state in localstorage and rehydrate it whenever needed. In order to use redux-persist you need to

1. install it
2. create a persist Store
3. create a persist Config and persit reducer
4. wrap your App with persistGate (react)

## 1. Install it

in order to install you type `npm install redux-persist` in your console.

## 2. Create Persist store

wrap your store in persistStore from 'redux-persist'
like so

```JS
import { configureStore, getDefaultMiddleware } from "@reduxjs/toolkit";
import logger from "redux-logger";
import rootReducer from "./root-reducer";
import { persistStore } from "redux-persist";

export const store = configureStore({
  reducer: rootReducer,
  middleware: [...getDefaultMiddleware({ serializableCheck: false }), logger],
});

export const persistor = persistStore(store);
```

## 3. Create Persist config and persist reducer

Wrap your combined reducer in Persist reducer and createa persist config that is JSON

```JS
import { combineReducers } from "redux";
import { persistReducer} from 'redux-persist';
import storage from 'redux-persist/lib/storage';
import userReducer from './user/users'
// import cartReducer from "./cart/cart-reducer";
import cartReducer from './cart/cart'

const persistConfig = {
  key:'root',
  storage,
  whitelist:['cart'] // Only cart will be persisted.
}

const rootReducer = combineReducers({
  user: userReducer,
  cart: cartReducer
});

export default persistReducer(persistConfig, rootReducer);
```

Whitelist is optional. If you dont specify everything is going to be persisted. We wish only the cart to be persited in this case.

## 4. Wrap your react App (index.js) with persist gate

Wrap the app with PersistGate and specify the persistor. The `persistor` is your store wrapped in persist Store (look step 2)

```JS
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router } from "react-router-dom";
import { store, persistor } from "./redux/store";
import { PersistGate } from "redux-persist/integration/react";

import "./index.css";
import App from "./App";
import { Provider } from "react-redux";

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <Router>
        <PersistGate persistor={persistor}>
          <App />
        </PersistGate>
      </Router>
    </Provider>
  </React.StrictMode>,
  document.getElementById("root")
);
```
