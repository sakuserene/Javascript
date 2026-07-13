---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 2: Creating Products and Validating Form Data"
difficulty: "Beginner to Intermediate"
estimated_time: "120–150 minutes"
prerequisites:

* "Product Inventory architecture and rendering"
* "Reading form values"
* "JavaScript objects"
* "Array push()"
* "Unique ID generation"
* "Basic form validation"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 1: Architecture, State, HTML, CSS, and Rendering"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 3: Editing and Updating Products"

---

# Building a Complete Product Inventory with Plain Loops

## Part 2: Creating Products and Validating Form Data

## Course Navigation

| Direction         | Link                                                                                                               |
| ----------------- | ------------------------------------------------------------------------------------------------------------------ |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                  |
| Previous Subtopic | [Architecture, State, HTML, CSS, and Rendering](./02-product-inventory-part-1-architecture-state-and-rendering.md) |
| Next Subtopic     | [Editing and Updating Products](./02-product-inventory-part-3-editing-and-updating.md)                             |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                  |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 2

---

## Learning Objectives

By the end of this part, you should be able to:

* Read product values from form inputs.
* Explain why input values initially arrive as strings.
* Trim and normalize text input.
* Convert price and quantity into numbers.
* Validate required fields.
* Reject invalid prices.
* Reject negative prices.
* Reject invalid quantities.
* Reject decimal quantities.
* Reject negative quantities.
* Detect duplicate product names with a plain loop.
* Create a new product object.
* Generate a unique product ID.
* Add a product to state with `push()`.
* Reset the form after successful creation.
* Re-render the DOM after state changes.
* Display success and validation messages.
* Prevent invalid data from entering application state.
* Explain the complete Create flow.

---

## Prerequisites

You should already understand:

* The Product Inventory state structure.
* DOM element selection.
* Standard `for` loops.
* Functions.
* Form submission events.
* `preventDefault()`.
* Arrays of objects.
* `push()`.
* Reusable rendering functions.

---

## What You Will Build

You will make the Product Inventory form functional.

The user will be able to enter:

```text
Product Name: Monitor
Price: 18000
Quantity: 3
```

After submission, the application state will become:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
  {
    id: 4,
    name: "Monitor",
    price: 18000,
    quantity: 3,
  },
]
```

The DOM and summary cards will then re-render from the updated array.

---

# 52. Understanding the Product Create Flow

## 52.1 What You Will Learn

Creating a product is not only:

```js
products.push(newProduct);
```

A complete Create operation includes:

```text
User submits the form
        ↓
Prevent page reload
        ↓
Read form values
        ↓
Normalize values
        ↓
Validate all fields
        ↓
Check duplicate names
        ↓
Generate a unique ID
        ↓
Create a fresh object
        ↓
Push object into state
        ↓
Reset form
        ↓
Re-render products
        ↓
Display success feedback
```

Every stage protects the application from invalid or inconsistent state.

---

# 53. Listening for Form Submission

Use the existing form element:

```js
const productForm =
  document.getElementById(
    "product-form"
  );
