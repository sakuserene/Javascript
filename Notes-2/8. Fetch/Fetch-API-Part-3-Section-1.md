---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 3 - Section 1
title: Fetch API - Part 3 - Sending Data with POST Requests
topic: Sending Data with POST Requests
---

# Fetch API --- Part 3 (Section 1)

## Introduction

A **POST** request sends data from the client to the server.

Unlike **GET**, which retrieves data, **POST** is commonly used to:

-   Create new records
-   Submit forms
-   Send login credentials
-   Upload structured data

## GET vs POST

  GET                         POST
  --------------------------- ------------------------------
  Retrieves data              Sends data
  Parameters usually in URL   Data sent in request body
  Safe and idempotent         Usually changes server state

## Basic POST Request

``` javascript
fetch("/api/products", {
  method: "POST"
});
```

## Sending JSON

``` javascript
const product = {
  name: "Keyboard",
  price: 2500
};

fetch("/api/products", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(product)
});
```

## Why `JSON.stringify()`?

The request body must be transmitted as text.

`JSON.stringify()` converts a JavaScript object into a JSON string.

``` javascript
const user = { name: "Alice" };

console.log(JSON.stringify(user));
```

Output:

``` text
{"name":"Alice"}
```

## Request Flow

``` text
JavaScript Object
      ↓
JSON.stringify()
      ↓
HTTP Request Body
      ↓
Server
```

## Key Takeaways

-   POST sends data to a server.
-   Use `method: "POST"`.
-   Set `Content-Type: application/json`.
-   Convert objects with `JSON.stringify()` before sending.

## Continue

Next file:

**Fetch-API-Part-3-Section-2.md**
