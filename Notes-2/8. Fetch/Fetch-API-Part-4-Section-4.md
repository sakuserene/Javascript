---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 4 - Section 4 (Completion)
title: Fetch API - Part 4 - PUT, PATCH, and DELETE Requests
topic: PUT, PATCH, and DELETE Requests
---

# Fetch API --- Part 4 (Section 4)

## Best Practices

-   Use **PUT** only when replacing an entire resource.
-   Use **PATCH** for partial updates.
-   Confirm deletions before sending `DELETE` requests.
-   Check `response.ok` before processing responses.
-   Keep API logic in reusable helper functions.

## Knowledge Check

1.  What is the difference between `PUT` and `PATCH`?
2.  When should `DELETE` be used?
3.  Why can a successful `DELETE` return `204 No Content`?
4.  Why should you include authentication headers when required?
5.  Why should update logic be separated from UI code?

## Exercises

### Beginner

1.  Update a user's name with `PATCH`.
2.  Replace a product using `PUT`.
3.  Delete a record and handle a `204` response.

### Intermediate

1.  Create reusable `putJSON()` and `patchJSON()` helpers.
2.  Implement optimistic UI updates.
3.  Handle authorization failures (`401` and `403`).

### Advanced

1.  Build a complete CRUD interface using `GET`, `POST`, `PATCH`, and
    `DELETE`.
2.  Implement rollback logic if an optimistic update fails.

## Interview Questions

**Q:** When should you use `PATCH` instead of `PUT`?

**A:** Use `PATCH` when only specific fields need to change. Use `PUT`
when replacing the entire resource.

**Q:** Why is `204 No Content` common after `DELETE`?

**A:** It indicates the operation succeeded and there is no response
body to return.

## Debugging Checklist

-   Verify the endpoint URL.
-   Confirm the HTTP method.
-   Inspect request headers and payload.
-   Check the response status code.
-   Review server logs for validation or permission errors.

## Part 4 Summary

You learned:

-   The differences between `PUT`, `PATCH`, and `DELETE`.
-   Updating and deleting resources with `fetch()`.
-   Handling update/delete responses.
-   Using reusable helpers and authentication headers.
-   Production CRUD patterns and security best practices.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  Fetch API -- Part 3            Fetch API -- Part 4      Fetch API --
                                                          Part 5: Error
                                                          Handling and
                                                          Robust Fetch
                                                          Patterns

  ------------------------------------------------------------------------