```

Attach a submit listener:

```js
productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);
```

The handler will coordinate the Create operation.

---

# 54. Preventing the Default Form Behavior

A normal HTML form submission reloads or navigates the page.

For a JavaScript-controlled form:

```js
function handleProductFormSubmit(
  event
) {
  event.preventDefault();
}
```

## Why `preventDefault()` Matters

Without it:

* The browser may reload.
* JavaScript state may be lost.
* Feedback messages may disappear.
* The DOM may reset before the user sees the result.

---

# 55. Reading Form Values

## 55.1 The Problem Being Solved

The product form contains:

```html
<input id="product-name" />
<input id="product-price" />
<input id="product-quantity" />
```

JavaScript must read the current user-entered values.

---

## 55.2 Reusable Form-Reading Function

```js
function readProductForm() {
  return {
    name:
      productNameInput.value.trim(),

    priceText:
      productPriceInput.value.trim(),

    quantityText:
      productQuantityInput.value.trim(),
  };
}
```

This function returns raw form data.

Example result:

```js
{
  name: "Monitor",
  priceText: "18000",
  quantityText: "3",
}
```

---

# 56. Why Numeric Inputs Still Produce Strings

Even when HTML uses:

```html
<input type="number" />
```

the `.value` property returns text.

Example:

```js
productPriceInput.value
```

may return:

```text
"18000"
```

not:

```text
18000
```

Therefore, conversion is required before calculations and numeric validation.

---

# 57. Why Text Is Trimmed

```js
productNameInput.value.trim();
```

Input:

```text
"   Monitor   "
```

Result:

```text
"Monitor"
```

This prevents names consisting only of spaces and improves duplicate detection.

---

# 58. Validation Strategy

A useful validator returns an object.

Invalid result:

```js
{
  isValid: false,
  message:
    "Product name is required.",
  field: "name",
}
```

Valid result:

```js
{
  isValid: true,
  message: "",
  field: null,
  normalizedData: {
    name: "Monitor",
    price: 18000,
    quantity: 3,
  },
}
```

This structure gives the submit handler enough information to:

* Stop invalid creation.
* Show a message.
* Focus the relevant field.
* Use normalized data after success.

---

# 59. Validating the Product Name

## 59.1 Required Name

```js
if (data.name === "") {
  return {
    isValid: false,
    message:
      "Product name is required.",
    field: "name",
  };
}
```

---

## 59.2 Minimum Length

```js
if (data.name.length < 2) {
  return {
    isValid: false,
    message:
      "Product name must contain at least 2 characters.",
    field: "name",
  };
}
```

This rejects values such as:

```text
A
```

---

## 59.3 Maximum Length

The HTML already uses:

```html
maxlength="80"
```

JavaScript should still validate:

```js
if (data.name.length > 80) {
  return {
    isValid: false,
    message:
      "Product name cannot exceed 80 characters.",
    field: "name",
  };
}
```

HTML validation improves the interface.

JavaScript validation protects state logic.

---

# 60. Detecting Duplicate Product Names with a Plain Loop

## 60.1 The Problem

Suppose state contains:

```js
{
  id: 1,
  name: "Laptop",
}
```

The user enters:

```text
laptop
```

Without normalization, JavaScript may treat these as different strings.

For this inventory, they should be considered duplicates.

---

## 60.2 Duplicate Search Function

```js
function productNameExists(
  name,
  ignoredProductId = null
) {
  const normalizedName =
    name.trim().toLowerCase();

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    if (
      product.id ===
      ignoredProductId
    ) {
      continue;
    }

    const existingName =
      product.name
        .trim()
        .toLowerCase();

    if (
      existingName ===
      normalizedName
    ) {
      return true;
    }
  }

  return false;
}
```

---

# 61. Duplicate Function Syntax Breakdown

## `name`

The proposed product name.

## `ignoredProductId`

Used later during editing so a product does not detect itself as a duplicate.

During Create:

```js
ignoredProductId === null;
```

## `toLowerCase()`

Makes comparison case-insensitive.

## `continue`

Skips one product when its ID should be ignored.

## `return true`

Stops immediately when a duplicate is found.

## `return false`

Runs only after every product has been checked without finding a duplicate.

---

# 62. Duplicate Validation Rule

```js
if (
  productNameExists(
    data.name
  )
) {
  return {
    isValid: false,
    message:
      "A product with this name already exists.",
    field: "name",
  };
}
```

In Create mode, no ID is ignored.

---

# 63. Validating Price

## 63.1 Required Price

```js
if (data.priceText === "") {
  return {
    isValid: false,
    message:
      "Price is required.",
    field: "price",
  };
}
```

---

## 63.2 Converting Price

```js
const price =
  Number(data.priceText);
```

Examples:

```js
Number("18000");
```

Result:

```text
18000
```

```js
Number("15.5");
```

Result:

```text
15.5
```

---

## 63.3 Rejecting Invalid Numbers

```js
if (
  !Number.isFinite(price)
) {
  return {
    isValid: false,
    message:
      "Price must be a valid number.",
    field: "price",
  };
}
```

`Number.isFinite()` rejects:

* `NaN`
* `Infinity`
* `-Infinity`

---

## 63.4 Rejecting Negative Price

```js
if (price < 0) {
  return {
    isValid: false,
    message:
      "Price cannot be negative.",
    field: "price",
  };
}
```

A price of zero may be valid if the application allows free products.

---

# 64. Currency Precision Consideration

This beginner application stores:

```js
price: 18000
```

or:

```js
price: 18000.5
```

JavaScript floating-point arithmetic can have precision issues for decimal currency.

For production financial systems, a safer design may store the smallest unit:

```js
priceInCents
```

or for Kenyan shillings and cents:

```js
priceInMinorUnits
```

For this lesson, normal JavaScript numbers are sufficient.

---

# 65. Validating Quantity

## 65.1 Required Quantity

```js
if (data.quantityText === "") {
  return {
    isValid: false,
    message:
      "Quantity is required.",
    field: "quantity",
  };
}
```

---

## 65.2 Convert Quantity

```js
const quantity =
  Number(data.quantityText);
