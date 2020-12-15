# Functional programming

- Redux is built on top of functional programming

# What is functional programming?

- Functional programming is a programming paradigm
- Functional programming is about decomping the problem into small and re-usable functions that takes one input and produces one output.

## Benefits

- More concise
- easier to debug
- easier to test
- more scalable

## Languages

- Clojure
- Haskell
- Javacript (Multi paradigm not sorely functional)

# Functions as First-class citizens

In Javascript functions are first-class citizens. This means that we can treat them like any variable.

## Functions

We can do following with a fucntion

- Assign to a variable
- Pass as an argument
- Return from them other functions

### Assign to a variable

**Example**

```JS
function sayHello(){
    return "Hello World";
}

let fn = sayHello

// now calling fn() is exactly the same as calling sayHello()
```

` sayHello` is not called here. Just passed as reference.

### Pass as an argument

**Example**

```JS
function sayHello(){
    return "Hello World";
}

function greet(fnMessage){
    console.log(fnMessage());
}

greet(sayHello);
```

`greet` takes function as argument and calls it.
When calling greet we just pass the function reference to `sayHello` as argument.

### Return from them other functions

**Example**

```JS
function sayHello(){
    return function(){
        return "hello World";
    };
}

let fn = sayHello();

let message = fn();
```

`fn` is a reference to the inner function inside `sayHello`
when executing `fn` you get the string value that the inner functions return namely `"Hello World"`

# Higher-Order Functions

An higher order fucntion is a function that takes a function as an argument or returns it.
**Example**

```JS
// Returns a function. sayHello is a higher order function.
function sayHello(){
    return function(){
        return "hello World";
    };
}

// Takes a function as argument. greet is a higher order function.
function greet(fnMessage){
    console.log(fnMessage());
}
```

**Example of higer order function in Javascript**

```JS
let numbers = [1,2,3];
// number => number * 2 is an arrow function. map is a higher order function.
numbers.map(number => number * 2);
```

Map is a higher order function.

# Function composition

**Example**

```JS
// This is the non-functional approach
let input = "  Javascript  ";
let output = "<div>" + input.trim() + "</div>"

// Functional approach
const trim = str => str.trim();
const wrapInDiv = str => `<div>${str}</div>`;
const toLowerCase = str => str.toLowerCase();
// This is called functional composition.
const result = wrapInDiv(toLowerCase(trim(input)));
```

# Composing and piping

## Lodash

Utility library for JavaScript
to install it go to terminal type `npm i lodash`

```JS
import {compose, pipe } from 'lodash/fp';
```

all utility functions for functional programming are found in that package.

```JS
import {compose, pipe } from 'lodash/fp';

// This is the non-functional approach
let input = "  Javascript  ";
let output = "<div>" + input.trim() + "</div>"

// Functional approach

// Without lodash.
const trim = str => str.trim();
const wrapInDiv = str => `<div>${str}</div>`;
const toLowerCase = str => str.toLowerCase();
const result = wrapInDiv(toLowerCase(trim(input)));

// With Lodash
const transform = compose(wrapInDiv, toLowerCase, trim); // The order of execution is trim, toLowerCase, Wrap. But the reading the opposite
// But you can use the pipe function for easier reading
const transform = pipe(trim, toLowerCase, wrapInDiv); // The order of execution is the same as you read them.
//Tranform is af function that does the same as
const result = transform(input);
// As this one. But is more readable without too many paranthesis.
const result = wrapInDiv(toLowerCase(trim(input)));
```

# Currying

## The problem

```JS
import {compose, pipe } from 'lodash/fp';

const trim = str => str.trim();
const wrap = (type, str) => `<${type}>${str}</${type}>`;
const toLowerCase = str => str.toLowerCase();

const transform = pipe(trim, toLowerCase, wrap);
const result = transform(input);
```

You want to change `wrapInDiv` into a more re-usable function so you add another paramenter. But inside `pipe` you can see wrap do not get two arguments.
if you pass an argument like this `pipe(trim, toLowerCase, wrap("div"))` the function `pipe` will break because it cannot build a pipeline with bunch of functions then a string. Since the execution value of `wrap`is a string.
This is what currying solves.

## Solution

Currying is the principle of reducing parameters.

**Example**

```JS
// This function needs two paramters.
function add(a, b){
    return a + b;
}
// call add function without currying
let result = add(1,5) // result = 6

// After currying this would be as follows.
function add(a){
    return function(b){
        return a + b;
    }
}

// Call add function with currying
let result = add(1)(5) // result = 6

// You can type the currying as arrow functions
const add = a => b => a + b;

let result = add(1,5) // result = 6
```

The solution for the previous problem

```JS
import {compose, pipe } from 'lodash/fp';

const trim = str => str.trim();
const wrap = type => str => `<${type}>${str}</${type}>`;
const toLowerCase = str => str.toLowerCase();

const transform = pipe(trim, toLowerCase, wrap("div"));
const result = transform(input);
console.log(result);
```

`wrap("div")` does not return a string but a function.

# Pure Functions

A Function is pure when for each input it gives exactly the same result.
**Example**

```JS
// Pure function
function myFunction(number){
    return number * 2
}

//Not so pure function
function myUnPureFunction(number){
    return number * Math.Random();
}
```

`myUnPureFunction` will not return the same value for given input.

## Pure functions

- No Random values
- No current date/time
- No global state (DOM, files, db etc)
- No mutation of parameters

## Benefits

- Self-documenting
- Easily testable
- Concurrency
- Cacheable

# Immutability

Once created object you cannot change it.
If you create an object you need to take a copy then change the copy.
In pure functional programming langauge you cannot mutate data. In javascript you can. this javascript is not a functional programming language.

## Why immutability

- Predictability
- Fast change detection
- Concurrency

# Updating objects

This is an example of how to work with objects with immutability.

```JS
const person = {
    name: "John",
    address : {
        country:"USA",
        city:"San francico"
    }
};
const updated  = {
    ...person,
    address: {
        ...person.address,
        city:"New York"
    },
    name: "bob",
    age:30
}
console.log(updated);
```

When working with immutability we need to make a copy of the objects we want to modify.

# Updating Arrays

**Example**

```JS
const numbers = [1,2,3];

//adding
const index = numbers.indexOf(2);
const added = [...numbers.slice(0,index), 4, ...numbers.slice(index)]

console.log(added);
```
