---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 1 - Section 4 (Completion)
title: Fetch API - Part 1 - Introduction to HTTP and Fetch
topic: Introduction to HTTP and Fetch
---

# Fetch API --- Part 1 (Section 4)

## Best Practices

-   Always check `response.ok` before using response data.
-   Handle network failures gracefully.
-   Keep request logic separate from UI code.
-   Prefer HTTPS endpoints.
-   Read API documentation before integrating.

## Knowledge Check

1.  What problem does HTTP solve?
2.  What is the difference between a client and a server?
3.  Why does `fetch()` return a Promise?
4.  What is the purpose of `response.json()`?
5.  Name five common HTTP methods.
6.  What does status code **404** mean?
7.  When would you use **POST** instead of **GET**?

## Exercises

### Beginner

1.  Fetch a list of users from a public API.
2.  Log the `Response` object.
3.  Parse and display the returned JSON.

### Intermediate

1.  Display the names of all returned users.
2.  Show an error message if `response.ok` is `false`.
3.  Display the HTTP status code on the page.

### Advanced

1.  Build a reusable `fetchData()` helper.
2.  Compare the responses from two different APIs.

## Interview Questions

**Q:** Why doesn't `fetch()` return the data immediately?

**A:** Network requests are asynchronous. `fetch()` immediately returns
a Promise while the browser communicates with the server in the
background.

**Q:** What is the purpose of `response.json()`?

**A:** It reads the response body and parses it as JSON, returning
another Promise that resolves to the JavaScript object or array.

## Part 1 Summary

You learned:

-   How HTTP enables communication between browsers and servers.
-   The request--response lifecycle.
-   Common HTTP methods and status codes.
-   Why the Fetch API is Promise-based.
-   How to make your first `fetch()` request.
-   How to parse JSON responses.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  DOM Events -- Part 12          Fetch API -- Part 1      Fetch API --
                                                          Part 2: Working
                                                          with GET
                                                          Requests

  ------------------------------------------------------------------------
