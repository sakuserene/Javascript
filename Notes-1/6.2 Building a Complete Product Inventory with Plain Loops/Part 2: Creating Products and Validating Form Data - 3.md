---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 2: Creating Products and Validating Form Data"
difficulty: "Beginner to Intermediate"
estimated_time: "120–150 minutes"
prerequisites:

* "Product Inventory architecture"
* "Arrays of objects"
* "Form submit events"
* "Input validation"
* "Rendering with plain loops"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 1: Architecture, State, HTML, CSS, and Rendering"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 3: Updating Products"

---

# Building a Complete Product Inventory with Plain Loops

## Part 2: Creating Products and Validating Form Data

## Course Navigation

| Direction         | Link                                                                                                                |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                   |
| Previous Subtopic | [Product Inventory Part 1: Architecture and Rendering](./02-product-inventory-part-1-architecture-and-rendering.md) |
| Next Subtopic     | [Product Inventory Part 3: Updating Products](./02-product-inventory-part-3-updating-products.md)                   |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                   |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 2

---

## Learning Objectives

By the end of this part, you should be able to:

* Read product values from form inputs.
* Trim and normalize product names.
* Convert price and quantity strings into numbers.
* Validate empty fields.
* Reject invalid prices.
* Reject negative prices.
* Reject invalid quantities.
* Reject decimal quantities.
* Check for duplicate product names using a plain loop.
* Generate a unique product ID.
* Create a fresh product object.
* Add a product using `push()`.
* Reset the form after successful creation.
* Re-render the inventory after adding a product.
* Display useful validation and success messages.
* Keep invalid data out of application state.

---

# 52. Understanding the Product Create Flow

## 52.1 What You Will Learn

The Product Inventory form collects three values:

```text
Product name
Price
Quantity
```

A complete Create flow is:

```text
User enters values
        ↓
User submits the form
        ↓
preventDefault() stops page reload
        ↓
Read form values
        ↓
Normalize values
        ↓
Validate values
        ↓
Check duplicate name
        ↓
Generate unique ID
        ↓
Create product object
        ↓
Push object into products
        ↓
Reset form
        ↓
Render updated products
        ↓
Show success message
```

---

# 53. Form Values Are Strings

Even number inputs return strings through `.value`.

HTML:

```html
<input
  id="product-price"
  type="number"
/>
```

JavaScript:

```js
const priceValue =
  productPriceInput.value;
```

If the user enters:

```text
50000
```

the value is:

```js
"50000"
```

not:

```js
50000
```

You must convert it before storing it in state.

---

# 54. Reading Product Form Values

Create:

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

This function only reads data.

It does not:

* Validate.
* Convert.
* Create a product.
* Update state.
* Render.

---

# 55. Why Keep Raw Numeric Text Initially?

Suppose the price field is empty.

```js
productPriceInput.value
```

returns:

```js
""
```

If you convert immediately:

```js
Number("")
```

the result is:

```text
0
```

That can hide the difference between:

```text
No value entered
```

and:

```text
The user entered zero
```

Keeping:

```js
priceText
```

allows explicit empty-field validation first.

---

# 56. Product Name Validation

## 56.1 Empty Name

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

## 56.2 Minimum Length

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

---

## 56.3 Maximum Length

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

---

# 57. Duplicate Product Names

## 57.1 Why Check Duplicates?

Suppose state already contains:

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

Without normalization, the application may treat them as different.

For this application, product names should be unique regardless of:

* Capitalization.
* Leading spaces.
* Trailing spaces.

---

## 57.2 Duplicate Check with a Plain Loop

```js
function productNameExists(
  productName,
  ignoredProductId = null
) {
  const normalizedName =
    productName
      .trim()
      .toLowerCase();

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

For Create:

```js
productNameExists(data.name);
```

No ID is ignored.

The ignored-ID parameter will become useful during Update.

---

# 58. Price Validation

## 58.1 Empty Price

```js
if (data.priceText === "") {
  return {
    isValid: false,
    message:
      "Product price is required.",
    field: "price",
  };
}
```

---

## 58.2 Convert Price

```js
const price =
  Number(data.priceText);
