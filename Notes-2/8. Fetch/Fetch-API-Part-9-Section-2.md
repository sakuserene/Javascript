---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 9 - Section 2
title: Fetch API - Part 9 - Building a Reusable API Client
topic: Building a Reusable API Client
---

# Fetch API --- Part 9 (Section 2)

## Reusable CRUD Helpers

Instead of repeating request configuration, create helper functions.

``` javascript
function get(path) {
  return apiRequest(path);
}

function post(path, data) {
  return apiRequest(path, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });
}
```

``` javascript
function patch(path, data) {
  return apiRequest(path, {
    method: "PATCH",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });
}

function remove(path) {
  return apiRequest(path, {
    method: "DELETE"
  });
}
```

## Centralized Headers

Define headers in one place.

``` javascript
function defaultHeaders() {
  return {
    "Content-Type": "application/json",
    "Accept": "application/json"
  };
}
```

Merge custom headers when needed.

## Environment-Based Configuration

``` javascript
const API_BASE_URL =
  import.meta.env?.VITE_API_URL ??
  "https://api.example.com";
```

Or use your framework's environment variable system.

## CRUD Example

``` javascript
await post("/products", {
  name: "Keyboard",
  price: 2500
});

await patch("/products/1", {
  price: 2800
});

await remove("/products/1");
```

## Best Practices

-   Keep helpers small.
-   Reuse configuration.
-   Avoid hard-coded URLs.
-   Keep UI components free of networking details.

## Continue

Next file:

**Fetch-API-Part-9-Section-3.md**
