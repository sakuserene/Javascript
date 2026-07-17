---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 8 - Section 4 (Completion)
title: Fetch API - Part 8 - Async/Await Patterns and Concurrent Requests
topic: Async/Await Patterns and Concurrent Requests
---

# Fetch API --- Part 8 (Section 4)

## Best Practices

-   Use `Promise.all()` only for independent requests.
-   Use `Promise.allSettled()` when partial failures are acceptable.
-   Handle every rejected promise.
-   Cache reusable responses when appropriate.
-   Avoid unnecessary duplicate requests.

## Knowledge Check

1.  When should you use sequential requests?
2.  What happens if one promise rejects in `Promise.all()`?
3.  When is `Promise.allSettled()` a better choice?
4.  How does `Promise.any()` differ from `Promise.race()`?
5.  Why should concurrent requests be limited in some situations?

## Exercises

### Beginner

1.  Fetch users and posts concurrently.
2.  Display both results.
3.  Compare sequential and concurrent execution times.

### Intermediate

1.  Use `Promise.allSettled()` to process mixed results.
2.  Build a timeout using `Promise.race()`.
3.  Retrieve data from multiple mirrors using `Promise.any()`.

### Advanced

1.  Build a dashboard that loads several independent widgets
    concurrently.
2.  Create a reusable concurrent request helper.

## Interview Questions

**Q:** Why is `Promise.all()` faster than sequential requests?

**A:** Independent requests begin at the same time, reducing the total
waiting time to approximately the duration of the slowest request.

**Q:** When should `Promise.allSettled()` be preferred?

**A:** When the application should continue processing successful
results even if some requests fail.

## Debugging Checklist

-   Verify each request independently.
-   Inspect rejected promises.
-   Use the browser Network tab to compare request timing.
-   Check for unnecessary duplicate requests.

## Part 8 Summary

You learned:

-   Sequential versus concurrent requests.
-   `Promise.all()`, `Promise.allSettled()`, `Promise.race()`, and
    `Promise.any()`.
-   Production-ready concurrency patterns.
-   Performance optimization and common pitfalls.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  Fetch API -- Part 7            Fetch API -- Part 8      Fetch API --
                                                          Part 9: Building
                                                          a Reusable API
                                                          Client

  ------------------------------------------------------------------------
