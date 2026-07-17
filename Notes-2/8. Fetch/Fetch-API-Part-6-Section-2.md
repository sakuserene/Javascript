---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 6 - Section 2
title: Fetch API - Part 6 - Authentication, Headers, and Tokens
topic: Authentication, Headers, and Tokens
---

# Fetch API --- Part 6 (Section 2)

## Sending Authenticated Requests

Once a user is authenticated, include the access token in the
`Authorization` header.

``` javascript
const accessToken = "YOUR_ACCESS_TOKEN";

fetch("/api/profile", {
  headers: {
    "Authorization": `Bearer ${accessToken}`
  }
});
```

## Access Tokens vs Refresh Tokens

  Access Token            Refresh Token
  ----------------------- ------------------------------------------
  Short-lived             Long-lived
  Used for API requests   Used to obtain a new access token
  Sent frequently         Sent only when refreshing authentication

Typical flow:

``` text
Login
  ↓
Access Token + Refresh Token
  ↓
API Requests
  ↓
Access Token Expires
  ↓
Use Refresh Token
  ↓
Receive New Access Token
```

## Handling `401 Unauthorized`

``` javascript
const response = await fetch("/api/profile");

if (response.status === 401) {
  console.log("Please sign in again.");
}
```

A `401` response usually means the request lacks valid authentication.

## Handling `403 Forbidden`

``` javascript
if (response.status === 403) {
  console.log("You do not have permission.");
}
```

A `403` response indicates the user is authenticated but not authorized
to perform the action.

## Automatic Token Refresh

Many applications automatically request a new access token before
retrying the original request.

Keep this logic in a reusable API helper rather than scattering it
throughout the UI.

## Production Workflow

``` text
Login
  ↓
Store Tokens
  ↓
Authenticated Requests
  ↓
Refresh When Needed
  ↓
Logout if Refresh Fails
```

## Continue

Next file:

**Fetch-API-Part-6-Section-3.md**
