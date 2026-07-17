---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 6 - Section 3
title: Fetch API - Part 6 - Authentication, Headers, and Tokens
topic: Authentication, Headers, and Tokens
---

# Fetch API --- Part 6 (Section 3)

## Reusable Authenticated Fetch Wrapper

Centralize authentication logic so every request behaves consistently.

``` javascript
async function authFetch(url, options = {}) {
  const token = localStorage.getItem("accessToken");

  const response = await fetch(url, {
    ...options,
    headers: {
      ...options.headers,
      Authorization: `Bearer ${token}`
    }
  });

  return response;
}
```

## Automatically Refreshing Tokens

``` javascript
async function requestWithRefresh(url, options = {}) {
  let response = await authFetch(url, options);

  if (response.status === 401) {
    await refreshAccessToken();
    response = await authFetch(url, options);
  }

  return response;
}
```

## Secure Logout

When refresh fails:

-   Remove stored tokens.
-   Clear user state.
-   Redirect to the login page.

``` javascript
localStorage.removeItem("accessToken");
localStorage.removeItem("refreshToken");
window.location.href = "/login";
```

## Security Best Practices

-   Prefer HttpOnly cookies when supported by your backend.
-   Never commit tokens to source control.
-   Always use HTTPS.
-   Keep access tokens short-lived.
-   Validate permissions on the server.

## Production Architecture

``` text
UI
 ↓
Auth Fetch Wrapper
 ↓
Refresh Logic
 ↓
fetch()
 ↓
API
```

## Common Mistakes

-   Storing sensitive tokens insecurely.
-   Repeating authentication logic throughout the application.
-   Ignoring failed refresh requests.

## Continue

Next file:

**Fetch-API-Part-6-Section-4.md**
