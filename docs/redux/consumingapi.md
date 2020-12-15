# Introduction

We will learn how to consume API with redux.

# Setting up the Backend

We just create an express server and create a fake api so we can learn to consume apis.

# The Approach

The reducer is a Pure function and therefore should not have any side effects.
The actions creators is where the api calls should be. With Thunk middleware an action creator can return a function.
You have for to do following

1. Call Api
2. Resolved : Dispatch(Success)
3. Rejected : Dispatch(Error)
   This is very repetitive and time consuming. It's easier to use middleware.

# Api Middleware

We create a middleware to handle the api call.

```JS
import axios from "axios";

const api = ({ dispatch }) => (next) => async (action) => {
  if (action.type !== "API_CALL_BEGIN") {
    return next(action);
  }
  next(action);
  const { url, method, data, onSuccess, onError } = action.payload;
  try {
    const response = await axios.request({
      baseURL: "http://localhost:9002/api",
      url,
      method,
      data,
    });
    dispatch({ type: onSuccess, payload: response.data });
  } catch (error) {
    dispatch({ type: onError, payload: error });
  }
};

export default api;
```

You apply middleware as so

```JS
import reducer from "./reducer";
import toastify from "./middlware/toastify";
import api from "./middlware/api";

const store = configureStore({
  reducer,
  middleware: [...getDefaultMiddleware(), toastify, api],
});

export default store;
```

You use it as follows

```JS
import store from "./store/store";

 store.dispatch({
   type:'API_CALL_BEGIN',
   payload:{
     url:'/bugs',
     onSuccess:'bugsReceived',
     onError:'apiRequestFailed'
   }
 });
```

# Actions

Not really any notes

# Getting Data from the Server

We can see now that the action.payload is what we have fetched from the server. Now we need to write a reducer that stores the `action.payload` to the state.

```JS
import { createSlice } from "@reduxjs/toolkit";
import { createSelector } from "reselect";
let lastId = 0;

const slice = createSlice({
  name: "bugs",
  initialState: {
    list:[],
    loading:false,
    lastFetch:null
  },
  reducers: {
    receiveBugs: (bugs, action) =>{
      bugs.list = action.payload;
    },
    assignBugToUser:(bugs, action)=> {
      const {bugId, userId} = action.payload;
      const index = bugs.list.findIndex(bug => bug.id === bugId);
      bugs.list[index].userId = userId;
    },
    addBug: (bugs, action) => {
      bugs.list.push({
        description: action.payload.description,
        id: ++lastId,
        resolved: false,
      });
    },
    resolveBug: (bugs, action) => {
      const index = bugs.list.findIndex((bug) => bug.id === action.payload.id);
      bugs.list[index].resolved = true;
    },
    removeBug: (bugs, action) => {
      bugs-list.filter((bug) => bug.id === action.payload.id);
    },
  },
});

export const { addBug, resolveBug, removeBug, assignBugToUser } = slice.actions;
export default slice.reducer;
// Selector function
export const getBugByUserId = userId => createSelector(
  (state) => state.entities.bugs,
  (bugs) =>bugs.filter(bug => bug.userId === userId)
);
export const getUnresolvedBugs = createSelector(
  (state) => state.entities.bugs,
  (state) => state.entities.projects,
  (bugs, projects) => bugs.filter((bug) => !bug.resolved)
);
```

and you can use it like this

```JS
 store.dispatch(apiCallBegin({
  url:'/bugs',
  onSuccess:'bugs/receiveBugs',
}));
```

The problem is that you should now have this type of logic in the ui Layer. this is why we will re-write it to following:

```JS
import store from "./store/store";
import {
  loadBugs
} from "./store/bugs";
 store.dispatch(loadBugs());
```

`loadBugs` is a action that calls the api like so

```JS
import { createSlice } from "@reduxjs/toolkit";
import { createSelector } from "reselect";
import { apiCallBegin } from "./api";

let lastId = 0;

const slice = createSlice({
  name: "bugs",
  initialState: {
    list: [],
    loading: false,
    lastFetch: null,
  },
  reducers: {
    receiveBugs: (bugs, action) => {
      bugs.list = action.payload;
    },
    assignBugToUser: (bugs, action) => {
      const { bugId, userId } = action.payload;
      const index = bugs.list.findIndex((bug) => bug.id === bugId);
      bugs.list[index].userId = userId;
    },
    addBug: (bugs, action) => {
      bugs.list.push({
        description: action.payload.description,
        id: ++lastId,
        resolved: false,
      });
    },
    resolveBug: (bugs, action) => {
      const index = bugs.list.findIndex((bug) => bug.id === action.payload.id);
      bugs.list[index].resolved = true;
    },
    removeBug: (bugs, action) => {
      bugs - list.filter((bug) => bug.id === action.payload.id);
    },
  },
});

export const {
  addBug,
  resolveBug,
  removeBug,
  assignBugToUser,
  receiveBugs,
} = slice.actions;
export default slice.reducer;

//Action creators
const url = "/bugs";
export const loadBugs = () =>
  apiCallBegin({
    url,
    onSuccess: receiveBugs.type,
  });

// Selector function
export const getBugByUserId = (userId) =>
  createSelector(
    (state) => state.entities.bugs,
    (bugs) => bugs.filter((bug) => bug.userId === userId)
  );
export const getUnresolvedBugs = createSelector(
  (state) => state.entities.bugs,
  (state) => state.entities.projects,
  (bugs, projects) => bugs.filter((bug) => !bug.resolved)
);

```
