---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 1 - Section 2
title: Fetch API - Part 1 - Introduction to HTTP and Fetch
topic: Introduction to HTTP and Fetch
---

# Fetch API --- Part 1 (Section 2)

## HTTP Requests and Responses

Every Fetch request follows the same high-level pattern:

``` text
Browser (Client)
      │
HTTP Request
      ▼
Server
      │
HTTP Response
      ▼
Browser
```

A **request** asks the server to perform an action.

A **response** contains:

-   Status code
-   Headers
-   Response body

## HTTP Methods

### GET

Retrieve data.

``` javascript
fetch("/api/products");
```

### POST

Create new data.

``` javascript
fetch("/api/products", {
  method: "POST"
});
```

### PUT

Replace an existing resource.

### PATCH

Update part of an existing resource.

### DELETE

Remove a resource.

## Status Codes

  Code   Meaning
  ------ -----------------------
  200    OK
  201    Created
  204    No Content
  400    Bad Request
  401    Unauthorized
  403    Forbidden
  404    Not Found
  500    Internal Server Error

## Request Headers

Headers describe the request.

``` javascript
fetch("/api/users", {
  headers: {
    "Accept": "application/json"
  }
});
```

## Response Headers

Servers also send headers describing the response.

Examples:

-   `Content-Type`
-   `Cache-Control`
-   `Content-Length`

## Browser Network Lifecycle

``` text
fetch()
   ↓
HTTP request sent
   ↓
Server processes request
   ↓
HTTP response returned
   ↓
JavaScript receives Response object
```

## Continue

Next file:

**Fetch-API-Part-1-Section-3.md**
