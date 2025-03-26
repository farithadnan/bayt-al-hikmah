---
title: RxJS
draft: false
tags:
  - js
---

![[rxjs_logo.png|center|100x100]]

**RxJS**[^1]  (Reactive Extensions for JavaScript) is a library for **reactive programming** that enables handling **[[Asynchronous|asynchronous]] data streams (observables)** and **event-based actions**. It is widely used in frameworks like **Angular** but can be integrated into any JavaScript application. Essentially, it provides a powerful way to work with asynchronous events and data flows in a more **manageable and composable** manner.

📌 Essential concepts in RxJS:

1. **[[observable.md|Observable]]**: are collection of data or event sources that emits values over time, allowing you to react to new or updated values asynchronously using by subscribing (**subscription**) to them.
2. **[[observer.md|Observer]]**: Is a set or **collection of callbacks** which is used to listen the values delivered by the **observables**.
3. **[[subscription.md|Subscription]]**: responsible for **listening & respond to every update** happening within the **observables**.
4. **[[operator.md|Operator]]**: are functions like array methods (`map`, `filter` and etc), to deals with **observables**.
5. **[[Subject]]**: the only way of multicasting a value or event to multiple **observers**
6. **[[Schedulers]]**: manage the timing and order of tasks, letting us control when actions run on various timing when using mechanism like `setTimeout`, `requestAnimationFrame`, or others.

RxJS is ideal for applications that handle asynchronous data streams, such as UI elements reacting to user inputs over time.
