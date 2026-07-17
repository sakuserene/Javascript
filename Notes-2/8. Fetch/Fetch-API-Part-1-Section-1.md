---
title: Fetch API - Part 1 - Introduction to HTTP and Fetch
---

# Fetch API --- Part 1 (Section 1)

## What is HTTP?

HTTP (HyperText Transfer Protocol) is the protocol browsers and servers
use to communicate.

### Request → Response

``` text
Browser
   │ Request
   ▼
Server
   │ Response
   ▼
Browser
```

## What is Fetch?

`fetch()` is the modern browser API for making HTTP requests.

``` javascript
fetch("/api/users");
```

It returns a **Promise** immediately while the browser performs the
network request.

## Why Fetch?

-   Promise-based
-   Cleaner than XMLHttpRequest
-   Supports async/await
-   Built into modern browsers

## Continue

Next: Fetch-API-Part-1-Section-2.md
