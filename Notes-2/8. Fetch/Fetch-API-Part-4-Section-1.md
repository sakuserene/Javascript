---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 4 - Section 1
title: Fetch API - Part 4 - PUT, PATCH, and DELETE Requests
topic: PUT, PATCH, and DELETE Requests
---

# Fetch API --- Part 4 (Section 1)

## Introduction

CRUD applications need more than reading and creating data.

You also need to:

-   Update existing resources
-   Delete resources

REST APIs commonly use three HTTP methods:

-   **PUT** -- Replace an existing resource
-   **PATCH** -- Partially update a resource
-   **DELETE** -- Remove a resource

## PUT vs PATCH

  PUT                                   PATCH
  ------------------------------------- -------------------------------
  Replaces the entire resource          Updates only specified fields
  Typically sends the complete object   Sends only changed values

Example resource:

``` json
{
  "id": 10,
  "name": "Keyboard",
  "price": 2500,
  "stock": 15
}
```

### PUT Example

``` javascript
fetch("/api/products/10", {
  method: "PUT",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    id: 10,
    name: "Mechanical Keyboard",
    price: 2800,
    stock: 20
  })
});
```

### PATCH Example

``` javascript
fetch("/api/products/10", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    price: 2800
  })
});
```

## DELETE

``` javascript
fetch("/api/products/10", {
  method: "DELETE"
});
```

Many APIs respond with **204 No Content** after a successful deletion.

## Key Takeaways

-   Use **PUT** for full replacement.
-   Use **PATCH** for partial updates.
-   Use **DELETE** to remove resources.
-   Always follow your API's documentation.

## Continue

Next file:

**Fetch-API-Part-4-Section-2.md**
