---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 2 - Section 3
title: Fetch API - Part 2 - Working with GET Requests
topic: Working with GET Requests
---

# Fetch API --- Part 2 (Section 3)

## Query Parameters

Many APIs allow you to filter data using query parameters.

``` javascript
fetch("https://jsonplaceholder.typicode.com/posts?userId=1")
  .then(r => r.json())
  .then(data => console.log(data));
```

The browser sends:

``` text
GET /posts?userId=1
```

## Searching

``` javascript
const term = "javascript";

fetch(`/api/articles?search=${encodeURIComponent(term)}`);
```

Always use `encodeURIComponent()` for user-provided values.

## Pagination

Large datasets are commonly split into pages.

``` javascript
fetch("/api/products?page=2&limit=20");
```

Typical parameters:

-   `page`
-   `limit`
-   `offset`
-   `sort`
-   `order`

## Reusable GET Helper

``` javascript
async function getJSON(url) {
  const response = await fetch(url);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}

getJSON("/api/users")
  .then(users => console.log(users))
  .catch(console.error);
```

## Production Pattern

``` javascript
const params = new URLSearchParams({
  page: 1,
  limit: 10,
  search: "phone"
});

fetch(`/api/products?${params}`);
```

Using `URLSearchParams` avoids manually concatenating query strings.

## Performance Tips

-   Request only the data you need.
-   Use pagination for large datasets.
-   Cache results when appropriate.
-   Avoid duplicate GET requests for the same data.

## Common Mistakes

-   Building query strings by hand.
-   Forgetting to encode user input.
-   Requesting huge datasets unnecessarily.

## Continue

Next file:

**Fetch-API-Part-2-Section-4.md**
