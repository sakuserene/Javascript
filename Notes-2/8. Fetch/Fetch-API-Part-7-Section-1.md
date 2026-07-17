---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 7 - Section 1
title: Fetch API - Part 7 - File Uploads, Downloads, and FormData
topic: File Uploads, Downloads, and FormData
---

# Fetch API --- Part 7 (Section 1)

## Introduction to FormData

`FormData` is a browser API used to send form fields and files together
in an HTTP request.

It is commonly used for:

-   Uploading images
-   Uploading documents
-   Sending text fields with files
-   Multipart form submissions

## Why Not JSON?

JSON works well for structured text data, but binary files require a
different encoding.

`FormData` uses the `multipart/form-data` content type.

  JSON                 FormData
  -------------------- -----------------------
  Text data            Text + files
  `application/json`   `multipart/form-data`

## Creating FormData

``` javascript
const formData = new FormData();
```

## Appending Text Fields

``` javascript
formData.append("name", "Alice");
formData.append("email", "alice@example.com");
```

## Appending a File

``` javascript
const file = document.querySelector("#avatar").files[0];
formData.append("avatar", file);
```

## Uploading with Fetch

``` javascript
await fetch("/api/upload", {
  method: "POST",
  body: formData
});
```

> Do **not** manually set the `Content-Type` header. The browser
> generates the correct multipart boundary automatically.

## Common Mistakes

-   Manually setting `Content-Type` for `FormData`.
-   Forgetting to select a file before uploading.
-   Trying to serialize files with `JSON.stringify()`.

## Continue

Next file:

**Fetch-API-Part-7-Section-2.md**
