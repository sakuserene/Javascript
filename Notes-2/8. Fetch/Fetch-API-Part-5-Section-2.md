---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 5 - Section 2
title: Fetch API - Part 5 - Error Handling and Robust Fetch Patterns
topic: Error Handling and Robust Fetch Patterns
---

# Fetch API --- Part 5 (Section 2)

## Using `try...catch`

With `async`/`await`, `try...catch` provides a clean way to handle
errors.

``` javascript
async function loadUsers() {
  try {
    const response = await fetch("/api/users");

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    const users = await response.json();
    console.log(users);
  } catch (error) {
    console.error("Request failed:", error.message);
  }
}
```

## Request Timeouts with `AbortController`

Some requests may take too long. You can cancel them.

``` javascript
const controller = new AbortController();

setTimeout(() => controller.abort(), 5000);

fetch("/api/users", {
  signal: controller.signal
})
.catch(error => {
  if (error.name === "AbortError") {
    console.log("Request timed out");
  }
});
```

## Cancelling In-Flight Requests

``` javascript
const controller = new AbortController();

fetch("/api/search?q=phone", {
  signal: controller.signal
});

// Later...
controller.abort();
```

Useful for search boxes where a new request replaces an old one.

## Handling Slow Networks

-   Show a loading indicator.
-   Disable duplicate submit buttons.
-   Allow users to retry failed requests.
-   Provide clear error messages.

## Debugging Tips

-   Test offline mode in browser DevTools.
-   Simulate slow networks.
-   Log `error.name` and `error.message`.
-   Verify timeout values.

## Continue

Next file:

**Fetch-API-Part-5-Section-3.md**
