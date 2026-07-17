---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 2 - Section 2
title: Fetch API - Part 2 - Working with GET Requests
topic: Working with GET Requests
---

# Fetch API --- Part 2 (Section 2)

## Parsing JSON Responses

A successful `fetch()` returns a **Response** object.

``` javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then(response => response.json())
  .then(data => console.log(data));
```

`response.json()` reads the response body and converts JSON into
JavaScript objects.

## Checking `response.ok`

Always verify that the request succeeded.

``` javascript
fetch("/api/users")
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

## Reading Status Codes

``` javascript
fetch("/api/users")
  .then(response => {
    console.log(response.status);
    console.log(response.statusText);
  });
```

Example output:

``` text
200
OK
```

## Handling Empty Responses

Some endpoints return **204 No Content**.

``` javascript
fetch("/api/logout")
  .then(response => {
    if (response.status === 204) {
      console.log("No content returned.");
      return;
    }

    return response.json();
  });
```

## Displaying Data in the DOM

``` html
<ul id="users"></ul>
```

``` javascript
fetch("https://jsonplaceholder.typicode.com/users")
  .then(response => response.json())
  .then(users => {
    const list = document.getElementById("users");

    users.forEach(user => {
      const li = document.createElement("li");
      li.textContent = user.name;
      list.appendChild(li);
    });
  });
```

Sample output:

``` text
• Leanne Graham
• Ervin Howell
• Clementine Bauch
```

## Common Mistakes

-   Calling `response.json()` twice.
-   Ignoring `response.ok`.
-   Assuming every response contains JSON.
-   Forgetting to handle network failures.

## Debugging Tips

-   Inspect the Network tab.
-   Log the Response object before parsing.
-   Verify endpoint URLs and status codes.

## Continue

Next file:

**Fetch-API-Part-2-Section-3.md**
