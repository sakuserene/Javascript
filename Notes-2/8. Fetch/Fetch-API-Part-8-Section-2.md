---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 8 - Section 2
title: Fetch API - Part 8 - Async/Await Patterns and Concurrent Requests
topic: Async/Await Patterns and Concurrent Requests
---

# Fetch API --- Part 8 (Section 2)

## `Promise.allSettled()`

`Promise.allSettled()` waits for **every** promise to finish, regardless
of whether it succeeds or fails.

``` javascript
const results = await Promise.allSettled([
  fetch("/api/users"),
  fetch("/api/posts"),
  fetch("/api/comments")
]);

console.log(results);
```

Each result contains:

-   `status` (`fulfilled` or `rejected`)
-   `value` (for fulfilled promises)
-   `reason` (for rejected promises)

Use it when you want to process every result without stopping on the
first failure.

## `Promise.race()`

`Promise.race()` settles as soon as the **first** promise settles.

``` javascript
const first = await Promise.race([
  fetch("/api/cache"),
  fetch("/api/database")
]);
```

Typical uses:

-   Implementing request timeouts
-   Choosing the fastest response

## `Promise.any()`

`Promise.any()` resolves with the **first successfully fulfilled**
promise.

``` javascript
const response = await Promise.any([
  fetch("/api/server-a"),
  fetch("/api/server-b"),
  fetch("/api/server-c")
]);
```

Unlike `Promise.race()`, rejected promises are ignored until all
promises fail.

## Choosing the Right Utility

  Utility                  Best Use Case
  ------------------------ ------------------------------
  `Promise.all()`          All requests must succeed
  `Promise.allSettled()`   Collect every result
  `Promise.race()`         First settled result wins
  `Promise.any()`          First successful result wins

## Debugging Tips

-   Inspect individual promise results.
-   Handle rejected promises explicitly.
-   Test partial failures in browser DevTools.

## Continue

Next file:

**Fetch-API-Part-8-Section-3.md**
