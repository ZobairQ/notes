# What is middleware

A middleware is a piece of code that gets executed after an action is dispatched but before the action reaches the reducer.
You have middleware for

- Calling apis
- Error reporting
- Analytics
- Authorization
- etc.

We can forexample when some action is dispatched, we can first log it then check if the user is authorized to perform this action before we actually perform the action. Then we can stop the action before it is perform if the user is not autorized.

# Creating Middleware

We will be writing middleware from scratch.

```JS
const logger = store=> next => action => {
    console.log("store", store);
    console.log("next", next);
    console.log("action", action);
    next(action);
}


export default logger;

```

The store is a store but not the store.
next is the next middlware. But your middlware is the only middleware, then next is the reducer. If you dont invoke `next(action)` your actions will not dispatch.
The action is of course the action that is being dipatched.

The three arrows are result of currying.

The way you use middlwares in your store

```JS
import { configureStore } from "@reduxjs/toolkit";
import reducer from "./reducer";
import logger from "./middlware/logger";

const store = configureStore({ reducer, middleware: [logger] });

export default store;
```

# Parameterizing Middlware

If you want to add parameters to you middlware you have to add it before the store parameter. When you call your function with you paramter then the function will return a middlware as result of currying.

```JS
const logger = param => store=> next => action => {
    console.log("Parameter", param);
    next(action);
}


export default logger;
```

when using it

```JS
import { configureStore } from "@reduxjs/toolkit";
import reducer from "./reducer";
import logger from "./middlware/logger";

const store = configureStore({ reducer, middleware: [logger("Some awesomeParameter")] });

export default store;
```

Notice we invoke `logger` with a parameter. Because when you provide the first function a parameter then i will return a middleware. If you dont execute `logger` you will get and error.

# Dispatching Functions

When calling apis we need to be able to dispatch functions on our soter.dispatch.
`store.dispatch` expects a plain object but we can use middleware to accomplish what we want.
The middleware `func` as shown check if the action that is being dispatched is a function if that is the case we invoke the action with 2 parameters a reference to dispatch, and the reference to the getState method.
You can then use the reference for dispatch method inside your dispatch as shown.

```JS
const func = (store) => (next) => (action) => {
  if (typeof action === "function") {
    action(store.dispatch, store.getState);
  } else {
    next(action);
  }
};

export default func;
```

The way the middleware is used in the store

```JS
import { configureStore, getDefaultMiddleware } from "@reduxjs/toolkit";
import reducer from "./reducer";
import logger from "./middlware/logger";
import func from "./middlware/func";

const store = configureStore({
  reducer,
  middleware: [logger("something"), func],
});

export default store;
```

and now we can dispatch following

```JS
import store from "./store/store";
import {
  addBug,
  resolveBug,
  getUnresolvedBugs,
  getBugByUserId,
  assignBugToUser,
} from "./store/bugs";
import { addProject } from "./store/projects";
import { addNewUser } from "./store/auth";
import { addUser } from "./store/users";

 store.dispatch((dispatch, getState) => {
   dispatch({type:'receiveBug', bugs:[1,2,3]});
   console.log(getState());
 });
```

## Thunk

However we dont need to write `func` middleware from scratch.
There is a library called `Redux-thunk` that already does this.
However it is also included in the redux toolkit.
you need to do following to get it working

```JS
import { configureStore, getDefaultMiddleware } from "@reduxjs/toolkit";
import reducer from "./reducer";
import logger from "./middlware/logger";

const store = configureStore({
  reducer,
  middleware: [...getDefaultMiddleware(), logger("something")],
});

export default store;
```

The spreader of the `getDefaultMiddleware()` will ensure that the Thunk middleware is a part of your store and now you can invoke dispatch with function calls.
