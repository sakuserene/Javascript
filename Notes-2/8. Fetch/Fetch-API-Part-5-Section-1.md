---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 5 - Section 1
title: Fetch API - Part 5 - Error Handling and Robust Fetch Patterns
topic: Error Handling and Robust Fetch Patterns
---

# Fetch API --- Part 5 (Section 1)

## Introduction

Real-world applications must expect requests to fail.

Failures may occur because of:

-   No internet connection
-   Server errors
-   Invalid requests
-   Timeouts
-   Authentication failures

Understanding how Fetch reports these failures is essential.

## Network Errors vs HTTP Errors

A common misconception is that `fetch()` rejects every unsuccessful
request.

It does **not**.

### Network Error

``` javascript
try {
  await fetch("https://invalid.example");
} catch (error) {
  console.error("Network error:", error.message);
}
```

Network failures reject the Promise.

### HTTP Error

``` javascript
const response = await fetch("/api/users/999");

console.log(response.ok);
console.log(response.status);
```

Possible output:

``` text
false
404
```

The Promise resolves successfully because the server responded, even
though the response indicates an error.

## Checking `response.ok`

Always verify the response before parsing it.

``` javascript
const response = await fetch("/api/users/999");

if (!response.ok) {
  throw new Error(`HTTP ${response.status}`);
}

const data = await response.json();
```

## Error Flow

``` text
fetch()
   ↓
Network failure?
   ├── Yes → Promise rejected
   └── No
        ↓
Response received
        ↓
Check response.ok
        ↓
Process data or throw error
```

## Key Takeaways

-   Network errors reject the Promise.
-   HTTP errors usually do not reject the Promise.
-   Always inspect `response.ok` and `response.status`.

## Continue

Next file:

**Fetch-API-Part-5-Section-2.md**
