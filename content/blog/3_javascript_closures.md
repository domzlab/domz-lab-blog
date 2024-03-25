---
title: JavaScript Closures
description: A post about JavaScript Closures
permalink: "/blog/javascript-closure/"
date: 2024-03-25
templateEngineOverride: md
tags:
  - JavaScript
  - TypeScript
  - Closures
---

### Introduction

Over the past few years, I have been using JavaScript on and off. I have also recently started learning more about TypeScript. I am currently going through the `30 days of JavaScript` challenge on leetcode, and in the first card, learned about JavaScript Closures. Thus, I thought it would be a good idea to jot down some of things that I learned from answering the questions in the Closures section of the `30 days of JavaScript` challenge.


### Lexical Scoping

From my understanding, lexical scoping simply refers to the ability of a nested function being able to have access to the variables defined in the outer function. 

Another way I like to think of it is that you have a function that returns another function. The function you return can reference the variables that are created within the outer function, the input parameters of the function. 

### Returning a single function

Creating a closure is relatively simple. In the example code below, createCounter is a function that takes in a value. Each tiem your call counter, it will increment the value of n.


```ts
const createCounter = function (n: number) {
    return function() {
        n++;
    }
}
```

The above can also be done by returning an arrow function.

```ts
const createCounter = function (n: number) {
    return () => n++;
}
```

You can also explicitly define the function, and it return that function.

```ts
const createCounter = function(n: number) {
    
    function counter(){
        return n++;
    }
    
    return counter;
}

```

### Returning multiple functions

To returning more than one function, you can do so in the following ways.

1. In your return block, define the different functions you want to return.


```ts
type Counter = {
    increment: () => number,
    decrement: () => number,
    reset: () => number,
}

function createCounter(init: number): Counter {
    const initVal = init;
    return {
        increment() {
            return ++init;
        },
        decrement(){
            return --init;
        },
        reset(){
            init = initVal;
            return init;
        }
    }
    
}; 
```

2. Create the different instances of the functions you want to return. Then return an object containing all those functions


```ts
type Counter = {
    increment: () => number,
    decrement: () => number,
    reset: () => number,
}

function createCounter(init: number): Counter {
    const initVal = init;
    
    function increment(){
        return ++init;
    }
    
    function decrement(){
            return --init;
    }
    
    function reset(){
            init = initVal;
            return init;
    }
    
    return { increment, decrement, reset }
};
```


### References:
[1] "Closures - JavaScript | MDN," developer.mozilla.org. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures