# Introduction

We learn how to write clean redux code

# Structuring Files and Folders

You should also structure your Redux files in a folder for itself. However do not call the folder redux. You should never call your artifacts the same name as you tools.

# Ducks Pattern

Ducks Pattern is when you have one JS file that contains

1. Action types
1. Action creators
1. Reducer

There few importants things you need to note when using the ducks pattern.

1. You should only export your reducer as default.
2. You should export all of your action creators.
3. The ducks file name should describe what feature this redux part belongs to.

**Example**

I have redux for adding, removing and resolving bugs
so my ducks file is called `bugs.js`
It looks like this

```JS
// Actions types
const ADD_BUG = "BUG_ADD";
const REMOVE_BUG = "REMOVE_BUG";
const RESOLVE_BUG = "RESOLVE_BUG";

// Action creators
export const addBug = (description) => ({
  type: ADD_BUG,
  payload: {
    description: description,
  },
});

export const resolveBug = (id) => ({
  type: RESOLVE_BUG,
  payload: {
    id,
  },
});

// Reducer
let lastId = 0;
export default function reducer(state = [], action) {
  switch (action.type) {
    case ADD_BUG:
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case REMOVE_BUG:
      return state.filter((bug) => bug.id === action.payload.id);
    case RESOLVE_BUG:
      return state.map((bug) =>
        bug.id !== action.payload.id
          ? bug
          : {
              ...bug,
              resolved: true,
            }
      );
    default:
      return state;
  }
}
```

# Redux Toolkit

Is a library for simplifying redux code.

to install it run

`npm i @reduxjs/toolkit@1.2.5`

# Creating the store

Redux toolkit provides a bunch of utility functions to simplefy creating redux.

By using createstore functions from the toolkit we can simplify following code from

```JS
import { createStore } from "redux";
import { devToolsEnhancer } from "redux-devtools-extension";
import reducer from "./bugs";

const store = createStore(reducer, devToolsEnhancer({ trace: true }));

export default store;
```

to the following

```JS
import { configureStore } from "@reduxjs/toolkit";
import reducer from "./bugs";

const store = configureStore({ reducer });

export default store;
```

# Creating Actions

Now this is how we can create actions without too much boilerplate.
Inside the toolkit there is a function called createActions.
It will return an action creator.
You dont need action types anymore.
And since we use ducks pattern, we can use the actionCreators.types inside our reducer.
We can re-write our `bugs.js` from this

```JS
const ADD_BUG = "BUG_ADD";
const REMOVE_BUG = "REMOVE_BUG";
const RESOLVE_BUG = "RESOLVE_BUG";

// Action creators
export const addBug = (description) => ({
  type: ADD_BUG,
  payload: {
    description: description,
  },
});

export const resolveBug = (id) => ({
  type: RESOLVE_BUG,
  payload: {
    id,
  },
});

// Reducer
let lastId = 0;
export default function reducer(state = [], action) {
  switch (action.type) {
    case ADD_BUG:
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case REMOVE_BUG:
      return state.filter((bug) => bug.id === action.payload.id);
    case RESOLVE_BUG:
      return state.map((bug) =>
        bug.id !== action.payload.id
          ? bug
          : {
              ...bug,
              resolved: true,
            }
      );
    default:
      return state;
  }
}
```

to this

```JS
import { createAction } from "@reduxjs/toolkit";

// Action creators
export const addBug = createAction("BUG_ADDED");
export const resolveBug = createAction('RESOLVE_BUG');
export const removeBug = createAction("REMOVE_BUG");
// Reducer
let lastId = 0;
export default function reducer(state = [], action) {
  switch (action.type) {
    case addBug.type:
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case removeBug.type:
      return state.filter((bug) => bug.id === action.payload.id);
    case resolveBug.type:
      return state.map((bug) =>
        bug.id !== action.payload.id
          ? bug
          : {
              ...bug,
              resolved: true,
            }
      );
    default:
      return state;
  }
}

```

Remember when you call your actioncreators now you have to keep in mind that your argument will end up in the payload. If you wanted to change description or any other property then pass an object like so:

You have to change this

