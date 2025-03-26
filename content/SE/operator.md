---
title: Operator
draft: false
tags:
  - js
---

In RxJS, **operators** [^1] are functions that let you manipulate, transform, filter, or combine observable data streams. They help you control how values emitted by an observable are processed before reaching subscribers.

## Pipeable Operators

**Pipeable Operators** is actually a operator that can be "*piped*" to [[observable.md|Observable]] using this syntax `observable.pipe(operator)` or `observable.pipe(operatorFactory())`. **Operators Factory** include, [filter(...)](https://rxjs.dev/api/operators/filter) and [mergeMap(...)](https://rxjs.dev/api/operators/mergeMap). When pipeable operators is being used it don't change the existing observable instance. Instead, they return a *new observable*, where its subscription logic is based on the original/first observable. It's used to transform data via the operators inside `.pipe()`.

> A Pipeable Operator is a function that takes an Observable as its input and returns another Observable. It is a pure operation: the previous Observable stays unmodified.

### Common Pipeable Operators

| Operator           | Description                                         | Example                                           |
| ------------------ | --------------------------------------------------- | ------------------------------------------------- |
| `map(fn)`          | Transforms emitted values                           | `map(x => x * 2)`                                 |
| `filter(fn)`       | Filters values based on a condition                 | `filter(x => x > 10)`                             |
| `take(n)`          | Takes only the first `n` values                     | `take(3)` → takes first 3 values                  |
| `skip(n)`          | Skips the first `n` values                          | `skip(2)` → ignores first 2 values                |
| `debounceTime(ms)` | Waits for a pause in emission                       | `debounceTime(500)` (waits 500ms before emitting) |
| `mergeMap(fn)`     | Flattens inner observables                          | Useful for HTTP requests                          |
| `switchMap(fn)`    | Cancels previous inner observable on a new emission | Used for autocomplete searches                    |

### Example of Pipeable Operators

```ts
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

of(1, 2, 3, 4, 5).pipe(
  filter(x => x % 2 === 0), // Keep only even numbers
  map(x => x * 10) // Multiply each by 10
).subscribe(console.log);

// Output: 20, 40
```

## Creation Operator

**Creation Operator** are functions that can be used to create Observable  with predefined behavior or by combining other Observables. It's used to create new observables from a function.

### Common Creation Operators

| Operator                                                                              | Description                                                      | Example                                          |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------ |
| [`of(...)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/of)                | Emits provided values one by one                                 | `of(1, 2, 3)` → emits `1, 2, 3` sequentially     |
| [`from(...)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/from)            | Converts arrays, promises, or iterables into observables         | `from([1, 2, 3])` behaves like `of(1, 2, 3)`     |
| [`interval(n)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/interval)      | Emits numbers (`0,1,2...`) every `n` milliseconds                | `interval(1000)` → emits `0,1,2...` every second |
| [`timer(n)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/timer)            | Emits a single value after `n` milliseconds                      | `timer(3000)` → emits `0` after 3 sec            |
| [`range(start, count)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/range) | Emits a sequence of numbers                                      | `range(1, 5)` → emits `1,2,3,4,5`                |
| [`empty()`](https://www.learnrxjs.io/learn-rxjs/operators/creation/empty)             | Emits nothing and completes                                      | `empty()` emits nothing, just completes          |
| [`throwError(err)`](https://www.learnrxjs.io/learn-rxjs/operators/creation/throw)     | Emits an error immediately                                       | `throwError('Oops')` → emits error               |
| [`never()`](https://rxjs.dev/api/index/const/NEVER)                                   | Emits nothing and **never completes** (Use for testing purposes) | `never()` → never emits anything                 |
|                                                                                       |                                                                  |                                                  |

### Example of Creation Operators

```ts
import { of, from } from 'rxjs';

of(10, 20, 30).subscribe(console.log);
// Output: 10, 20, 30

from([10, 20, 30]).subscribe(console.log);
// Output: 10, 20, 30 (same as `of`, but from an array)
```

## How to Handle Higher-Order Observables

Observables usually emit simple values like numbers or strings. But sometimes, an Observable emits **other Observables**—these are called **higher-order Observables**.

### Example

Imagine you have an Observable that emits URLs. You use `http.get(url)` to fetch the file, but since `http.get()` returns another Observable, you end up with an **Observable of Observables**:

```ts
const fileObservable = urlObservable.pipe(
  map((url) => http.get(url)) // Creates an Observable inside an Observable
);
```

Ways to handle **Higher-Order Observable** is by **flattening** them using flattening operators—converting an Observable of Observables into a single Observable.

### Flattening/Join Operators [^2]

| **Operator**                                                    | **Behavior**                                                                                          | **Best Use Case**                                                                                                                      | **Analogy**                                                                                        |
| --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **[`concatAll()`](https://rxjs.dev/api/operators/concatAll)**   | Waits for each inner Observable to **complete** before subscribing to the next one. Preserves order.  | When order matters, and you must process each Observable **one at a time** (e.g., making API calls sequentially).                      | A single cashier serving one customer at a time, ensuring each finishes before moving to the next. |
| **[`mergeAll()`](https://rxjs.dev/api/operators/mergeAll)**     | Subscribes to **all** inner Observables **at the same time**, emitting values as soon as they arrive. | When order doesn’t matter, and you need **parallel execution** (e.g., loading multiple images or API requests in parallel).            | Multiple cashiers serving multiple customers at once.                                              |
| **[`switchAll()`](https://rxjs.dev/api/operators/switchAll)**   | Subscribes to the latest inner Observable and **cancels the previous one** when a new one arrives.    | When you only care about the latest data and want to discard old ones (e.g., live search, where only the newest query result matters). | A single cashier who always serves the latest customer, ignoring previous ones.                    |
| **[`exhaustAll()`](https://rxjs.dev/api/operators/exhaustAll)** | Subscribes to the first inner Observable and **ignores new ones** until the current one completes.    | When you must **finish processing** one task before handling new ones (e.g., preventing duplicate form submissions).                   | A cashier who serves only one customer and ignores everyone else until done.                       |

#### Example Using `concatAll()`

```ts
const fileObservable = urlObservable.pipe(
  map((url) => http.get(url)), // Creates an Observable of Observables
  concatAll() // Flattens it into a single Observable
);
```

### Flattening + Mapping Operators

> Just as many array libraries combine [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) and [`flat()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) (or `flatten()`) into a single [`flatMap()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap), there are mapping equivalents of all the RxJS flattening operators [`concatMap()`](https://rxjs.dev/api/operators/concatMap), [`mergeMap()`](https://rxjs.dev/api/operators/mergeMap), [`switchMap()`](https://rxjs.dev/api/operators/switchMap), and [`exhaustMap()`](https://rxjs.dev/api/operators/exhaustMap). - RxJS

| **Operator**                                                | **Behavior**                                                                                                                               | **Best Use Case**                                                                                                                                        | **Analogy**                                                                   |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| [`concatMap()`](https://rxjs.dev/api/operators/concatMap)   | Maps each value to an inner Observable and processes them **one by one** in order, waiting for each to complete before moving to the next. | When order matters, and each task must finish before starting the next (e.g., queueing API calls, processing form submissions in sequence).              | A single cashier serving one customer at a time before moving to the next.    |
| [`mergeMap()`](https://rxjs.dev/api/operators/mergeMap)     | Maps each value to an inner Observable and processes them **in parallel**, emitting values as soon as they arrive.                         | When you need fast, concurrent execution and order doesn't matter (e.g., fetching multiple data sources, handling multiple requests at once).            | Multiple cashiers serving multiple customers simultaneously.                  |
| [`switchMap()`](https://rxjs.dev/api/operators/switchMap)   | Maps each value to an inner Observable but **cancels the previous one** when a new value arrives, only keeping the latest one.             | When only the latest request matters (e.g., live search autocomplete, canceling previous API calls when new ones arrive).                                | A single cashier who always serves the latest customer, ignoring older ones.  |
| [`exhaustMap()`](https://rxjs.dev/api/operators/exhaustMap) | Maps each value to an inner Observable but **ignores new ones** until the current one completes.                                           | When you must process one task fully before accepting new ones (e.g., preventing duplicate form submissions, ensuring only one login request at a time). | A cashier who serves only one customer and ignores all others until finished. |

> [!tip] Tips
>
> - **Mapping**: Transforming the value.
> - **Flattening**: Combining multiple observable into single observable.

#### Example Using `SwitchMap()`

```ts
const fileObservable = urlObservable.pipe(
  switchMap((url) => http.get(url)) // Fetches the latest URL, cancels previous requests
);
```

---

## 🔗 Sources

[^1]: [RxJS - RxJS Operators](https://rxjs.dev/guide/operators)
[^2]: [RxJS - Join Operators](https://rxjs.dev/guide/operators#join-operators)