---
title: Observer
draft: false
tags:
  - js
---
An **Observer** [^1] is an entity that receives values from an **Observable**. It consists of a set of callback functions, each handling a different type of notification the Observable can send:

- `next(value)`: Processes the emitted value.
- `error(err)`: Handles any error that occurs during execution.
- `complete()`: Executes when the Observable has finished sending data.

Here’s an example of how an **Observer** is structured:

```js
const observer = {
  next: value => console.log('Received value:', value),
  error: err => console.error('Error encountered:', err),
  complete: () => console.log('Observable has completed'),
};
```

This observer can then be used to subscribe to an Observable, listening for values, errors, or completion signals.

---
# Sources

[^1]: [RxJS - Observer](https://rxjs.dev/guide/observer)
