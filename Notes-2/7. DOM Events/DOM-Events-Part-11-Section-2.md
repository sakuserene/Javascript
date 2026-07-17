---
course: JavaScript Beginner to Professional Mastery
module: Document Object Model (DOM)
part: 11 - Section 2
subtopic: Form Events
title: DOM Events - Part 11 - Form Events
topic: DOM Events
---

# DOM Events --- Part 11 (Section 2)

## `input` vs `change`

Although both events respond to user input, they fire at different
times.

### `input`

Fires **immediately** whenever the value changes.

``` javascript
username.addEventListener("input", (event) => {
  console.log(event.target.value);
});
```

Typing `cat` produces:

``` text
c
ca
cat
```

### `change`

Fires only after the value has been committed.

For text inputs, this usually means the field loses focus after being
edited.

``` javascript
username.addEventListener("change", (event) => {
  console.log("Final:", event.target.value);
});
```

Output after typing `cat` and clicking elsewhere:

``` text
Final: cat
```

## `focus` vs `blur`

### `focus`

Occurs when an element gains focus.

``` javascript
email.addEventListener("focus", () => {
  console.log("Focused");
});
```

### `blur`

Occurs when an element loses focus.

``` javascript
email.addEventListener("blur", () => {
  console.log("Lost focus");
});
```

## `focusin` vs `focusout`

Unlike `focus` and `blur`, these events bubble, making them useful for
event delegation.

## Real-Time Validation

``` javascript
password.addEventListener("input", (event) => {
  if (event.target.value.length < 8) {
    console.log("Password too short");
  } else {
    console.log("Looks good");
  }
});
```

## Best Practices

-   Use `input` for live feedback.
-   Use `change` for finalized values.
-   Validate after `blur` to reduce distractions.
-   Prefer delegated `focusin`/`focusout` when managing many fields.

## Continue

Next file:

**DOM-Events-Part-11-Section-3.md**
