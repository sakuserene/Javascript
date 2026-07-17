---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 4 - Section 3
title: Fetch API - Part 4 - PUT, PATCH, and DELETE Requests
topic: PUT, PATCH, and DELETE Requests
---

# Fetch API --- Part 4 (Section 3)

## Using `async` / `await`

`async`/`await` works naturally with update and delete requests.

``` javascript
async function updateProduct(id, changes) {
  const response = await fetch(`/api/products/${id}`, {
    method: "PATCH",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(changes)
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}
```

## Reusable Helper Functions

``` javascript
async function patchJSON(url, data) {
  const response = await fetch(url, {
    method: "PATCH",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });

  if (!response.ok) throw new Error("Update failed");

  return response.json();
}
```

``` javascript
async function deleteResource(url) {
  const response = await fetch(url, {
    method: "DELETE"
  });

  if (!response.ok) throw new Error("Delete failed");
}
```

## Authentication Headers

Many APIs require an access token.

``` javascript
await fetch("/api/products/10", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer YOUR_ACCESS_TOKEN"
  },
  body: JSON.stringify({
    price: 3000
  })
});
```

## Security Best Practices

-   Send authentication tokens only over HTTPS.
-   Validate permissions on the server.
-   Never trust client-side checks.
-   Update only fields users are allowed to modify.

## Production CRUD Pattern

``` text
UI
 ↓
Validation
 ↓
API Helper
 ↓
fetch()
 ↓
Server
 ↓
Update UI
```

## Common Mistakes

-   Using `PUT` when only a single field changes.
-   Forgetting authorization headers.
-   Assuming every successful response contains JSON.

## Continue

Next file:

**Fetch-API-Part-4-Section-4.md**
