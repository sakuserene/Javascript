---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 5 - Section 3
title: Fetch API - Part 5 - Error Handling and Robust Fetch Patterns
topic: Error Handling and Robust Fetch Patterns
---

# Fetch API --- Part 5 (Section 3)

## Retrying Failed Requests

Temporary failures sometimes succeed when retried.

``` javascript
async function fetchWithRetry(url, retries = 3) {
  for (let attempt = 1; attempt <= retries; attempt++) {
    try {
      const response = await fetch(url);

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }

      return await response.json();
    } catch (error) {
      if (attempt === retries) throw error;
    }
  }
}
```

## Exponential Backoff

``` text
Attempt 1 → Wait 1 second
Attempt 2 → Wait 2 seconds
Attempt 3 → Wait 4 seconds
Attempt 4 → Wait 8 seconds
```

Use increasing delays to avoid overwhelming a busy server.

## Centralized Fetch Wrapper

``` javascript
async function apiRequest(url, options = {}) {
  const response = await fetch(url, options);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}
```

## Production Architecture

``` text
UI
 ↓
API Service
 ↓
Fetch Wrapper
 ↓
fetch()
 ↓
Server
```

## Security

-   Never expose secrets in client-side code.
-   Send tokens only over HTTPS.
-   Do not endlessly retry authorization failures.

## Performance

-   Retry only transient failures.
-   Cache successful responses when appropriate.
-   Avoid duplicate requests.

## Continue

Next file:

**Fetch-API-Part-5-Section-4.md**
