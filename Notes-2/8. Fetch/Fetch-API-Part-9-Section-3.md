---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 9 - Section 3
title: Fetch API - Part 9 - Building a Reusable API Client
topic: Building a Reusable API Client
---

# Fetch API --- Part 9 (Section 3)

## Automatic Authentication

A reusable API client can automatically attach authentication tokens.

``` javascript
function authHeaders() {
  const token = localStorage.getItem("accessToken");

  return {
    "Content-Type": "application/json",
    "Accept": "application/json",
    ...(token && {
      Authorization: `Bearer ${token}`
    })
  };
}
```

## Automatic Token Refresh

Retry the request after refreshing an expired access token.

``` javascript
async function apiRequest(path, options = {}) {
  let response = await fetch(`${API_BASE_URL}${path}`, {
    ...options,
    headers: {
      ...authHeaders(),
      ...options.headers
    }
  });

  if (response.status === 401) {
    await refreshAccessToken();

    response = await fetch(`${API_BASE_URL}${path}`, {
      ...options,
      headers: {
        ...authHeaders(),
        ...options.headers
      }
    });
  }

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}
```

## Centralized Error Handling

Keep error handling inside the API layer.

``` javascript
try {
  const users = await get("/users");
} catch (error) {
  console.error(error.message);
}
```

## Production Architecture

``` text
UI
 ↓
API Client
 ↓
Authentication
 ↓
Error Handling
 ↓
fetch()
 ↓
Backend
```

## Common Mistakes

-   Refreshing tokens in every component.
-   Duplicating authorization logic.
-   Ignoring failed refresh requests.
-   Mixing API code with UI rendering.

## Continue

Next file:

**Fetch-API-Part-9-Section-4.md**