```

Examples:

```js
Number("50000");
```

returns:

```text
50000
```

```js
Number("2499.50");
```

returns:

```text
2499.5
```

---

## 58.3 Reject Invalid Price

```js
if (
  Number.isNaN(price) ||
  !Number.isFinite(price)
) {
  return {
    isValid: false,
    message:
      "Product price must be a valid number.",
    field: "price",
  };
}
```

---

## 58.4 Reject Negative Price

```js
if (price < 0) {
  return {
    isValid: false,
    message:
      "Product price cannot be negative.",
    field: "price",
  };
}
```

A price of zero may be allowed depending on business rules.

For this lesson:

```text
0 is valid
```

---

# 59. Quantity Validation

## 59.1 Empty Quantity

```js
if (data.quantityText === "") {
  return {
    isValid: false,
    message:
      "Product quantity is required.",
    field: "quantity",
  };
}
```

---

## 59.2 Convert Quantity

```js
const quantity =
  Number(data.quantityText);
```

---

## 59.3 Require a Whole Number

```js
if (
  !Number.isInteger(quantity)
) {
  return {
    isValid: false,
    message:
      "Product quantity must be a whole number.",
    field: "quantity",
  };
}
```

Reject:

```text
2.5
3.7
```

Accept:

```text
0
1
2
10
```

---

## 59.4 Reject Negative Quantity

```js
if (quantity < 0) {
  return {
    isValid: false,
    message:
      "Product quantity cannot be negative.",
    field: "quantity",
  };
}
```

---

# 60. Complete Validation Function

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
        "Product price is required.",
      field: "price",
    };
  }

  const price =
    Number(data.priceText);

  if (
    Number.isNaN(price) ||
    !Number.isFinite(price)
  ) {
    return {
      isValid: false,
      message:
        "Product price must be a valid number.",
      field: "price",
    };
  }

  if (price < 0) {
    return {
      isValid: false,
      message:
        "Product price cannot be negative.",
      field: "price",
    };
  }

  if (
    data.quantityText === ""
  ) {
    return {
      isValid: false,
      message:
        "Product quantity is required.",
      field: "quantity",
    };
  }

  const quantity =
    Number(data.quantityText);

  if (
    !Number.isInteger(quantity)
  ) {
    return {
      isValid: false,
      message:
        "Product quantity must be a whole number.",
      field: "quantity",
    };
  }

  if (quantity < 0) {
    return {
      isValid: false,
      message:
        "Product quantity cannot be negative.",
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

# 61. Validation Result Structure

Invalid result:

```js
{
  isValid: false,
  message:
    "Product price is required.",
  field: "price"
}
```

Valid result:

```js
{
  isValid: true,
  message: "",
  field: null,
  normalizedData: {
    name: "Laptop",
    price: 50000,
    quantity: 2
  }
}
```

The normalized data is ready to store in state.

---

# 62. Focusing the Invalid Field

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

This improves usability.

---

# 63. Generating Product IDs

```js
function generateProductId() {
  const productId =
    nextProductId;

  nextProductId++;

  return productId;
}
```

Call this only after validation succeeds.

Incorrect:

```js
const id =
  generateProductId();

if (!result.isValid) {
  return;
}
```

Correct:

```js
if (!result.isValid) {
  return;
}

const id =
  generateProductId();
```

---

# 64. Creating a Product Object

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

A fresh object is created for every successful submission.

---

# 65. Why Create a Fresh Object?

Incorrect:

```js
const reusableProduct = {};