```

---

## 65.3 Require an Integer

```js
if (
  !Number.isInteger(quantity)
) {
  return {
    isValid: false,
    message:
      "Quantity must be a whole number.",
    field: "quantity",
  };
}
```

This rejects:

```text
2.5
```

---

## 65.4 Reject Negative Quantity

```js
if (quantity < 0) {
  return {
    isValid: false,
    message:
      "Quantity cannot be negative.",
    field: "quantity",
  };
}
```

Quantity zero is valid and means:

```text
Out of stock
```

---

# 66. Complete Validation Function

```js
function validateProductData(
  data
) {
  if (data.name === "") {
    return {
      isValid: false,
      message:
        "Product name is required.",
      field: "name",
    };
  }

  if (data.name.length < 2) {
    return {
      isValid: false,
      message:
        "Product name must contain at least 2 characters.",
      field: "name",
    };
  }

  if (data.name.length > 80) {
    return {
      isValid: false,
      message:
        "Product name cannot exceed 80 characters.",
      field: "name",
    };
  }

  if (
    productNameExists(
      data.name,
      editingProductId
    )
  ) {
    return {
      isValid: false,
      message:
        "A product with this name already exists.",
      field: "name",
    };
  }

  if (data.priceText === "") {
    return {
      isValid: false,
      message:
        "Price is required.",
      field: "price",
    };
  }

  const price =
    Number(data.priceText);

  if (
    !Number.isFinite(price)
  ) {
    return {
      isValid: false,
      message:
        "Price must be a valid number.",
      field: "price",
    };
  }

  if (price < 0) {
    return {
      isValid: false,
      message:
        "Price cannot be negative.",
      field: "price",
    };
  }

  if (
    data.quantityText === ""
  ) {
    return {
      isValid: false,
      message:
        "Quantity is required.",
      field: "quantity",
    };
  }

  const quantity =
    Number(
      data.quantityText
    );

  if (
    !Number.isInteger(
      quantity
    )
  ) {
    return {
      isValid: false,
      message:
        "Quantity must be a whole number.",
      field: "quantity",
    };
  }

  if (quantity < 0) {
    return {
      isValid: false,
      message:
        "Quantity cannot be negative.",
      field: "quantity",
    };
  }

  return {
    isValid: true,
    message: "",
    field: null,
    normalizedData: {
      name: data.name,
      price,
      quantity,
    },
  };
}
```

---

# 67. Why Validation Uses `editingProductId`

The duplicate check calls:

```js
productNameExists(
  data.name,
  editingProductId
);
```

During Create:

```js
editingProductId === null;
```

No product is ignored.

During Update later:

```js
editingProductId === 2;
```

The product with ID `2` is ignored during its own duplicate check.

This lets one validation function support both modes.

---

# 68. Focusing the Invalid Field

Create:

```js
function focusInvalidField(
  field
) {
  if (field === "name") {
    productNameInput.focus();
    return;
  }

  if (field === "price") {
    productPriceInput.focus();
    return;
  }

  if (field === "quantity") {
    productQuantityInput.focus();
  }
}
```

This helps the user correct the right value quickly.

---

# 69. Generating Product IDs

Existing state:

```js
let nextProductId = 4;
```

Generator:

```js
function generateProductId() {
  const id = nextProductId;

  nextProductId++;

  return id;
}
```

The generator should run only after validation succeeds.

---

# 70. Creating a Fresh Product Object

```js
function createProduct(
  data
) {
  const newProduct = {
    id: generateProductId(),
    name: data.name,
    price: data.price,
    quantity: data.quantity,
  };

  products.push(
    newProduct
  );

  return newProduct;
}
```

---

# 71. Why a Fresh Object Is Required

Correct:

```js
const newProduct = {
  id: generateProductId(),
  name: data.name,
  price: data.price,
  quantity: data.quantity,
};
```

This creates a different object for every submission.

Incorrect:

```js
const reusableProduct = {};

function createProduct(data) {
  reusableProduct.name =
    data.name;

  products.push(
    reusableProduct
  );
}
```

Every array entry may reference the same object.

---

# 72. Resetting the Form After Creation

```js
function resetProductForm() {
  productForm.reset();

  productNameInput.focus();
}
```

This changes only form UI state.

It does not clear:

```js
products
```

---

# 73. General Create-Mode Reset Function

Because the same form will later support editing, create:

```js
function resetFormToCreateMode() {
  editingProductId = null;

  productForm.reset();

  productFormTitle.textContent =
    "Add Product";

  submitButton.textContent =
    "Add Product";

  cancelEditButton.hidden =
    true;

  productNameInput.focus();
}
```

This function can be used after:

* Successful Create.
* Successful Update.
* Cancel Edit.
* Deleting the actively edited product.

---

# 74. Complete Create Handler

```js
function handleCreateProduct(
  data
) {
  const newProduct =
    createProduct(data);

  resetFormToCreateMode();

  renderProducts();

  showSuccess(
    `${newProduct.name} was added successfully.`
  );
}
```

The order is:

1. Create state.
2. Reset form mode.
3. Render current state.
4. Show feedback.

---

# 75. Complete Form Submit Handler for Create Mode

```js
function handleProductFormSubmit(
  event
) {
  event.preventDefault();

  clearMessages();

  const rawData =
    readProductForm();

  const validationResult =
    validateProductData(
      rawData
    );

  if (
    !validationResult.isValid
  ) {
    showError(
      validationResult.message
    );

    focusInvalidField(
      validationResult.field
    );

    return;
  }

  if (
    editingProductId === null
  ) {
    handleCreateProduct(
      validationResult.normalizedData
    );

    return;
  }

  showInfo(
    "Update functionality will be implemented in the next part."
  );
}
```

The edit branch is temporarily a placeholder.

---

# 76. Why the Submit Handler Checks Form Mode

The form will eventually perform two different operations.

```js
if (
  editingProductId === null
)
```

means:

```text
Create a new product
```

Otherwise:

```text
Update an existing product
```

Without this mode check, submitting during editing could accidentally create duplicates.

---

# 77. Reset Button Behavior

The HTML includes:

```html
<button
  id="reset-button"
  type="button"
>
  Reset Form
