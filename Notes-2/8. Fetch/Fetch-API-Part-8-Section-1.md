---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 8 - Section 1
title: Fetch API - Part 8 - Async/Await Patterns and Concurrent Requests
topic: Async/Await Patterns and Concurrent Requests
---

# Fetch API --- Part 8 (Section 1)

## Introduction

Modern JavaScript applications often make multiple API requests.

Using `async`/`await` produces readable asynchronous code, while Promise
utilities help coordinate multiple requests efficiently.

## Sequential Requests

Sequential requests wait for each previous request to finish.

``` javascript
const users = await fetch("/api/users").then(r => r.json());
const posts = await fetch("/api/posts").then(r => r.json());
```

Execution:

``` text
Request Users
     ↓
Response Users
     ↓
Request Posts
     ↓
Response Posts
```

## Concurrent Requests

Independent requests can run at the same time.

``` javascript
const [users, posts] = await Promise.all([
  fetch("/api/users").then(r => r.json()),
  fetch("/api/posts").then(r => r.json())
]);
```

Execution:

``` text
Users Request ─────┐
                   ├── Responses
Posts Request ─────┘
```

## Why Concurrent Requests?

Benefits include:

-   Reduced waiting time
-   Better user experience
-   Improved application performance

Use concurrency only when requests do not depend on one another.

## Key Takeaways

-   Use sequential requests when later requests depend on earlier
    results.
-   Use concurrent requests for independent operations.
-   `Promise.all()` is a common way to coordinate multiple requests.

## Continue

Next file:

**Fetch-API-Part-8-Section-2.md**