function createProduct(data) {
  reusableProduct.id =
    generateProductId();

  reusableProduct.name =
    data.name;

  products.push(
    reusableProduct
  );
}
```

Every entry may point to the same object reference.

Correct:

```js
const newProduct = {
  id: generateProductId(),
  name: data.name,
  price: data.price,
  quantity: data.quantity,
};
```

---

# 66. Resetting the Product Form

```js
function resetProductForm() {
  productForm.reset();

  productNameInput.focus();
}
```

This resets only form controls.

It does not reset:

```js
products
```

or:

```js
nextProductId
```

---

# 67. Create Mode UI

```js
function setCreateModeUI() {
  editingProductId = null;

  productFormTitle.textContent =
    "Add Product";

  submitButton.textContent =
    "Add Product";

  cancelEditButton.hidden =
    true;
}
```

For now, Create is the only completed mode.

Update behavior will be implemented next.

---

# 68. Reset to Create Mode

```js
function resetFormToCreateMode() {
  setCreateModeUI();

  productForm.reset();

  productNameInput.focus();
}
```

---

# 69. Complete Submit Handler for Create

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
    editingProductId !== null
  ) {
    showInfo(
      "Product updating will be implemented in the next part."
    );

    return;
  }

  const newProduct =
    createProduct(
      validationResult.normalizedData
    );

  resetFormToCreateMode();

  renderProducts();

  showSuccess(
    `${newProduct.name} was added successfully.`
  );
}
```

Attach it:

```js
productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);
```

---

# 70. Reset Button Behavior

The Reset Form button should:

* Clear current inputs.
* Return to Create mode.
* Clear old messages.
* Not delete products.

```js
resetButton.addEventListener(
  "click",
  function () {
    clearMessages();

    resetFormToCreateMode();

    renderProducts();

    showInfo(
      "The form was reset."
    );
  }
);
```

The render call prepares for later edit highlighting.

---

# 71. Why Re-render After Create?

Before Create:

```js
products.length === 3;
```

After:

```js
products.push(newProduct);
```

state contains four products.

The DOM still displays three until:

```js
renderProducts();
```

The summary also needs refreshing:

```text
Total Products
Total Units
Inventory Value
Out of Stock
```

---

# 72. Create Flow Example

User enters:

```text
Name: Mouse
Price: 1500
Quantity: 4
```

Normalized data:

```js
{
  name: "Mouse",
  price: 1500,
  quantity: 4,
}
```

Created product:

```js
{
  id: 4,
  name: "Mouse",
  price: 1500,
  quantity: 4,
}
```

Final state:

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
    name: "Mouse",
    price: 1500,
    quantity: 4,
  },
]
```

---

# 73. Updated Summary After Create

Before adding Mouse:

```text
Total Products: 3
Total Units: 7
Inventory Value: KSh 225,000
Out of Stock: 1
```

Mouse value:

```text
1,500 × 4 = 6,000
```

After adding Mouse:

```text
Total Products: 4
Total Units: 11
Inventory Value: KSh 231,000
Out of Stock: 1
```

---

# 74. Empty and Invalid Input Cases

## Blank Name

Input:

```text
Name: ""
Price: 500
Quantity: 2
```

Result:

```text
Product name is required.
```

State does not change.

---

## Missing Price

Input:

```text
Name: Mouse
Price: ""
Quantity: 2
```

Result:

```text
Product price is required.
```

---

## Negative Price

Input:

```text
Name: Mouse
Price: -500
Quantity: 2
```

Result:

```text
Product price cannot be negative.
```

---

## Decimal Quantity

Input:

```text
Name: Mouse
Price: 1500
Quantity: 2.5
```

Result:

```text
Product quantity must be a whole number.
```

---

## Duplicate Name

Existing product:

```text
Laptop
```

New input:

```text
 laptop
