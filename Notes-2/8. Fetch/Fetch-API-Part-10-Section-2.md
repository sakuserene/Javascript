---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 10 - Section 2
title: "Fetch API - Part 10 - Capstone Project: Building a Complete API
  Service Layer"
topic: Capstone Project -- Building a Complete API Service Layer
---

# Fetch API --- Part 10 (Section 2)

## Building the Core API Client

Create a single client responsible for every HTTP request.

``` javascript
const API_BASE_URL = import.meta.env.VITE_API_URL;

export async function apiRequest(path, options = {}) {
  const response = await fetch(`${API_BASE_URL}${path}`, {
    ...options,
    headers: {
      Accept: "application/json",
      ...options.headers
    }
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.status === 204
    ? null
    : response.json();
}
```

## Reusable CRUD Helpers

``` javascript
export const get = (path) => apiRequest(path);

export const post = (path, data) =>
  apiRequest(path, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data)
  });

export const patch = (path, data) =>
  apiRequest(path, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data)
  });

export const remove = (path) =>
  apiRequest(path, { method: "DELETE" });
```

## Integrating Authentication

``` javascript
function authHeaders() {
  const token = localStorage.getItem("accessToken");

  return token
    ? { Authorization: `Bearer ${token}` }
    : {};
}
```

Merge authentication headers into every protected request.

## Project Flow

``` text
Component
   ↓
Resource Module
   ↓
CRUD Helper
   ↓
apiRequest()
   ↓
Backend API
```

## Best Practices

-   Keep the client framework-agnostic.
-   Export small reusable helpers.
-   Handle authentication centrally.
-   Return parsed data consistently.

## Continue

Next file:

**Fetch-API-Part-10-Section-3.md**
