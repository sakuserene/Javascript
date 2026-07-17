---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 1 - Section 3
title: Fetch API - Part 1 - Introduction to HTTP and Fetch
topic: Introduction to HTTP and Fetch
---

# Fetch API --- Part 1 (Section 3)

## Your First `fetch()` Request

The simplest Fetch request uses only a URL.

``` javascript
fetch("https://jsonplaceholder.typicode.com/users");
```

This immediately returns a **Promise**, not the actual data.

## Why a Promise?

Network communication takes time.

JavaScript cannot pause the entire application while waiting for a
server.

Instead, `fetch()` starts the request and immediately returns a Promise
that will eventually be:

-   **fulfilled** with a `Response`
-   **rejected** if a network error occurs

``` text
fetch()
   ↓
Promise returned immediately
   ↓
Browser sends HTTP request
   ↓
Server responds
   ↓
Promise settles
```

## Reading the Response

``` javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then((response) => {
    console.log(response);
  });
```

The value received is a **Response object**, not the JSON data itself.

## Parsing JSON

Most APIs return JSON.

``` javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then((response) => response.json())
  .then((data) => {
    console.log(data);
  });
```

Sample output (truncated):

``` text
[
  { id: 1, name: "Leanne Graham" },
  { id: 2, name: "Ervin Howell" }
]
```

## Common Beginner Mistakes

-   Expecting `fetch()` to return data immediately.
-   Forgetting to call `response.json()`.
-   Assuming every response is successful without checking
    `response.ok`.

## Debugging Tips

-   Log the `Response` object before parsing it.
-   Inspect the **Network** tab in browser DevTools.
-   Check the response status code before using the data.

## Continue

Next file:

**Fetch-API-Part-1-Section-4.md**