```

Result:

```text
A product with this name already exists.
```

---

# 75. Complete Part 2 JavaScript Additions

Add these functions to the Part 1 `app.js` file:

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

function productNameExists(
  productName,
  ignoredProductId = null
) {
  const normalizedName =
    productName
      .trim()
      .toLowerCase();

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
        "Product price is required.",
      field: "price",
    };
  }

  const price =
    Number(data.priceText);

  if (
    Number.isNaN(price) ||
    !Number.isFinite(price)
  ) {
    return {
      isValid: false,
      message:
        "Product price must be a valid number.",
      field: "price",
    };
  }

  if (price < 0) {
    return {
      isValid: false,
      message:
        "Product price cannot be negative.",
      field: "price",
    };
  }

  if (
    data.quantityText === ""
  ) {
    return {
      isValid: false,
      message:
        "Product quantity is required.",
      field: "quantity",
    };
  }

  const quantity =
    Number(data.quantityText);

  if (
    !Number.isInteger(quantity)
  ) {
    return {
      isValid: false,
      message:
        "Product quantity must be a whole number.",
      field: "quantity",
    };
  }

  if (quantity < 0) {
    return {
      isValid: false,
      message:
        "Product quantity cannot be negative.",
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

function generateProductId() {
  const productId =
    nextProductId;

  nextProductId++;

  return productId;
}

function createProduct(data) {
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

function setCreateModeUI() {
  editingProductId = null;

  productFormTitle.textContent =
    "Add Product";

  submitButton.textContent =
    "Add Product";

  cancelEditButton.hidden =
    true;
}

function resetFormToCreateMode() {
  setCreateModeUI();

  productForm.reset();

  productNameInput.focus();
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
    editingProductId !== null
  ) {
    showInfo(
      "Product updating will be implemented in the next part."
    );

    return;
  }

  const newProduct =
    createProduct(
      validationResult.normalizedData
    );

  resetFormToCreateMode();

  renderProducts();

  showSuccess(
    `${newProduct.name} was added successfully.`
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
      "The form was reset."
    );
  }
);

setCreateModeUI();
renderProducts();
```

> Remove the older standalone `renderProducts();` at the bottom before adding the final two initialization calls, so the page is not rendered twice unnecessarily.

---

# 76. Three Complete Examples

## Example 1: Validate Product Data Without the DOM

### Problem

Validate a product object before adding it to state.

### Complete Code

```js
function validateProduct(
  product
) {
  if (
    typeof product.name !==
      "string" ||
    product.name.trim() === ""
  ) {
    return false;
  }

  if (
    typeof product.price !==
      "number" ||
    !Number.isFinite(
      product.price
    ) ||
    product.price < 0
  ) {
    return false;
  }

  if (
    !Number.isInteger(
      product.quantity
    ) ||
    product.quantity < 0
  ) {
    return false;
  }

  return true;
}

console.log(
  validateProduct({
    name: "Laptop",
    price: 50000,
    quantity: 2,
  })
);

console.log(
  validateProduct({
    name: "",
    price: -10,
    quantity: 2.5,
  })
);
```

### Expected Console Output

```text
true
false
```

### Final State

No state exists in this example.

Validation only returns a result.

### Common Mistakes

* Changing data during validation.
* Returning true too early.
* Forgetting integer quantity validation.

### Small Modification Challenge

Reject names shorter than two characters.

---

## Example 2: Duplicate Name Check

### Problem

Check product names using a case-insensitive plain loop.

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
    const existingName =
      products[index].name
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

console.log(
  productNameExists(
    " laptop "
  )
);

console.log(
  productNameExists(
    "Keyboard"
  )
);
```

### Expected Console Output

```text
true
false
```

### Final State

The products array remains unchanged.

### Common Mistakes

* Normalizing only one value.
* Returning false inside the loop after the first non-match.
* Comparing IDs instead of names.

### Small Modification Challenge

Add an `ignoredProductId` parameter.

---

## Example 3: Complete Create Form

### Problem

Add valid products to state and render them.

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

  <p id="message"></p>

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

    const message =
      document.getElementById(
        "message"
      );

    const productList =
      document.getElementById(
        "product-list"
      );

    function renderProducts() {
      productList.innerHTML = "";

      if (products.length === 0) {
        const emptyItem =
          document.createElement(
            "li"
          );

        emptyItem.textContent =
          "No products available.";

        productList.appendChild(
          emptyItem
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
          document.createElement(
            "li"
          );

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

        message.textContent = "";

        const name =
          productNameInput.value
            .trim();

        const priceText =
          productPriceInput.value
            .trim();

        const quantityText =
          productQuantityInput.value
            .trim();

        if (name === "") {
          message.textContent =
            "Name is required.";

          return;
        }

        if (
          priceText === ""
        ) {
          message.textContent =
            "Price is required.";

          return;
        }

        const price =
          Number(priceText);

        if (
          !Number.isFinite(price) ||
          price < 0
        ) {
          message.textContent =
            "Price is invalid.";

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
          message.textContent =
            "Quantity is invalid.";

          return;
        }

        const newProduct = {
          id: nextProductId,
          name,
          price,
          quantity,
        };

        nextProductId++;

        products.push(
          newProduct
        );

        productForm.reset();

        renderProducts();

        message.textContent =
          `${name} was added.`;
      }
    );

    renderProducts();
  </script>
</body>
</html>
```

