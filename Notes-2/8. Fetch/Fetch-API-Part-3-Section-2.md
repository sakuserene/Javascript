---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 3 - Section 2
title: Fetch API - Part 3 - Sending Data with POST Requests
topic: Sending Data with POST Requests
---

# Fetch API --- Part 3 (Section 2)

## Handling POST Responses

Most APIs return information after creating a resource.

``` javascript
fetch("/api/products", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Keyboard",
    price: 2500
  })
})
.then(response => {
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  return response.json();
})
.then(product => {
  console.log(product);
})
.catch(error => {
  console.error(error);
});
```

## Typical Server Response

``` json
{
  "id": 101,
  "name": "Keyboard",
  "price": 2500
}
```

The server often returns the newly created resource with its generated
identifier.

## Handling Validation Errors

``` javascript
fetch("/api/register", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    email: "",
    password: "123"
  })
})
.then(response => {
  if (response.status === 400) {
    throw new Error("Validation failed");
  }

  return response.json();
})
.catch(error => console.error(error.message));
```

Possible output:

``` text
Validation failed
```

## Real-World Examples

### User Registration

``` javascript
fetch("/api/register", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    username: "alice",
    password: "securePassword123"
  })
});
```

### User Login

``` javascript
fetch("/api/login", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    email: "alice@example.com",
    password: "secret"
  })
});
```

### Creating a Blog Post

``` javascript
fetch("/api/posts", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    title: "Learning Fetch API",
    content: "My first article..."
  })
});
```

## Common Mistakes

-   Forgetting the `Content-Type` header.
-   Sending plain objects instead of `JSON.stringify()` output.
-   Ignoring `response.ok`.
-   Assuming every error response contains JSON.

## Debugging Tips

-   Inspect the Network tab.
-   Verify the request payload.
-   Check response status codes and server messages.

## Continue

Next file:

**Fetch-API-Part-3-Section-3.md**
