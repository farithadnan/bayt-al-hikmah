---
title: Observable
draft: false
tags:
  - js
---

**Observables** [^1] are collections of data, **streams of data**, or event sources that **emit values over time**, allowing you to **react to new or updated values asynchronously** by **subscribing** to them (**subscription**). In this context, data can come in **various forms** or **different data types**.

> [!IMPORTANT] Example in real life
Imagine **YouTube Live Streaming** 🎥
>
> - The streamer (Observable) **emits** data (video frames).
> - Viewers (Subscribers) **receive** the data in real time.
> - If a viewer **leaves** the stream (unsubscribe), they stop receiving new updates.

---

> [!summary] Reminder
>
> - **Pull Systems:** Consumer requests data (`function`, `iterator`).
> - **Push Systems:** Producer sends data (`Promise`, `Observable`).
> - **Observables are better than Promises** when you need **multiple values over time**.
> - **Observables are lazy**, just like functions. (Execute only when its being call)
> - **Subscribing to an Observable is like calling a function**, but it can return multiple values.
> - **Observables can be synchronous or asynchronous.**

---

## Pull vs. Push Systems

**Pull and Push** are two ways in which data flows from a **Producer** (who creates data) to a **Consumer** (who receives data).

| System   | Producer (Data Source)                      | Consumer (Data Receiver)               |
| -------- | ------------------------------------------- | -------------------------------------- |
| **Pull** | **Passive** – Produces data only when asked | **Active** – Requests data when needed |
| **Push** | **Active** – Produces data automatically    | **Passive** – Reacts when data arrives |

### Pull

The **Consumer** asks for data when needed. The **Producer** only provides it when requested.

#### 🧑🏽‍💻 Examples of Pull Systems

1. **Functions**

    - A function only runs when you call it.

    ```js
    function getData() {
        return 42; // Only returns a value when called
    }
    console.log(getData()); // 42
    ```

2. **Iterators & Generators** (`function*`)

    - You manually call `.next()` to "pull" the next value.

    ```js
    function* generator() {
        yield 1;
        yield 2;
        yield 3;
    }
    const iter = generator();
    console.log(iter.next().value); // 1
    console.log(iter.next().value); // 2
    
    ```

### Push

The **Producer** sends data whenever it wants, and the **Consumer** reacts to it automatically.

#### 🧑🏽‍💻 Examples of Push Systems

1. **Promises**

    - A **Promise** resolves and sends data when it's ready, without the consumer asking.

    ```js
    const promise = new Promise((resolve) => {
        setTimeout(() => resolve(42), 1000);
    });
    promise.then((value) => console.log(value)); // 42 (after 1 second)
    
    ```

2. **Observables**

    - **Observables are Push-based**, meaning they emit multiple values over time, and consumers subscribe to them.

    ```js
    import { Observable } from 'rxjs';
    
    const observable = new Observable(subscriber => {
        subscriber.next(1);
        subscriber.next(2);
        setTimeout(() => subscriber.next(3), 1000);
    });
    
    observable.subscribe(value => console.log(value));
    
    ```

    - **Output**

    ```md
    1
    2
    (after 1 second) 3
    ```

### Observables vs Functions & Promises

Observables are often misunderstood, so let's compare them:

|Feature|**Function**|**Promise**|**Observable**|
|---|---|---|---|
|**Returns**|Single value|Single value (async)|Multiple values (sync or async)|
|**Lazy Execution**|✅ Yes|✅ Yes|✅ Yes|
|**Can be canceled**|❌ No|❌ No|✅ Yes (unsubscribe)|
|**Push/Pull?**|Pull|Push|Push|
|**Example**|`getData()`|`fetchData().then()`|`observable.subscribe()`|

---

## Observable as Generalized Function

Observables are **similar to functions**, but instead of returning a single value, they return **a stream of values over time**.

🧑🏽‍💻**Function Example**

```js
function foo() {
  console.log("Hello");
  return 42;
}

console.log(foo());
console.log(foo());
```

✨**Function Output**

```md
Hello
42
Hello
42
```

🧑🏽‍💻**Same behavior but using observable**

```js
import { Observable } from 'rxjs';

const foo = new Observable(subscriber => {
  console.log("Hello");
  subscriber.next(42);
});

foo.subscribe(value => console.log(value));
foo.subscribe(value => console.log(value));
```

✨**Output**

```md
Hello
42
Hello
42
```

- Just like functions, an Observable **does nothing until subscribed to** (lazy execution).  
- Each `subscribe()` call **triggers a new execution**, like calling a function multiple times.