### Expected Initial DOM

```text
No products available.
```

### Expected DOM After Adding Laptop

```text
Laptop — KSh 50,000 — Quantity: 2
```

### Final State

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
]
```

### Common Mistakes

* Forgetting `preventDefault()`.
* Storing numeric strings.
* Creating the ID before validation.
* Forgetting to render after `push()`.

### Small Modification Challenge

Prevent duplicate names using a plain loop.

---

# 77. Common Beginner Mistakes

## Mistake 1: Converting Empty Strings Too Early

```js
const price =
  Number(
    priceInput.value
  );
```

An empty value becomes zero.

Check the raw string first.

---

## Mistake 2: Using `parseInt()` for Price

```js
parseInt("299.99");
```

returns:

```text
299
```

Use:

```js
Number()
```

for prices that may include decimals.

---

## Mistake 3: Allowing Decimal Quantities

Quantity should use:

```js
Number.isInteger()
```

---

## Mistake 4: Returning False Inside the Duplicate Loop

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

## Mistake 5: Creating the ID Before Validation

Invalid attempts consume IDs unnecessarily.

---

## Mistake 6: Reusing One Product Object

Create a fresh object on every submission.

---

## Mistake 7: Resetting State Instead of the Form

Incorrect:

```js
products = [];
```

Correct:

```js
productForm.reset();
```

---

## Mistake 8: Not Re-rendering

The product exists in state but not on the page.

---

## Mistake 9: Forgetting Duplicate Normalization

```text
Laptop
 laptop
LAPTOP
```

should be treated consistently.

---

## Mistake 10: Using `innerHTML` for Product Names

Use `textContent`.

---

# 78. Beginner Questions

1. What type does an input `.value` return?
2. Why should numeric text be converted?
3. Why should empty numeric fields be checked before conversion?
4. What does `trim()` do?
5. Why should names be normalized for duplicate checking?
6. How do you convert a price string?
7. How do you validate a whole-number quantity?
8. Why are negative quantities invalid?
9. What does `Number.isFinite()` check?
10. When should an ID be generated?
11. What does `push()` do?
12. Why should each product be a fresh object?
13. What happens after successful creation?
14. Why should the form reset only after success?
15. Why should rendering happen after state mutation?

---

# 79. Intermediate Questions

1. Why return normalized data from validation?
2. Why should validation avoid mutating state?
3. Why does the duplicate function accept an ignored ID?
4. Why should Create pass `null` as the ignored ID?
5. Why is `Number("")` potentially misleading?
6. Why is `parseInt()` unsuitable for decimal prices?
7. Why is `Number.isInteger()` important for quantity?
8. Why should the submit handler coordinate smaller functions?
9. Why should invalid submissions preserve entered values?
10. Why should focus move to the invalid field?
11. Why is a structured validation result useful?
12. Why should success messages use the newly created object?
13. Why should `nextProductId` remain outside the handler?
14. Why should totals be re-rendered after creation?
15. How does this Create flow prepare for Update?

---

# 80. Advanced Questions

1. How could currency be stored in integer cents?
2. Why can floating-point arithmetic affect decimal prices?
3. How could business rules require a price greater than zero?
4. How could validation be shared between browser and server?
5. Why is client-side validation insufficient for secure systems?
6. How could product names be normalized more strictly?
7. How should Unicode and accented characters affect duplicate checks?
8. How could IDs be generated by a server?
9. How would asynchronous creation affect button disabling?
10. What is optimistic creation?
11. How could failed server creation roll back UI state?
12. How could validation functions be unit-tested?

---

# 81. Output Prediction Questions

## Question 1

```js
console.log(
  Number("")
);
```

Predict the output.

---

## Question 2

```js
console.log(
  Number("50000")
);
```

Predict the output.

---

## Question 3

```js
console.log(
  Number.isInteger(2.5)
);
```

Predict the output.

---

## Question 4

```js
console.log(
  " Laptop "
    .trim()
    .toLowerCase()
);
```

Predict the output.

---

## Question 5

```js
let products = [];

