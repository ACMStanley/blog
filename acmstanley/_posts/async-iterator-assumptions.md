---
author: acmstanley
title: How to Think Async: 3 (not so) Surprising Behaviours of Asynchronous Iterators in JavaScript
layout: default_post
---

During a recently finished project, I was ~forced~ guided through all things async in JavaScript by an excellent mentor. He even rewarded me with a [vim clutch](https://blog.scottlogic.com/2022/12/08/building-a-rusty-vim-clutch.html) for my hard-pressed asynchronous efforts. A lot of our time on said project involved a heavy focus on asynchronous iterators; a relatively young feature of JavaScript, introduced in ES2018. If you aren’t already familiar with async iterators; their cousin: the iterator; or even their uncles: the generator and async generator; Sam has kindly covered those in <a href="https://blog.scottlogic.com/2020/04/22/Async-Iterators-Across-Execution-Contexts.html">one of his posts</a>.

The ’A’ word is a dangerous weapon in the programmers arsenal. We’ve all been guilty of a using the ‘A’ word at some point. Often times we use the ‘A’ word when we think no one is listening, but the word ‘assume’ always comes back to bite you. While working with async iterators, we had made some subtle assumptions that we didn’t even realise, and these went undetected for quite a while. The purpose of this blog post is to outline these incorrect assumptions, so you can avoid making the same mistakes we did, and our efforts need not be in vain.

## Assumption 1: Order of Execution 

The first assumption that we made is that: when an async iterator encounters a yield statement, the code up to that point will be executed, and then the iterator will wait until the next method is called before continuing. In reality, when an async iterator encounters a yield statement, the code up to that point is not immediately executed. Instead, the iterator waits until ‘next’ is called, and then executes all code up to the next yield statement.

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


