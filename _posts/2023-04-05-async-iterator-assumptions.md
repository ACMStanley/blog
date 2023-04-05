---
author: acmstanley
title: 'How to Think Async: Surprising Behaviours of Asynchronous Iterators and Generators in JavaScript'
layout: default_post
date: 2023-04-05 00:00:00 Z
---

During a recently finished project, I was ~~forced~~ guided through all things async in JavaScript by an excellent mentor. He even rewarded me with a [vim clutch](https://blog.scottlogic.com/2022/12/08/building-a-rusty-vim-clutch.html) for my hard-pressed asynchronous efforts. A lot of our time on said project involved a heavy focus on asynchronous iterators; a relatively young feature of JavaScript, introduced in ES2018. If you aren’t already familiar with async iterators; their older cousin: the iterator; or even their uncles: the generator and async generator; Sam has generously covered those in <a href="https://blog.scottlogic.com/2020/04/22/Async-Iterators-Across-Execution-Contexts.html">one of his posts</a>.

During the project, I commited the developer crime of using the 'A' word sparingly. The ’A’ word is a dangerous weapon in the programmers arsenal. We’ve all been guilty of a using the ‘A’ word at some point. Often times we use the ‘A’ word when we think no one is listening, but the word ‘assume’ always comes back to bite you. While working with async iterators, I made some subtle assumptions that I didn’t even realise I was making, that caused some bizzare and seemingly unexplainable behaviour. The purpose of this blog post is to confront these dodgy assumptions publicly, so they don't trip you up like they did me.

## Assumption on Order of Execution

In a plain old synchrounous iterator, the order of: `next()` being called, code being executed, and results being yielded, is trivial. In most cases, you can basically treat all 3 as one atomic step. However, in async iterators, this is not the case.

I initially assumed that yield statements acted like a block. I.e. code is executed greedliy until it reaches a yield statement, and the iterator would wait until `next()` is called, bef immediatly returning the result, and conntinuing until the next yield. In reality, when an async iterator encounters a yield statement, the code up to that point is not immediately executed. Instead, the iterator waits until `next()` is called, and then executes all code up to the next yield statement.

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

await gen.next().then(result => {
  console.log(result);
});

console.log("After first next");

await gen.next().then(result => {
  console.log(result);
});

console.log("After second next");

await gen.next().then(result => {
  console.log(result);
});

console.log("After third next");
~~~

In this example, we create an async generator function that logs a message when it starts, resumes, and completes. We then create an instance of the generator and call the `next()` method three times, logging messages before and after each call.

If my assumption on the order of execution was correct the ouput would be: 

~~~
Generator started
Generator created
{value: 1, done: false}
Generator resumed
After first next
{value: 2, done: false}
Generator completed
After second next
{value: undefined, done: true}
After third next
~~~

However the actual output of this code is:

~~~
Generator created
Generator started
{value: 1, done: false}
After first nextfinally
Generator resumed
{value: 2, done: false}
After second next
Generator completed
{value: undefined, done: true}
After third next
~~~

Proving that 

## Assumption on AsyncGenerator's 'return' Behaviour

According to the Mozilla JavaScript docs:

>"*The return() method of an async generator acts as if a return statement is inserted in the generator's body at the current suspended position, which finishes the generator and allows the generator to perform any cleanup tasks when combined with a `try...finally` block.*"

Below is a code example that demonstrates this feature:

~~~ javascript
async function* myGenerator() {
  try {
    yield 1;
    yield 2;
  } finally {
    console.log("Generator cleanup code executed");
  }
}

const gen = myGenerator();
await gen.next().then(result => console.log(result));
gen.return();
await gen.next().then(result => console.log(result));
~~~

After the first `next()` is invoked and `1` is yielded, `return()` is called. This executes the cleanup code in the `finally` block and closes finishes the iterator. The next time `next()` is called, since the iterator is finished, a 'done' result is returned rather than `2`.

For proof and clarity, this is the output of the example code block:
~~~
{value: 1, done: false}
Generator cleanup code executed
{value: undefined, done: true}
~~~

This is quite a simple example, as the logic of this generator is essentially synchronous, and the behaviour is actually identical to that of a synchrounous iterator. The complexity comes when we start adding asynchronous logic into the main body of the generator.

Consider the following example:

~~~ javascript
async function* myGenerator() {
  try {
    await new Promise((resolve) => setTimeout(resolve, 10000));
    yield 1;
    yield 2;
  } finally {
    console.log("Generator cleanup code executed");
  }
}

const gen = myGenerator();
gen.next();
gen.return().then((result) => console.log(result));
~~~

This was my incorrect assumption of what would happen:
1. `next()` is called.
2. Since `gen.next()` is not prepended with `await`, `gen.return()` is immediatly called.
3. The iterator immediatly finishes, and "*Generator cleanup code executed*" is printed to the console.

What actually happens is that after the first `next()` is called - and `return()` is immediatly called - is the iterator waits 10 seconds for the promise to resolve before the cleanup code is run and "*Generator cleanup code executed*" is printed. This might seem like only a trivial issue, but this can be a dangerous assumption to hold when we consider a real-life example:

~~~ javascript
async function* myStringTransformer() {
  try {
    /*open data pipeline*/
    while(true){
      const datum = await /*next value from data pipeline*/
      yield datum.toString();
    }
  } finally {
    /*Close pipeline*/
  }
}
~~~

The pseudocode above shows a generator that opens a data pipeline, and transforms each datum that comes through pipeline into a string. The `finally` block defines the cleanup, which closes the pipeline. The intended use is that once we are done with this transformer, we call `return()`, signalling it to close the pipeline. The happy path to this example is that data is coming through quickly and consistently; after calling `return()` the next time some data comes through, the pipeline will be closed. But what about in the case where no data comes for another hour? or no data ever comes through again? the pipeline will remain open indefinitely. Depending on the application, this could be a one way ticket to memory leak town; population: me.