products.push({
  id: 1,
  name: "Laptop",
});

console.log(
  products.length
);
```

Predict the output.

---

## Question 6

```js
let nextId = 4;

const product = {
  id: nextId,
};

nextId++;

console.log(
  product.id
);

console.log(nextId);
```

Predict both outputs.

---

## Question 7

```js
const quantity =
  Number("0");

console.log(
  Number.isInteger(
    quantity
  )
);

console.log(
  quantity < 0
);
```

Predict both outputs.

---

## Question 8

```js
const price =
  Number("abc");

console.log(price);

console.log(
  Number.isNaN(price)
);
```

Predict both outputs.

---

# 82. Debugging Exercises

For each exercise:

1. Identify the bug.
2. Explain why it occurs.
3. Correct it.
4. State the expected behavior.

## Debugging Exercise 1: Empty Price Becomes Zero

```js
const price =
  Number(
    priceInput.value
  );

if (price < 0) {
  showError(
    "Invalid price."
  );
}
```

---

## Debugging Exercise 2: Decimal Quantity Accepted

```js
const quantity =
  Number(
    quantityInput.value
  );

if (quantity >= 0) {
  // Valid
}
```

---

## Debugging Exercise 3: Duplicate Search Stops Early

```js
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
```

---

## Debugging Exercise 4: ID Increments on Invalid Form

```js
const id =
  generateProductId();

const result =
  validateProductData(data);

if (!result.isValid) {
  return;
}
```

---

## Debugging Exercise 5: Strings Stored in State

```js
const product = {
  price:
    priceInput.value,
  quantity:
    quantityInput.value,
};
```

---

## Debugging Exercise 6: Form Resets Before Validation

```js
productForm.reset();

const result =
  validateProductData(data);
```

---

## Debugging Exercise 7: State Is Cleared After Add

```js
products.push(
  newProduct
);

products = [];
```

---

## Debugging Exercise 8: Missing Re-render

```js
products.push(
  newProduct
);

