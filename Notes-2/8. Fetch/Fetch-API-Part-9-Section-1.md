---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 9 - Section 1
title: Fetch API - Part 9 - Building a Reusable API Client
topic: Building a Reusable API Client
---

# Fetch API --- Part 9 (Section 1)

## Introduction

As applications grow, scattering `fetch()` calls throughout UI
components becomes difficult to maintain.

A reusable API client centralizes networking logic so that:

-   Requests are consistent.
-   Authentication is handled in one place.
-   Error handling is standardized.
-   Code is easier to test and maintain.

## Layered Architecture

``` text
UI Components
      ↓
API Client
      ↓
fetch()
      ↓
Backend API
```

The UI should focus on rendering and user interaction, while the API
client manages communication with the server.

## Basic API Client

``` javascript
const API_BASE_URL = "https://api.example.com";

async function apiRequest(path, options = {}) {
  const response = await fetch(`${API_BASE_URL}${path}`, options);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}
```

## Reusable GET Helper

``` javascript
function get(path) {
  return apiRequest(path);
}

get("/users")
  .then(users => console.log(users))
  .catch(console.error);
```

## Benefits

-   Less duplicated code
-   Easier updates
-   Consistent error handling
-   Better scalability

## Common Mistakes

-   Hard-coding API URLs throughout the application.
-   Mixing rendering code with networking logic.
-   Duplicating request logic.

## Continue

Next file:

**Fetch-API-Part-9-Section-2.md**
