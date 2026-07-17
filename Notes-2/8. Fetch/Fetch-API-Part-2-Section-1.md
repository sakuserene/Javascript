---
title: Fetch API - Part 2 - Working with GET Requests
---

# Fetch API --- Part 2 (Section 1)

## GET Requests

A GET request retrieves data from a server without modifying it.

``` javascript
fetch("/api/products")
  .then(r => r.json())
  .then(data => console.log(data));
```

## GET is the Default

``` javascript
fetch("/api/products");
```

is equivalent to

``` javascript
fetch("/api/products", {
  method: "GET"
});
```

## Response Object

Successful requests resolve to a `Response` object.

Check:

-   `response.ok`
-   `response.status`
-   `response.headers`

## Continue

Next: Fetch-API-Part-2-Section-2.md