</button>
```

Handler:

```js
resetButton.addEventListener(
  "click",
  function () {
    clearMessages();

    resetFormToCreateMode();

    renderProducts();

    showInfo(
      "The product form was reset."
    );
  }
);
```

Rendering is useful because it removes any editing highlight.

---

# 78. Why Reset Is Different from Clearing State

Reset form:

```js
productForm.reset();
```

Clear state:

```js
products = [];
```

These are entirely different actions.

The Reset Form button should not delete inventory data.

---

# 79. Preventing Duplicate Rendering

After Create:

```js
renderProducts();
```

The render function begins with:

```js
productList.innerHTML = "";
```

This ensures the newly updated array is displayed exactly once.

---

# 80. Summary Recalculation After Creation

Suppose Monitor is added:

```js
{
  price: 18000,
  quantity: 3,
}
```

Its inventory value is:

```text
54,000
```

Previous inventory value:

```text
225,000
```

New total:

```text
279,000
```

Because `renderProducts()` calls:

```js
renderSummary();
```

all summary values refresh automatically.

---

# 81. Complete Part 2 JavaScript Additions

Add these functions to the Part 1 `app.js`:

```js
function generateProductId() {
  const id = nextProductId;

  nextProductId++;

  return id;
}

function productNameExists(
  name,
  ignoredProductId = null
) {
  const normalizedName =
    name.trim().toLowerCase();

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    if (
      product.id ===
      ignoredProductId
    ) {
      continue;
    }

    const existingName =
      product.name
        .trim()
        .toLowerCase();

    if (
      existingName ===
      normalizedName
    ) {
      return true;
    }
  }

  return false;
}

function readProductForm() {
  return {
    name:
      productNameInput.value.trim(),

    priceText:
      productPriceInput.value.trim(),

    quantityText:
      productQuantityInput.value.trim(),
  };
}

function validateProductData(
  data
) {
  if (data.name === "") {
    return {
      isValid: false,
      message:
        "Product name is required.",
      field: "name",
    };
  }

  if (data.name.length < 2) {
    return {
      isValid: false,
      message:
        "Product name must contain at least 2 characters.",
      field: "name",
    };
  }

  if (data.name.length > 80) {
    return {
      isValid: false,
      message:
        "Product name cannot exceed 80 characters.",
      field: "name",
    };
  }

  if (
    productNameExists(
      data.name,
      editingProductId
    )
  ) {
    return {
      isValid: false,
      message:
        "A product with this name already exists.",
      field: "name",
    };
  }

  if (data.priceText === "") {
    return {
      isValid: false,
      message:
        "Price is required.",
      field: "price",
    };
  }

  const price =
    Number(data.priceText);

  if (
    !Number.isFinite(price)
  ) {
    return {
      isValid: false,
      message:
        "Price must be a valid number.",
      field: "price",
    };
  }

  if (price < 0) {
    return {
      isValid: false,
      message:
        "Price cannot be negative.",
      field: "price",
    };
  }

  if (
    data.quantityText === ""
  ) {
    return {
      isValid: false,
      message:
        "Quantity is required.",
      field: "quantity",
    };
  }

  const quantity =
    Number(
      data.quantityText
    );

  if (
    !Number.isInteger(
      quantity
    )
  ) {
    return {
      isValid: false,
      message:
        "Quantity must be a whole number.",
      field: "quantity",
    };
  }

  if (quantity < 0) {
    return {
      isValid: false,
      message:
        "Quantity cannot be negative.",
      field: "quantity",
    };
  }

  return {
    isValid: true,
    message: "",
    field: null,
    normalizedData: {
      name: data.name,
      price,
      quantity,
    },
  };
}

function focusInvalidField(
  field
) {
  if (field === "name") {
    productNameInput.focus();
    return;
  }

  if (field === "price") {
    productPriceInput.focus();
    return;
  }

  if (field === "quantity") {
    productQuantityInput.focus();
  }
}

function createProduct(
  data
) {
  const newProduct = {
    id: generateProductId(),
    name: data.name,
    price: data.price,
    quantity: data.quantity,
  };

  products.push(
    newProduct
  );

  return newProduct;
}

function resetFormToCreateMode() {
  editingProductId = null;

  productForm.reset();

  productFormTitle.textContent =
    "Add Product";

  submitButton.textContent =
    "Add Product";

  cancelEditButton.hidden =
    true;

  productNameInput.focus();
}

function handleCreateProduct(
  data
) {
  const newProduct =
    createProduct(data);

  resetFormToCreateMode();

  renderProducts();

  showSuccess(
    `${newProduct.name} was added successfully.`
  );
}

function handleProductFormSubmit(
  event
) {
  event.preventDefault();

  clearMessages();

  const rawData =
    readProductForm();

  const validationResult =
    validateProductData(
      rawData
    );

  if (
    !validationResult.isValid
  ) {
    showError(
      validationResult.message
    );

    focusInvalidField(
      validationResult.field
    );

    return;
  }

  if (
    editingProductId === null
  ) {
    handleCreateProduct(
      validationResult.normalizedData
    );

    return;
  }

  showInfo(
    "Update functionality will be implemented in the next part."
  );
}

productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);

