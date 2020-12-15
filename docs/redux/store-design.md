# Introduction

We will take a closer look at designin a redux store.

# Redux state vs Local state

There are two main approach to Redux

1.  Store global state only
2.  store all state.

Store global state is easy to implement.

Store all state has following benefits.

- Unified data access
- cacheability
- easier debugging
- more testable code

We dont want form state in the store
for following reasons

- Temporary values
- Too many dispatches
- Harder debugging

The more we state we put in the store, the more we can get out of redux.

# Structuring a Redux Store

If you need fast lookups use objects.
if you need ordered data use arrays.
In an application where you have several slices. Put the slices under entities

```json
{
  //The store
  "entities": {
    //Entity
    "bugs": [], // slice
    "projects": [], // slice
    "tags": [] // slice
  }
}
```

# Combining Reducers

You can combine reducers using CombineReducer function from redux

```JS
import { combineReducers } from 'redux'

const rootReducer = combineReducers({
    bugs:bugsReducer,
    projects:projectsReducer
})
```

# Normalization

Normalization of data means getting rid of duplicates and use refernces. Like ids.
you can use `normalizr` library.

# Selectors

Selectors are functions that handles certain logic "like a query" for a slice.
You can write selectors by hand but they will get executed every time. A better way is to use reselect.

# Reselect

Reselect is a library that helps you create selectors.
to install it you do this

`npm i reselect`

The reselect function looks like this :

```JS
export const getUnresolvedBugs = createSelector(
  (state) => state.entities.bugs,
  (state) => state.entities.projects,
  (bugs, projects) => bugs.filter((bug) => !bug.resolved)
);
```

The first arguments are your selectors ( what data you want to select from the state )
last argument is your result function.

# Query Selector with 2 arguments

```JS
export const getBugByUserId = userId => createSelector(
  (state) => state.entities.bugs,
  (bugs) =>bugs.filter(bug => bug.userId === userId)
);
```
