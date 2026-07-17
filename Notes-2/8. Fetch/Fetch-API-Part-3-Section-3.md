---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 3 - Section 3
title: Fetch API - Part 3 - Sending Data with POST Requests
topic: Sending Data with POST Requests
---

# Fetch API --- Part 3 (Section 3)

## Using `async` / `await`

`async`/`await` makes asynchronous code easier to read.

``` javascript
async function createProduct() {
  const response = await fetch("/api/products", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      name: "Keyboard",
      price: 2500
    })
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  const product = await response.json();
  console.log(product);
}

createProduct().catch(console.error);
```

## Sending Nested Objects

``` javascript
const order = {
  customer: {
    name: "Alice",
    email: "alice@example.com"
  },
  items: [
    { id: 1, quantity: 2 },
    { id: 5, quantity: 1 }
  ]
};

fetch("/api/orders", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(order)
});
```

`JSON.stringify()` correctly converts nested objects and arrays into
JSON.

## Reusable POST Helper

``` javascript
async function postJSON(url, data) {
  const response = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}
```

## Security Best Practices

-   Include authentication tokens when required.
-   Protect state-changing requests against CSRF where applicable.
-   Validate all data on the server.
-   Never trust client-side validation alone.

## Production Pattern

Keep networking code separate from UI components.

``` text
UI
 ↓
API Helper
 ↓
fetch()
 ↓
Server
```

## Common Mistakes

-   Forgetting `await` before `response.json()`.
-   Sending circular objects that cannot be serialized.
-   Mixing UI logic with networking logic.

## Continue

Next file:

**Fetch-API-Part-3-Section-4.md**
