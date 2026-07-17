---
course: JavaScript Beginner to Professional Mastery
module: Fetch API
part: 7 - Section 2
title: Fetch API - Part 7 - File Uploads, Downloads, and FormData
topic: File Uploads, Downloads, and FormData
---

# Fetch API --- Part 7 (Section 2)

## Uploading Multiple Files

You can append more than one file to the same `FormData` object.

``` html
<input id="photos" type="file" multiple>
```

``` javascript
const input = document.getElementById("photos");
const formData = new FormData();

for (const file of input.files) {
  formData.append("photos", file);
}

await fetch("/api/upload", {
  method: "POST",
  body: formData
});
```

## Sending Files with Text Fields

`FormData` can include both files and regular fields.

``` javascript
formData.append("title", "Vacation Photos");
formData.append("description", "Trip to the coast");

for (const file of input.files) {
  formData.append("photos", file);
}
```

The server receives both the uploaded files and the accompanying text
data.

## Previewing Selected Files

``` javascript
input.addEventListener("change", () => {
  for (const file of input.files) {
    console.log(file.name, file.size);
  }
});
```

Sample output:

``` text
photo1.jpg 215034
photo2.jpg 198551
```

## Validating Files

Check the file before uploading.

``` javascript
const file = input.files[0];

if (file.size > 5 * 1024 * 1024) {
  console.log("File is too large.");
}

if (!file.type.startsWith("image/")) {
  console.log("Only image files are allowed.");
}
```

## Best Practices

-   Validate file size before uploading.
-   Validate MIME types.
-   Display upload progress or feedback.
-   Allow users to remove files before submitting.

## Continue

Next file:

**Fetch-API-Part-7-Section-3.md**