resetButton.addEventListener(
  "click",
  function () {
    clearMessages();

    resetFormToCreateMode();

    renderProducts();

    showInfo(
      "The product form was reset."
    );
  }
);
```

Keep the existing initial call:

```js
renderProducts();
```

---

# 82. Expected Create Example

Initial summary:

```text
Total Products: 3
Total Units: 7
Inventory Value: KSh 225,000
Out of Stock: 1
```

User submits:

```text
Name: Monitor
Price: 18000
Quantity: 3
```

New state:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
  {
    id: 4,
    name: "Monitor",
    price: 18000,
    quantity: 3,
  },
]
```

Updated summary:

```text
Total Products: 4
Total Units: 10
Inventory Value: KSh 279,000
Out of Stock: 1
```

Feedback:

```text
Monitor was added successfully.
```

---

# 83. Invalid Input Examples

## Blank Name

Input:

```text
Name:
Price: 1000
Quantity: 2
```

Output:

```text
Product name is required.
```

State does not change.

---

## Duplicate Name

Existing:

```text
Laptop
```

Input:

```text
 laptop
```

Output:

```text
A product with this name already exists.
```

State does not change.

---

## Blank Price

Output:

```text
Price is required.
```

---

## Negative Price

Input:

```text
-500
```

Output:

```text
Price cannot be negative.
```

---

## Decimal Quantity

Input:

```text
2.5
```

Output:

```text
Quantity must be a whole number.
```

---

## Negative Quantity

Input:

```text
-2
```

Output:

```text
Quantity cannot be negative.
```

---

# 84. Three Complete Examples

## Example 1: Validate and Create Without the DOM

### Problem

Validate plain data and add a product to an array.

### Input Data

```js
{
  name: "Monitor",
  price: 18000,
  quantity: 3
}
```

### Complete Code

```js
let products = [];
let nextProductId = 1;

function generateProductId() {
  const id = nextProductId;

  nextProductId++;

  return id;
}

function productNameExists(
  name
) {
  const normalizedName =
    name.trim().toLowerCase();

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].name
        .trim()
        .toLowerCase() ===
      normalizedName
    ) {
      return true;
    }
  }

  return false;
}

function createProduct(
  name,
  price,
  quantity
) {
  const trimmedName =
    name.trim();

  if (trimmedName === "") {
    return null;
  }

  if (
    productNameExists(
      trimmedName
    )
  ) {
    return null;
  }

  if (
    !Number.isFinite(price) ||
    price < 0
  ) {
    return null;
  }

  if (
    !Number.isInteger(
      quantity
    ) ||
    quantity < 0
  ) {
    return null;
  }

  const product = {
    id: generateProductId(),
    name: trimmedName,
    price,
    quantity,
  };

  products.push(product);

  return product;
}

const result =
  createProduct(
    "Monitor",
    18000,
    3
  );

console.log(result);
console.log(products);
```

### Expected Console Output

```text
{
  id: 1,
  name: "Monitor",
  price: 18000,
  quantity: 3
}

[
  {
    id: 1,
    name: "Monitor",
    price: 18000,
    quantity: 3
  }
]
```

### Final State

One valid product exists.

### Common Mistakes

* Generating an ID before validation.
* Adding string numeric values.
* Forgetting to trim the name.
* Reusing the same object.

### Small Modification Challenge

Call the function again with:

```text
" monitor "
```

Confirm that the duplicate is rejected.

---

## Example 2: Duplicate Checking with a Plain Loop

### Problem

Check product names case-insensitively.

### Input Data

```js
const products = [
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 2,
    name: "Phone",
  },
];
```

### Complete Code

```js
const products = [
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 2,
    name: "Phone",
  },
];

function productNameExists(
  name
) {
  const normalizedName =
    name.trim().toLowerCase();

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const normalizedExistingName =
      products[index].name
        .trim()
        .toLowerCase();

    if (
      normalizedExistingName ===
      normalizedName
    ) {
      return true;
    }
  }

  return false;
}

console.log(
  productNameExists(
    " laptop "
  )
);

console.log(
  productNameExists(
    "Monitor"
  )
);
```

### Expected Console Output

```text
true
false
```

### Final State

The array remains unchanged.

### Common Mistakes

* Lowercasing only one value.
* Returning `false` inside the loop after the first mismatch.
* Comparing IDs instead of names.

### Small Modification Challenge

Modify the function to ignore one product ID during editing.

---

## Example 3: Browser Create Flow

### Problem

Create products from a form and re-render the list.

### Complete Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />

  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />

  <title>Create Products</title>
</head>