### Observables Can Emit Multiple Values

Unlike functions, Observables **can push multiple values over time**.

 🧑🏽‍💻 **Example:**

```js
import { Observable } from 'rxjs';

const foo = new Observable(subscriber => {
  console.log("Hello");
  subscriber.next(42);
  subscriber.next(100);
  subscriber.next(200);
});

console.log("before");
foo.subscribe(value => console.log(value));
console.log("after");

```

**Output:**

```md
before
Hello
42
100
200
after
```

- The function version **cannot** return multiple values, but an Observable **can**.

#### Observables Can Be Asynchronous

Observables can **emit values asynchronously**, like Promises.

**Example:**

```js
import { Observable } from 'rxjs';

const foo = new Observable(subscriber => {
  console.log("Hello");
  subscriber.next(42);
  subscriber.next(100);
  setTimeout(() => {
    subscriber.next(300); // happens asynchronously
  }, 1000);
});

console.log("before");
foo.subscribe(value => console.log(value));
console.log("after");
```

**Output:**

```md
before
Hello
42
100
after
(1 second later) 300
```

- The **first values are synchronous**, but **300 is emitted asynchronously**.

---

## Key Parts of an Observables

Observables have **four key parts**:

1. **Creation** – You create an Observable that will emit data.
2. **Subscription** – Someone listens to (or watches) the Observable.
3. **Execution** – The Observable starts emitting values.
4. **Unsubscription** – Stop listening to the Observable.

Below showcasing code examples that uses observable:

### 🧑🏽‍💻Example 1: Basic Observable

```js
import { Observable } from 'rxjs';

// 🔹 Step 1: Create an Observable (CREATION)
const myObservable = new Observable(observer => {
  observer.next('Hello');
  observer.next('World');
  observer.complete(); // 🔹 Execution ends
});

// 🔹 Step 2: Subscribe to the Observable (SUBSCRIPTION)
const subscription = myObservable.subscribe({
  next: value => console.log(value), // 🔹 Execution: Receive emitted values
  complete: () => console.log('Observable completed'), // 🔹 Execution: No more data
});

// 🔹 Step 3: Unsubscribe (UNSUBSCRIPTION) - Not necessary here since it auto-completes

```

📝 **Explanation:**

- **Creation** → `new Observable(observer => {...})` defines an Observable that emits values (`Hello` and `World`).
- **Subscription** → `subscribe({...})` starts listening for emitted values.
- **Execution** → The Observable emits **two values** (`Hello`, `World`) before calling `complete()`.
- **Unsubscription** → No need to manually unsubscribe here because the Observable **completes automatically**.

### 🧑🏽‍💻 Example 2: Observable with Asynchronous Execution & Unsubscription

```js
import { Observable } from 'rxjs';

// 🔹 Step 1: Create an Observable (CREATION)
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(9);
  subscriber.next(9);

  // 🔹 Emit a value asynchronously after 1 second
  const timeoutId = setTimeout(() => {
    subscriber.next(7);
    subscriber.complete(); // 🔹 Execution ends
  }, 1000);

  // 🔹 Step 4: Unsubscribe logic (UNSUBSCRIPTION)
  return () => {
    clearTimeout(timeoutId); // Stop async execution if unsubscribed early
    console.log('Unsubscribed: Cleanup done');
  };
});

console.log('Before subscribing');

// 🔹 Step 2: Subscribe to the Observable (SUBSCRIPTION)
const subscription = observable.subscribe({
  next: x => console.log('Received:', x), // 🔹 Execution: Process each value
  error: err => console.error('Error occurred:', err),
  complete: () => console.log('Observable completed'),
});

console.log('After subscribing');

// 🔹 Step 3: Unsubscribe after 500ms (before the last value is emitted)
setTimeout(() => {
  subscription.unsubscribe();
}, 500);

```

✨**Output**

```md
Before subscribing
Received: 1
Received: 9
Received: 9
After subscribing
Unsubscribed: Cleanup done
```

📝 **Explanation:**

- **Creation** → The Observable emits **three values** synchronously (`1, 9, 9`) and **one value** (`7`) after a delay.
- **Subscription** → The subscriber listens to values via `next()`.
- **Execution** → Values are emitted immediately, and the last value is scheduled after 1 second.
- **Unsubscription** → The subscriber **unsubscribes after 500ms**, **before the last value is emitted**, stopping execution.

---

## 🔗 Sources

[^1]: [RxJS - Observable](https://rxjs.dev/guide/observable)