```JS
store.dispatch(addBug("New bug just came in."));
store.dispatch(addBug("bug2 came in."));
store.dispatch(addBug("Bug 3 came in"));
store.dispatch(resolveBug(1));
```

to this

```JS
store.dispatch(addBug({ description: "New bug just came in." }));
store.dispatch(addBug({ description: "bug2 came in." }));
store.dispatch(addBug({ description: "Bug 3 came in" }));
store.dispatch(resolveBug({ id: 1 }));
```

# Creating Reducers

You can use createReducer to create the reducer for you instead of the switch statement.
`CreateReducer` function takes 2 arguments.

1. The initial state
2. The map between Action Type to a function that performs the action. Exactly like the our reducer.

Using createReducer function we can simplify our code from

```JS
import { createAction } from "@reduxjs/toolkit";

// Action creators
export const addBug = createAction("BUG_ADDED");
export const resolveBug = createAction('RESOLVE_BUG');
export const removeBug = createAction("REMOVE_BUG");
// Reducer
let lastId = 0;
export default function reducer(state = [], action) {
  switch (action.type) {
    case addBug.type:
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case removeBug.type:
      return state.filter((bug) => bug.id === action.payload.id);
    case resolveBug.type:
      return state.map((bug) =>
        bug.id !== action.payload.id
          ? bug
          : {
              ...bug,
              resolved: true,
            }
      );
    default:
      return state;
  }
}

```

To this

```JS
import { createAction, createReducer } from "@reduxjs/toolkit";

// Action creators
export const addBug = createAction("BUG_ADDED");
export const resolveBug = createAction('RESOLVE_BUG');
export const removeBug = createAction("REMOVE_BUG");

// Reducer
let lastId = 0;

const reducer =  createReducer([], {
    [addBug.type]: (bugs, action)=> {
        bugs.push(
            {
                description: action.payload.description,
                id: ++lastId,
                resolved: false,
              }
        )
    },
    [resolveBug.type]: (bugs, action) => {
        const index = bugs.findIndex(bug => bug.id === action.payload.id);
        bugs[index].resolved = true;
    },
    [removeBug.type]:(bugs,action)=>{
        bugs.filter((bug) => bug.id === action.payload.id);
    }
});

export default reducer;
```

Create Reducer is using immer that enables us to modify the state as if it was a mutable object.

# Creating Slices

A Slice a combination of both Actio and a reducer.
The function `createSlice` will create Action Type, Action creator and the reducer.
The `createSlice` function needs one object with following properties:

1. Name
2. Initial state
3. Reducers
   You can now cut your code down to following

```JS
import { createSlice } from "@reduxjs/toolkit";

let lastId = 0;

const slice = createSlice({
  name: "bugs",
  initialState: [],
  reducers: {
    addBug: (bugs, action) => {
      bugs.push({
        description: action.payload.description,
        id: ++lastId,
        resolved: false,
      });
    },
    resolveBug: (bugs, action) => {
      const index = bugs.findIndex((bug) => bug.id === action.payload.id);
      bugs[index].resolved = true;
    },
    removeBug: (bugs, action) => {
      bugs.filter((bug) => bug.id === action.payload.id);
    },
  },
});

export const { addBug, resolveBug, removeBug } = slice.actions;
export default slice.reducer;
```

# Projects Reducer

```JS
import { createSlice } from "@reduxjs/toolkit";
let lastId = 0;

const slice = createSlice({
    name:"projects",
    initialState:[],
    reducers :{
        addProject:(projects,action)=>{
            projects.push({
                name: action.payload.name,
                id: ++lastId,
              });
        },
        removeProject:(projects, action)=>{
            projects.filter(project => project.id === action.payload.id);
        },
    }
});

export const {addProject, removeProject} = slice.actions;
export default slice.reducer;
```

# Combining reducers

```JS
import { configureStore } from "@reduxjs/toolkit";
import {combineReducers} from 'redux';
import projectReducer from "./projects";
import bugReducer from "./bugs";

const reducer = combineReducers({bugReducer, projectReducer});
const store = configureStore({ reducer });

export default store;

```
