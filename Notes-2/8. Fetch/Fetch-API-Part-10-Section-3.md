---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 10 - Section 3
title: "Fetch API - Part 10 - Capstone Project: Building a Complete API
  Service Layer"
topic: Capstone Project -- Building a Complete API Service Layer
---

# Fetch API --- Part 10 (Section 3)

## Automatic Token Refresh

A production API client should retry protected requests after refreshing
an expired access token.

``` javascript
async function request(path, options = {}) {
  let response = await apiRequest(path, options);

  if (response?.status === 401) {
    await refreshAccessToken();
    response = await apiRequest(path, options);
  }

  return response;
}
```

## Centralized Error Handling

``` javascript
export function handleApiError(error) {
  console.error(error);

  return {
    message: error.message,
    success: false
  };
}
```

Every module can reuse the same error handler.

## File Upload Integration

``` javascript
export async function uploadAvatar(formData) {
  return fetch(`${API_BASE_URL}/upload`, {
    method: "POST",
    body: formData,
    headers: authHeaders()
  });
}
```

> Do not manually set the `Content-Type` header when sending `FormData`.

## Concurrent Requests

``` javascript
const [users, products] = await Promise.all([
  get("/users"),
  get("/products")
]);
```

Load independent resources together to reduce waiting time.

## Final Architecture

``` text
UI
 ↓
Resource Modules
 ↓
API Client
 ├── Authentication
 ├── CRUD Helpers
 ├── Uploads
 ├── Error Handling
 └── Concurrent Requests
 ↓
Backend API
```

## Best Practices

-   Keep every module focused on one responsibility.
-   Share reusable helpers.
-   Handle retries and authentication centrally.
-   Test each layer independently.

## Continue

Next file:

**Fetch-API-Part-10-Section-4.md**
