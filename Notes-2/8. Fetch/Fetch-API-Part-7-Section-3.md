---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 7 - Section 3
title: Fetch API - Part 7 - File Uploads, Downloads, and FormData
topic: File Uploads, Downloads, and FormData
---

# Fetch API --- Part 7 (Section 3)

## Downloading Files with Fetch

The Fetch API can also retrieve files such as images, PDFs, and ZIP
archives.

``` javascript
const response = await fetch("/files/report.pdf");
```

Instead of calling `response.json()`, use `response.blob()` for binary
data.

``` javascript
const blob = await response.blob();
```

## What is a Blob?

A **Blob** (Binary Large Object) represents raw binary data.

Common uses:

-   Images
-   PDFs
-   Audio
-   Video
-   ZIP files

## Creating a Download Link

``` javascript
const response = await fetch("/files/report.pdf");
const blob = await response.blob();

const url = URL.createObjectURL(blob);

const link = document.createElement("a");
link.href = url;
link.download = "report.pdf";
link.click();

URL.revokeObjectURL(url);
```

## Displaying Downloaded Images

``` javascript
const response = await fetch("/images/photo.jpg");
const blob = await response.blob();

document.querySelector("img").src = URL.createObjectURL(blob);
```

## Security Considerations

-   Validate downloaded file types.
-   Download files only from trusted sources.
-   Revoke object URLs after use to avoid memory leaks.

## Performance Tips

-   Stream large files when supported.
-   Avoid keeping large Blob objects in memory longer than necessary.
-   Reuse downloaded files through browser caching when appropriate.

## Common Mistakes

-   Using `response.json()` for binary files.
-   Forgetting `URL.revokeObjectURL()`.
-   Assuming every download succeeds without checking `response.ok`.

## Continue

Next file:

**Fetch-API-Part-7-Section-4.md**
