---
title: Subscription
draft: false
tags:
  - js
---

In [[rxjs.md|RxJS]], a **Subscription** represents the execution of an [[observable.md|Observable]]. Think of it as a contract between the Observable and the [[observer.md|Observer]]: by subscribing, the Observer starts receiving data emitted by the Observable.

## Key Points

- **Creating a Subscription:** When you call the `.subscribe()` method on an Observable, it begins emitting data, and a Subscription object is returned. [^1]

    ```js
    const subscription = myObservable.subscribe(value => console.log(value));
    ```

- **Unsubscribing:** To stop receiving data and clean up resources, invoke the `.unsubscribe()` method on the Subscription. This is crucial for preventing memory leaks, especially with Observables that emit data over time (like user interactions or intervals). [^2]

    ```js
    subscription.unsubscribe();
    ```

- **Managing Multiple Subscriptions:** Subscriptions can be combined using the `add()` method, allowing you to unsubscribe from multiple Observables simultaneously. [^3].

    ````js
    const sub1 = obs1.subscribe();
    const sub2 = obs2.subscribe();
    sub1.add(sub2);
    // Later
    sub1.unsubscribe(); // Unsubscribes from both obs1 and obs2
    ````

## Best Practices

- **Automatic Unsubscription:** For Observables that complete after emitting data (like HTTP requests), manual unsubscription isn't necessary. However, for long-lived Observables, ensure you unsubscribe appropriately to avoid unintended behavior.
- **Angular Integration:** In Angular, managing subscriptions is vital. Components should unsubscribe from Observables during the `ngOnDestroy` lifecycle hook to prevent memory leaks. Alternatively, Angular's `async` pipe can handle subscription management automatically in templates.

---

## 🔗 Sources

[^1]: [RxJS - Subscription](https://rxjs.dev/guide/subscription?utm_source=chatgpt.com)
[^2]: [Best Practices for Managing RxJS Subscriptions - This Dot Labs](https://www.thisdot.co/blog/best-practices-for-managing-rxjs-subscriptions?utm_source=chatgpt.com)
[^3]: [RxJS - Observer and Subscription - DEV Community](https://dev.to/this-is-learning/rxjs-observer-and-subscription-5cg3?utm_source=chatgpt.com)
