---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 4 - Section 2
title: Fetch API - Part 4 - PUT, PATCH, and DELETE Requests
topic: PUT, PATCH, and DELETE Requests
---

# Fetch API --- Part 4 (Section 2)

## Handling Update Responses

Always verify that an update request succeeded before using the returned
data.

``` javascript
fetch("/api/products/10", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    price: 2800
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
.catch(console.error);
```

## Handling `204 No Content`

Some APIs return no response body after a successful update or deletion.

``` javascript
fetch("/api/products/10", {
  method: "DELETE"
})
.then(response => {
  if (response.status === 204) {
    console.log("Product deleted.");
    return;
  }

  return response.json();
});
```

Output:

``` text
Product deleted.
```

## Optimistic UI Updates

For a responsive user experience:

1.  Update the interface immediately.
2.  Send the request.
3.  Roll back the change if the request fails.

## Error Handling

``` javascript
try {
  const response = await fetch("/api/products/10", {
    method: "PUT",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(updatedProduct)
  });

  if (!response.ok) {
    throw new Error("Update failed");
  }
} catch (error) {
  console.error(error.message);
}
```

## Real-World CRUD Example

-   Edit a product
-   Save changes with `PATCH`
-   Delete the product with `DELETE`
-   Refresh the displayed list

## Common Mistakes

-   Expecting JSON from a `204 No Content` response.
-   Ignoring `response.ok`.
-   Replacing an entire resource with `PUT` when only one field changed.

## Continue

Next file:

**Fetch-API-Part-4-Section-3.md**