<body>
  <h1>Product Inventory</h1>

  <form id="product-form">
    <input
      id="product-name"
      type="text"
      placeholder="Product name"
    />

    <input
      id="product-price"
      type="number"
      placeholder="Price"
    />

    <input
      id="product-quantity"
      type="number"
      placeholder="Quantity"
    />

    <button type="submit">
      Add Product
    </button>
  </form>

  <p id="error-message"></p>
  <p id="success-message"></p>
  <p id="product-count"></p>

  <ul id="product-list"></ul>

  <script>
    let products = [];
    let nextProductId = 1;

    const productForm =
      document.getElementById(
        "product-form"
      );

    const productNameInput =
      document.getElementById(
        "product-name"
      );

    const productPriceInput =
      document.getElementById(
        "product-price"
      );

    const productQuantityInput =
      document.getElementById(
        "product-quantity"
      );

    const errorMessage =
      document.getElementById(
        "error-message"
      );

    const successMessage =
      document.getElementById(
        "success-message"
      );

    const productCount =
      document.getElementById(
        "product-count"
      );

    const productList =
      document.getElementById(
        "product-list"
      );

    function generateProductId() {
      const id =
        nextProductId;

      nextProductId++;

      return id;
    }

    function productNameExists(
      name
    ) {
      const normalizedName =
        name
          .trim()
          .toLowerCase();

      for (
        let index = 0;
        index < products.length;
        index++
      ) {
        if (
          products[index].name
            .trim()
            .toLowerCase() ===
          normalizedName
        ) {
          return true;
        }
      }

      return false;
    }

    function renderProducts() {
      productList.innerHTML = "";

      productCount.textContent =
        `Total products: ${products.length}`;

      if (
        products.length === 0
      ) {
        const emptyState =
          document.createElement("li");

        emptyState.textContent =
          "No products available.";

        productList.appendChild(
          emptyState
        );

        return;
      }

      for (
        let index = 0;
        index < products.length;
        index++
      ) {
        const product =
          products[index];

        const listItem =
          document.createElement("li");

        listItem.textContent =
          `${product.name} — ` +
          `KSh ${product.price.toLocaleString()} — ` +
          `Quantity: ${product.quantity}`;

        productList.appendChild(
          listItem
        );
      }
    }

    productForm.addEventListener(
      "submit",
      function (event) {
        event.preventDefault();

        errorMessage.textContent = "";
        successMessage.textContent = "";

        const name =
          productNameInput.value.trim();

        const priceText =
          productPriceInput.value.trim();

        const quantityText =
          productQuantityInput.value.trim();

        if (name === "") {
          errorMessage.textContent =
            "Product name is required.";

          productNameInput.focus();
          return;
        }

        if (
          productNameExists(name)
        ) {
          errorMessage.textContent =
            "A product with this name already exists.";

          productNameInput.focus();
          return;
        }

        const price =
          Number(priceText);

        if (
          priceText === "" ||
          !Number.isFinite(price) ||
          price < 0
        ) {
          errorMessage.textContent =
            "Enter a valid non-negative price.";

          productPriceInput.focus();
          return;
        }

        const quantity =
          Number(quantityText);

        if (
          quantityText === "" ||
          !Number.isInteger(
            quantity
          ) ||
          quantity < 0
        ) {
          errorMessage.textContent =
            "Enter a valid non-negative whole quantity.";

          productQuantityInput.focus();
          return;
        }

        const newProduct = {
          id: generateProductId(),
          name,
          price,
          quantity,
        };

        products.push(
          newProduct
        );

        productForm.reset();
        productNameInput.focus();

        renderProducts();

        successMessage.textContent =
          `${newProduct.name} was added successfully.`;
      }
    );

    renderProducts();
  </script>
</body>
</html>
```

### Expected Initial DOM

```text
Total products: 0

No products available.
```

### Expected DOM After Adding Monitor

```text
Monitor was added successfully.

Total products: 1

Monitor — KSh 18,000 — Quantity: 3
```

### Final State

```js
[
  {
    id: 1,
    name: "Monitor",
    price: 18000,
    quantity: 3,
  },
]
```

### Common Mistakes

* Forgetting `preventDefault()`.
* Reading values after resetting the form.
* Calling render before `push()`.
* Using `innerHTML` for the product name.

### Small Modification Challenge

Display the product's inventory value:

```text
KSh 54,000
```

---

# 85. Common Beginner Mistakes

## Mistake 1: State Declared Inside the Submit Handler

```js
function handleSubmit() {
  let products = [];
}
```

Every submission starts with an empty array.

---

## Mistake 2: Numeric Strings Stored in State

Incorrect:

```js
{
  price: "18000",
  quantity: "3",
}
```

Correct:

```js
{
  price: 18000,
  quantity: 3,
}
```

---

## Mistake 3: ID Generated Before Validation

Invalid submissions consume IDs unnecessarily.

---

## Mistake 4: Duplicate Check Returns False Too Early

Incorrect:

```js
for (...) {
  if (match) {
    return true;
  }

  return false;
}
```

This checks only the first product.

---

## Mistake 5: Decimal Quantity Accepted

Use:

```js
Number.isInteger(quantity)
```

---

## Mistake 6: Form Reset Before Creating the Object

The input values become empty.

---

## Mistake 7: Rendering Before `push()`

The new product is not displayed in that render.

---

## Mistake 8: Creating During Edit Mode

The shared submit handler must check `editingProductId`.

---

## Mistake 9: Reset Button Clears Products

The reset action should clear only form state.

---

## Mistake 10: Duplicate Names Compared Without Normalization

`"Laptop"` and `" laptop "` may be treated incorrectly as different products.

---

# 86. Beginner Questions

1. Why must form submission call `preventDefault()`?
2. What type does an input's `.value` return?
3. Why is the product name trimmed?
4. Why must price be converted?
5. Why must quantity be converted?
6. Why should quantity be an integer?
7. Is quantity zero valid?
8. What does a duplicate-name function return?
9. Why should names be compared in lowercase?
10. When should a unique ID be generated?
11. What does `push()` do?
12. Why should every Create submission make a fresh object?
13. Why should the form reset after success?
14. Why should rendering happen after `push()`?
15. Why should invalid data never enter state?

---

# 87. Intermediate Questions

1. Why should form reading and validation be separate functions?
2. Why should validation return a structured result?
3. Why should normalized numeric data be returned?
4. Why should the validator know the invalid field?
5. Why does the duplicate checker accept an ignored ID?
6. Why should the same validator support Create and Update?
7. Why should the ID generator have one responsibility?
8. Why should Reset Form return to Create mode?
9. Why should summary values automatically refresh after creation?
10. Why should the submit handler coordinate smaller functions?
11. Why is `Number.isFinite()` useful for price?
12. Why is `Number.isInteger()` useful for quantity?
13. Why should zero price and zero quantity be considered separately from blank input?
14. Why should state store raw numeric values rather than formatted currency?
15. Why should success feedback use the created object?

---

# 88. Advanced Questions

1. How should decimal currency be represented in a production financial application?
2. Why can floating-point values create rounding errors?
3. How could validation rules be made configurable?
4. How could state logic be tested independently of the DOM?
5. How would server-generated IDs change the Create flow?
6. What is optimistic creation?
7. How should the UI react when a server rejects creation?
8. How could asynchronous submission prevent duplicate clicks?
9. Why might validation exist on both client and server?
10. How could product names be normalized more robustly for international text?
11. How would immutable Create differ from `push()`?
12. How could form validation errors be displayed beside individual fields?

---

# 89. Output Prediction Questions

## Question 1

```js
console.log(
  typeof input.value
);
```

Assume `input` is an HTML number input.

What is normally printed?

---

## Question 2

```js
console.log(
  Number("18000")
);
```

Predict the output.

---

## Question 3

```js
console.log(
  Number.isInteger(3)
);

