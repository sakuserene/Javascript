---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 6 - Section 1
title: Fetch API - Part 6 - Authentication, Headers, and Tokens
topic: Authentication, Headers, and Tokens
---

# Fetch API --- Part 6 (Section 1)

## Introduction

Many APIs require authentication before allowing access to protected
resources.

Authentication helps verify **who** is making the request, while
authorization determines **what** they are allowed to do.

## HTTP Headers

Headers provide additional information about a request.

``` javascript
fetch("/api/users", {
  headers: {
    "Accept": "application/json"
  }
});
```

Common headers:

-   `Accept`
-   `Content-Type`
-   `Authorization`

## Authorization Header

Protected APIs commonly expect credentials in the `Authorization`
header.

``` javascript
fetch("/api/profile", {
  headers: {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN"
  }
});
```

## Bearer Tokens

A Bearer token is an access token sent with each authenticated request.

``` text
Client
   │
Authorization: Bearer eyJhbGciOi...
   ▼
Server
```

If the token is valid, the server processes the request.

## API Keys vs Bearer Tokens

  API Key                     Bearer Token
  --------------------------- -------------------------------------
  Identifies an application   Identifies a user or session
  Often long-lived            Usually short-lived
  Simpler authentication      More secure for user authentication

## JWT Overview

A JSON Web Token (JWT) is a signed token that often contains user
identity and expiration information.

Applications typically:

1.  Log in.
2.  Receive an access token.
3.  Store it securely.
4.  Send it in future requests.

## Key Takeaways

-   Authentication commonly uses HTTP headers.
-   `Authorization: Bearer <token>` is widely used.
-   JWTs are common bearer tokens for modern APIs.

## Continue

Next file:

**Fetch-API-Part-6-Section-2.md**
