# Introduction

Now that we know fundamentals of functional programming we are ready to learn redux.

# Redux Architecture

In Redux we store our application state inside a single JavaScript Object.
An `Action` can be seen as an event. It will Dispatch to the `Store` The store then talks to the `Reducer`. The `Reducer` actually is responsible for updating state within the store. The store is immutable so state needs to be copied. The `Reducer` can be seen as a event handler.

# Redux application building

## Steps

1. Design the store
   - Decide what you want to keep in the store
1. Define the actions
   - What are actions that the user can perform
1. Create a reducer
   - Create several reducers. The reducer takes an action and returns updated state.
1. Set up the store
   - Setup the store based on your reducer.

# Designing the Store

The first step is to design store.<br>
What type of information do we want to store?<br>
For bug tracking application we use following structure

```JS
{
  bugs: [
    {
      id: 1,
      description: "",
      resolved: false,
    },
  ];
  currentUser: {
  }
}
```

# Defining the Actions

The next time is to decide what actions the user can perform.
For this bug tracking application the user can do followings.

- Add a bug
- Mark a bug as resolved
- Delete a bug

In a real world application we could have alot more actions.
An action is just a javascript object that described what just happened
The add bug action:
Type property is mandatory.

```JS
{
  type: "ADD_BUG",
  payload: {
    id:1,
    description: ""
  }
};
```

# Creating a Reducer

```JS
let lastId = 0;
function reducer(state = [], action) {
  switch (action.type) {
    case "ADD_BUG":
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case "REMOVE_BUG":
      return state.filter((bug) => bug.id !== action.payload.id);
    default:
      return state;
  }
}
```

# Creating the Store

To create store you need to import you reducer and createStore method from redux.
`createStore` is a high order function and that takes the `reducer` function as an argument.

```JS
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer);

export default store;
```

# Dispatching Actions

In order to dispatch the action all we need is to call `dispatch` on our store.

```JS
store.dispatch({
  type:"ADD_BUG",
  payload:{
    description:"This action will add the bug"
  }
});
```

The dispatch method will call `Reducer` with following arguments
`store.getState()` and `action`that we pass.
And the return value of the reducer will be the new state.

The entire flow is as follows.
You dispatch the store with an Action. The store is bound to a reducer on creation time. The dispatch method will invoke the Reducer with current store state and the action that you give in. The reducer will return a copy of the modified state. The dispatch will use the return value to set as the new state inside the store.

You can use the subscribe method on the store to pass a listener function which is executed whenever the state changes.

# Action Types

In order to avoid the copy paste of the strings for action type. Create a seperate file called `actionTypes.js`

```JS
export const ADD_BUG = "BUG_ADD";
export const REMOVE_BUG = "REMOVE_BUG";
```

import it as follows

```JS
import * as actions from './actionTypes';

Usage
actions.ADD_BUG
```

The reducer after the string const update:

```JS
import * as actions from './actionTypes';
let lastId = 0;
export default function reducer(state = [], action) {
  switch (action.type) {
    case actions.ADD_BUG:
      return [
        ...state,
        {
          description: action.payload.description,
          id: ++lastId,
          resolved: false,
        },
      ];
    case actions.REMOVE_BUG:

      return state.filter((bug) => bug.id === action.payload.id);
    default:
      return state;
  }
}
```

# Action Creators

An action creator is a function that creates the action for us. This way we can use it multiple places.

create a file called `actions.js` the name doesnt matter
a creator will look like this:

```JS
import * as actions from './actionTypes';

export function AddBug(description) {
  return {
    type: actions.ADD_BUG,
    payload: {
      description: description,
    },
  };
}
```

The dispatch on the store will look like this

```JS
import store from './store';
import { bugAdd, AddBug} from './actions';

store.dispatch(AddBug("A Nice description."));
```

The action creator using arrow functions looks like this:

```JS
const addBug = (description) => ({
  type: actions.ADD_BUG,
  payload: {
    description: description,
  },
});
```
