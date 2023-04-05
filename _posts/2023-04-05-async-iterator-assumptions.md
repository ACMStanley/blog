---
author: acmstanley
title: 'How to Think Async: Surprising Behaviours of Asynchronous Iterators in JavaScript'
layout: default_post
date: 2023-04-05 00:00:00 Z
---

During a recently finished project, I was ~forced~ guided through all things async in JavaScript by an excellent mentor. He even rewarded me with a [vim clutch](https://blog.scottlogic.com/2022/12/08/building-a-rusty-vim-clutch.html) for my hard-pressed asynchronous efforts. A lot of our time on said project involved a heavy focus on asynchronous iterators; a relatively young feature of JavaScript, introduced in ES2018. If you aren’t already familiar with async iterators; their older cousin: the iterator; or even their uncles: the generator and async generator; Sam has generously covered those in <a href="https://blog.scottlogic.com/2020/04/22/Async-Iterators-Across-Execution-Contexts.html">one of his posts</a>.

During the project, I commited the developer crime of using the 'A' word sparingly. The ’A’ word is a dangerous weapon in the programmers arsenal. We’ve all been guilty of a using the ‘A’ word at some point. Often times we use the ‘A’ word when we think no one is listening, but the word ‘assume’ always comes back to bite you. While working with async iterators, I made some subtle assumptions that I didn’t even realise I was making, that caused some bizzare and seemingly unexplainable behaviour. The purpose of this blog post is to confront these dodgy assumptions publicly, so they don't trip you up like they did me.

## Assumption 1: Order of Execution

In a plain old synchrounous iterator, the order of: next() being called, code being executed, and results being yielded, is trivial. In most cases, you can basically treat all 3 as one atomic step. However, in async iterators, this is not the case.

I initially assumed that yield statements acted like a block. I.e. code is executed greedliy until it reaches a yield statement, and the iterator would wait until next() is called, bef immediatly returning the result, and conntinuing until the next yield. In reality, when an async iterator encounters a yield statement, the code up to that point is not immediately executed. Instead, the iterator waits until ‘next’ is called, and then executes all code up to the next yield statement.

Here is an example to illustrate this behaviour:

~~~ javascript
async function* myAsyncGenerator() {
  console.log("Generator started");
  yield 1;
  console.log("Generator resumed");
  yield 2;
  console.log("Generator completed");
}

const gen = myAsyncGenerator();

console.log("Generator created");

gen.next().then(result => {
  console.log(result); // { value: 1, done: false }
});

console.log("After first next");

gen.next().then(result => {
  console.log(result); // { value: 2, done: false }
});

console.log("After second next");

gen.next().then(result => {
  console.log(result); // { value: undefined, done: true }
});

console.log("After third next");
~~~

In this example, we create an async generator function that logs a message when it starts, resumes, and completes. We then create an instance of the generator and call the next method three times, logging messages before and after each call.

The output of this code is:

~~~
Generator created
Generator started
After first next
After second next
After third next
Generator resumed
Generator completed
~~~
