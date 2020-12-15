# Introduction

It is always helpful to understand how a certain tool is built. Now we will build redux from scratch.

# Redux Store

The redux store has a `state` that internal and you cannot modify it from outside. You can only access the state by calling `getState` method.

```JS
function createStore(){
   let state;

   function getState(){
       return state;
   }

   return {
       getState
   };
}
```

# Dispatching actions

The dispatch funtion is public. We get the reducer as an arugment when `createStore` is called.

```JS
export function createStore(reducer){
    let state;

    function getState(){
        return state;
    }

    function dispatch(action){
      state = reducer(state, action);
    }

    return {
        dispatch,
        getState
    };
}
```

# Subscribing to the Store

We need to notify our subscribes that the states has changed.
These could be components in the ui that needs to know if something changed, such that they can update.
We create an array of listener that we get from each time subscribe method is executed.
Then on dipatch we actually go through all of our listeners and execute the method reference.

```JS
export function createStore(reducer){
    let state;
    let listeners = [];

    function getState(){
        return state;
    }

    function subscribe(listener) {
        listeners.push(listener);
    }

    function dispatch(action){
      state = reducer(state, action);
      for (let index = 0; index < listeners.length; index++) {
          listeners[index]();
      }
    }

    return {
        subscribe,
        dispatch,
        getState
    };
}
```