showSuccess(
  "Added."
);
```

---

# 83. Coding Exercises

## Exercise 1: Read Form Data

Create:

```js
readProductForm();
```

---

## Exercise 2: Validate Name

Reject empty, short, and overly long names.

---

## Exercise 3: Validate Price

Reject blank, invalid, and negative prices.

---

## Exercise 4: Validate Quantity

Reject blank, decimal, and negative quantities.

---

## Exercise 5: Duplicate Check

Use a case-insensitive plain loop.

---

## Exercise 6: Generate IDs

Create a persistent counter.

---

## Exercise 7: Create Product

Return a fresh product object.

---

## Exercise 8: Add Product to State

Use `push()`.

---

## Exercise 9: Reset Form

Preserve the products array.

---

## Exercise 10: Focus Invalid Field

Use the returned field name.

---

## Exercise 11: Re-render Summary

Confirm totals change after Create.

---

## Exercise 12: Build the Full Create Flow

Connect form submission to state and rendering.

---

# 84. Refactoring Exercises

## Exercise 1

Move form reading into:

```js
readProductForm();
```

## Exercise 2

Move all checks into:

```js
validateProductData(data);
```

## Exercise 3

Move duplicate logic into:

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

Move invalid-field focus into:

```js
focusInvalidField(field);
```

## Exercise 6

Move mode-reset logic into:

```js
resetFormToCreateMode();
```

## Exercise 7

Reduce the submit handler to coordination logic.

## Exercise 8

Replace repeated messages with reusable message functions.

---

# 85. Conceptual Questions

1. Why are form values considered raw data?
2. Why should validation happen before state mutation?
3. Why should price and quantity be stored as numbers?
4. Why should price and quantity use different numeric rules?
5. Why is zero quantity valid?
6. Why might zero price be valid?
7. Why should duplicate checking normalize case and spacing?
8. Why must `return false` appear after the duplicate loop?
9. Why should IDs be generated after validation?
10. Why should each created product be a new object?
11. Why should the form reset after success but not failure?
12. Why should a Create operation re-render summaries?
13. Why should the array remain the source of truth?
14. Why is validation not the same as sanitization?
15. How does this Create logic connect to future Update logic?

---

# 86. Part 2 Completion Task

Continue working in:

```text
product-inventory-plain-loops/
```

Update:

```text
app.js
```

Required functions:

```js
readProductForm();
productNameExists(
  productName,
  ignoredProductId
);
validateProductData(data);
focusInvalidField(field);
generateProductId();
createProduct(data);
setCreateModeUI();
resetFormToCreateMode();
handleProductFormSubmit(event);
```

Requirements:

* [ ] Read product name.
* [ ] Read price text.
* [ ] Read quantity text.
* [ ] Trim all input strings.
* [ ] Validate empty name.
* [ ] Validate minimum name length.
* [ ] Validate maximum name length.
* [ ] Prevent duplicate names.
* [ ] Compare names case-insensitively.
* [ ] Validate empty price.
* [ ] Convert price with `Number()`.
* [ ] Reject invalid price.
* [ ] Reject negative price.
* [ ] Validate empty quantity.
* [ ] Convert quantity with `Number()`.
* [ ] Require integer quantity.
* [ ] Reject negative quantity.
* [ ] Return normalized values.
* [ ] Focus the invalid field.
* [ ] Generate an ID after validation.
* [ ] Create a fresh object.
* [ ] Add it with `push()`.
* [ ] Reset the form after success.
* [ ] Re-render the product list.
* [ ] Re-render all summaries.
* [ ] Show a success message.
* [ ] Preserve form values after validation failure.
* [ ] Add Reset Form behavior.
* [ ] Do not implement Update yet.
* [ ] Do not implement Delete yet.
* [ ] Do not implement Search yet.

Manual testing checklist:

* [ ] Add Mouse, price 1500, quantity 4.
* [ ] Reject a blank name.
* [ ] Reject a one-character name.
* [ ] Reject a duplicate `laptop`.
* [ ] Reject an empty price.
* [ ] Accept a price of zero.
* [ ] Reject a negative price.
* [ ] Reject an empty quantity.
* [ ] Accept quantity zero.
* [ ] Reject quantity 2.5.
* [ ] Reject a negative quantity.
* [ ] Confirm the next ID increments only after valid creation.
* [ ] Confirm the form resets after success.
* [ ] Confirm invalid values remain visible for correction.
* [ ] Confirm totals update correctly.
* [ ] Confirm repeated renders do not duplicate cards.

Expected new product:

```js
{
  id: 4,
  name: "Mouse",
  price: 1500,
  quantity: 4,
}
```

Expected summary:

```text
Total Products: 4
Total Units: 11
Inventory Value: KSh 231,000
Out of Stock: 1
```

---

## Key Takeaways

* Input values are strings and must be normalized and converted.
* Empty numeric fields should be checked before conversion.
* Product names should be trimmed and checked case-insensitively.
* Price may contain decimals but cannot be negative.
* Quantity must be a non-negative whole number.
* Validation should return structured results.
* Invalid data must never reach application state.
* IDs should be generated only after validation succeeds.
* Every product should be created as a fresh object.
* `push()` mutates the products array.
* Re-rendering updates the list and all derived summaries.
* The next part will add Edit mode and update existing products using a standard `for` loop.

---

## Continue Learning

| Direction         | Link                                                                                                                |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                   |
| Previous Subtopic | [Product Inventory Part 1: Architecture and Rendering](./02-product-inventory-part-1-architecture-and-rendering.md) |
| Next Subtopic     | [Product Inventory Part 3: Updating Products](./02-product-inventory-part-3-updating-products.md)                   |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                   |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 2

When you are ready to continue, write:

```text
Next
```
