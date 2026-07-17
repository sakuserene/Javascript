---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 7 - Section 4 (Completion)
title: Fetch API - Part 7 - File Uploads, Downloads, and FormData
topic: File Uploads, Downloads, and FormData
---

# Fetch API --- Part 7 (Section 4)

## Best Practices

-   Validate files before uploading.
-   Do not manually set the `Content-Type` header when using `FormData`.
-   Check `response.ok` before processing downloads.
-   Revoke object URLs after use with `URL.revokeObjectURL()`.
-   Provide user feedback during long uploads and downloads.

## Knowledge Check

1.  What is `FormData` used for?
2.  Why shouldn't you manually set the `Content-Type` header for
    `FormData`?
3.  What does `response.blob()` return?
4.  Why should object URLs be revoked?
5.  How can you validate uploaded files before sending them?

## Exercises

### Beginner

1.  Upload a single image.
2.  Upload multiple files.
3.  Display the names of selected files.

### Intermediate

1.  Validate image type and size before upload.
2.  Download a PDF using `fetch()`.
3.  Display a downloaded image in an `<img>` element.

### Advanced

1.  Build a document upload page with previews and validation.
2.  Create a reusable download helper using `Blob` and
    `URL.createObjectURL()`.

## Interview Questions

**Q:** Why is `FormData` preferred over JSON for file uploads?

**A:** `FormData` supports multipart requests, allowing binary files and
text fields to be sent together.

**Q:** Why should `URL.revokeObjectURL()` be called?

**A:** It releases browser resources associated with temporary object
URLs, helping prevent memory leaks.

## Debugging Checklist

-   Confirm a file is selected.
-   Verify the upload endpoint.
-   Check file size and MIME type.
-   Inspect multipart requests in the Network tab.
-   Verify download responses before creating object URLs.

## Part 7 Summary

You learned:

-   Creating and using `FormData`.
-   Uploading single and multiple files.
-   Combining text fields with files.
-   Downloading binary data with `Blob`.
-   Creating downloadable links and displaying downloaded images.
-   Security and performance best practices.

## Navigation

  ------------------------------------------------------------------------
  Previous                       Current                  Next
  ------------------------------ ------------------------ ----------------
  Fetch API -- Part 6            Fetch API -- Part 7      Fetch API --
                                                          Part 8:
                                                          Async/Await
                                                          Patterns and
                                                          Concurrent
                                                          Requests

  ------------------------------------------------------------------------