console.log(
  Number.isInteger(3.5)
);
```

Predict both outputs.

---

## Question 4

```js
const name =
  "  Laptop  "
    .trim()
    .toLowerCase();

console.log(name);
```

Predict the output.

---

## Question 5

```js
let nextId = 4;

const product = {
  id: nextId++,
};

console.log(
  product.id
);

console.log(
  nextId
);
```

Predict both outputs.

---

## Question 6

```js
const products = [];

products.push({
  id: 1,
});

console.log(
  products.length
);
```

Predict the output.

---

## Question 7

```js
const price =
  Number("");

console.log(price);
```

Why is checking the original text for an empty string still important?

---

## Question 8

```js
const quantity =
  Number("2.5");

console.log(
  Number.isInteger(
    quantity
  )
);
```

Predict the output.

---

# 90. Debugging Exercises

For every exercise:

1. Identify the bug.
2. Explain why it happens.
3. Correct the code.
4. State the expected result.

## Debugging Exercise 1: Page Reloads

```js
form.addEventListener(
  "submit",
  function (event) {
    createProduct();
  }
);
```

---

## Debugging Exercise 2: Numeric Strings Stored

```js
const product = {
  price:
    priceInput.value,
  quantity:
    quantityInput.value,
};
```

---

## Debugging Exercise 3: Duplicate Search Stops Early

```js
function nameExists(name) {
  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].name ===
      name
    ) {
      return true;
    }

    return false;
  }
}
```

---

## Debugging Exercise 4: Negative Quantity Allowed

```js
if (
  Number.isInteger(quantity)
) {
  createProduct();
}
```

---

## Debugging Exercise 5: ID Generated Before Validation

```js
const id =
  generateProductId();

if (name === "") {
  return;
}
```

---

## Debugging Exercise 6: Render Before Push

```js
renderProducts();

products.push(
  newProduct
);
```

---

## Debugging Exercise 7: Reset Deletes State

```js
resetButton.addEventListener(
  "click",
  function () {
    products = [];
  }
);
```

---

## Debugging Exercise 8: Duplicate Name Is Case-Sensitive

```js
if (
  product.name ===
  newName
) {
  return true;
}
```

---

# 91. Coding Exercises

## Exercise 1: Read Form Data

Create:

```js
readProductForm();
```

---

## Exercise 2: Validate Product Name

Reject blank, short, and long names.

---

## Exercise 3: Detect Duplicate Names

Use a plain loop.

---

## Exercise 4: Validate Price

Reject blank, invalid, and negative values.

---

## Exercise 5: Validate Quantity

Reject blank, decimal, and negative values.

---

## Exercise 6: Generate IDs

Create a reusable counter-based generator.

---

## Exercise 7: Create Fresh Objects

Return a new product object every time.

---

## Exercise 8: Push Into State

Add valid products to the array.

---

## Exercise 9: Reset the Form

Return to Create mode without clearing products.

---

## Exercise 10: Re-render After Creation

Refresh cards and summaries.

---

## Exercise 11: Show Validation Feedback

Focus the invalid field.

---

## Exercise 12: Build the Full Create Flow

Connect form submission to state and rendering.

---

# 92. Refactoring Exercises

## Exercise 1

Move raw value reading into:

```js
readProductForm();
```

## Exercise 2

Move all validation into:

```js
validateProductData(data);
```

## Exercise 3

Move duplicate checking into:

```js
productNameExists(
  name,
  ignoredId
);
```

## Exercise 4

Move object creation into:

```js
createProduct(data);
```

## Exercise 5

Move Create coordination into:

```js
handleCreateProduct(data);
```

## Exercise 6

Replace repeated focus logic with:

```js
focusInvalidField(field);
```

## Exercise 7

Replace repeated mode reset statements with:

```js
resetFormToCreateMode();
```

## Exercise 8

Split one large submit handler into smaller functions.

---

# 93. Conceptual Questions

1. Why should form values be treated as untrusted input?
2. Why should invalid data be stopped before state mutation?
3. Why is trimming important?
4. Why are input values strings?
5. Why should numeric conversion happen before calculations?
6. Why is blank input different from numeric zero?
7. Why should duplicate names be normalized?
8. Why should Create generate a new ID?
9. Why should Update later preserve the old ID?
10. Why should object creation use a fresh object?
11. Why should the array be updated before rendering?
12. Why should summaries be recalculated from state?
13. Why should the form reset only after success?
14. Why should validation return normalized data?
15. How does this Create flow prepare the application for Update?

---

# 94. Part 2 Completion Task

Continue working inside:

```text
product-inventory-plain-loops/
```

Update:

```text
app.js
```

Required new functions:

```js
generateProductId();
productNameExists(
  name,
  ignoredProductId
);
readProductForm();
validateProductData(data);
focusInvalidField(field);
createProduct(data);
resetFormToCreateMode();
handleCreateProduct(data);
handleProductFormSubmit(event);
```

Required event listeners:

```js
productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);

resetButton.addEventListener(
  "click",
  ...
);
```

Requirements:

* [ ] Prevent default form submission.
* [ ] Read all product values.
* [ ] Trim the product name.
* [ ] Reject an empty name.
* [ ] Reject names shorter than 2 characters.
* [ ] Reject names longer than 80 characters.
* [ ] Reject duplicate names.
* [ ] Make duplicate checking case-insensitive.
* [ ] Reject blank price.
* [ ] Convert price to a number.
* [ ] Reject invalid price.
* [ ] Reject negative price.
* [ ] Allow zero price.
* [ ] Reject blank quantity.
* [ ] Convert quantity to a number.
* [ ] Reject decimal quantity.
* [ ] Reject negative quantity.
* [ ] Allow zero quantity.
* [ ] Generate an ID only after validation.
* [ ] Create a fresh product object.
* [ ] Add the product with `push()`.
* [ ] Reset the form after success.
* [ ] Return the form to Create mode.
* [ ] Re-render the list.
* [ ] Refresh all summaries.
* [ ] Show a success message.
* [ ] Focus invalid inputs.
* [ ] Reset Form must not delete state.
* [ ] Keep the Update branch as a placeholder.

Manual testing checklist:

* [ ] Add Monitor for KSh 18,000 with quantity 3.
* [ ] Reject blank name.
* [ ] Reject one-character name.
* [ ] Reject duplicate `Laptop`.
* [ ] Reject duplicate `laptop`.
* [ ] Reject blank price.
* [ ] Reject negative price.
* [ ] Accept zero price.
* [ ] Reject blank quantity.
* [ ] Reject quantity `2.5`.
* [ ] Reject negative quantity.
* [ ] Accept quantity zero.
* [ ] Confirm IDs increment only after successful creation.
* [ ] Confirm repeated renders do not duplicate cards.
* [ ] Confirm Reset Form leaves products unchanged.

Expected state after adding Monitor:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
  {
    id: 4,
    name: "Monitor",
    price: 18000,
    quantity: 3,
  },
]
```

Expected summary:

```text
Total Products: 4
Total Units: 10
Inventory Value: KSh 279,000
Out of Stock: 1
```

Expected feedback:

```text
Monitor was added successfully.
```

---

## Key Takeaways

* Create begins with a form-submit event.
* `preventDefault()` keeps the JavaScript application active without a page reload.
* Input values are initially strings.
* Text should be trimmed and numeric values converted.
* Validation must happen before ID generation and state mutation.
* Duplicate names can be detected with a standard `for` loop.
* Product names should be compared after trimming and lowercasing.
* Price should be a valid non-negative number.
* Quantity should be a non-negative integer.
* Every successful Create operation should produce a fresh object.
* `push()` mutates the existing state array.
* The form should reset only after successful creation.
* Re-rendering refreshes the list and all derived summaries.
* The next part will implement Edit mode and update existing products using a standard `for` loop.

---

## Continue Learning

| Direction         | Link                                                                                                               |
| ----------------- | ------------------------------------------------------------------------------------------------------------------ |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                  |
| Previous Subtopic | [Architecture, State, HTML, CSS, and Rendering](./02-product-inventory-part-1-architecture-state-and-rendering.md) |
| Next Subtopic     | [Editing and Updating Products](./02-product-inventory-part-3-editing-and-updating.md)                             |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                  |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 2

When you are ready to continue, write:

```text
Next
```

