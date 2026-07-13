---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 1: Architecture, State, HTML, CSS, and Rendering"
difficulty: "Beginner to Intermediate"
estimated_time: "120–150 minutes"
prerequisites:

* "Complete Shopping List CRUD with plain loops"
* "Arrays of objects"
* "Standard for loops"
* "DOM element creation"
* "Reusable render functions"
* "Event delegation"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Shopping List with Plain Loops"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 2: Creating Products and Validating Form Data"

---

# Building a Complete Product Inventory with Plain Loops

## Part 1: Architecture, State, HTML, CSS, and Rendering

## Course Navigation

| Direction         | Link                                                                                                 |
| ----------------- | ---------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                    |
| Previous Subtopic | [Building a Complete Shopping List with Plain Loops](./01-shopping-list-crud-with-plain-loops.md)    |
| Next Subtopic     | [Creating Products and Validating Form Data](./02-product-inventory-part-2-create-and-validation.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                    |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 1

---

## Learning Objectives

By the end of this part, you should be able to:

* Explain the architecture of a Product Inventory CRUD application.
* Define product state using an array of objects.
* Distinguish between application state, form state, and derived state.
* Identify the main DOM elements needed by the application.
* Build the complete HTML structure.
* Add clear beginner-friendly CSS.
* Render products using a standard `for` loop.
* Render an empty-state message.
* Render product totals using plain loops.
* Identify out-of-stock products.
* Calculate inventory value using a plain loop.
* Create Edit and Delete buttons with stable IDs.
* Use `dataset` attributes for dynamic actions.
* Explain why rendering must use the state array as the source of truth.
* Prepare the application for Create, Update, Delete, and Search features.

---

## Prerequisites

You should already understand:

* JavaScript arrays.
* JavaScript objects.
* Standard `for` loops.
* Functions.
* `document.getElementById()`.
* `document.createElement()`.
* `textContent`.
* `append()` and `appendChild()`.
* `classList.add()`.
* `dataset`.
* Form submit events.
* Button click events.
* State and DOM synchronization.

---

## What You Will Build

You will build a Product Inventory application with this product structure:

```js
{
  id,
  name,
  price,
  quantity
}
```

Example:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

The complete application will eventually support:

* Adding products.
* Displaying products.
* Editing products.
* Deleting products.
* Searching products.
* Preventing duplicate product names.
* Calculating total inventory value.
* Counting products.
* Counting total units.
* Showing out-of-stock products.
* Showing an empty state.
* Using one form for Create and Update.
* Using event delegation.
* Keeping state and DOM synchronized.

This part focuses on the application structure and Read operation.

---

# 22. Product Inventory Application Architecture

## 22.1 What You Will Learn

A larger CRUD application becomes easier to understand when it is divided into responsibilities.

The Product Inventory application will contain five main layers:

```text
Application state
        ↓
State functions
        ↓
Rendering functions
        ↓
Event handlers
        ↓
DOM interface
```

Each layer has a different job.

---

## 22.2 Application State

The main product state will be:

```js
let products = [
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
];
```

This array is the main source of truth.

The DOM should display whatever is currently inside this array.

---

## 22.3 UI State

The application also needs temporary UI state.

```js
let nextProductId = 4;
let editingProductId = null;
let currentSearchTerm = "";
```

### `nextProductId`

Stores the next unique ID.

### `editingProductId`

Stores the ID of the product currently being edited.

```js
null
```

means the form is in Create mode.

### `currentSearchTerm`

Stores the active search value.

In this first part, it begins as:

```js
""
```

Search logic will be implemented later.

---

## 22.4 Derived State

Derived state is information calculated from the main array.

Examples:

```text
Total products
Total units
Inventory value
Out-of-stock count
```

These values should not normally be stored separately.

Instead, calculate them from:

```js
products
```

Example:

```js
products.length
```

gives the number of product records.

---

## 22.5 Why Derived State Should Be Calculated

Suppose you store:

```js
let totalProducts = 3;
```

Then delete a product but forget to update the variable.

You may have:

```js
products.length === 2;
```

but:

```js
totalProducts === 3;
```

The application becomes inconsistent.

Prefer:

```js
const totalProducts =
  products.length;
```

whenever rendering the summary.

---

# 23. Product Data Structure

## 23.1 Product Object

Each product contains:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

---

## 23.2 `id`

```js
id: 1
```

The ID uniquely identifies the product.

It is used for:

* Edit.
* Delete.
* Finding the product.
* Connecting DOM buttons to state.
* Preserving identity after sorting and filtering.

---

## 23.3 `name`

```js
name: "Laptop"
```

The product name is displayed to the user.

It will also be used for:

* Search.
* Duplicate validation.
* Edit forms.
* Delete confirmation.

---

## 23.4 `price`

```js
price: 50000
```

The price is stored as a number.

Do not store:

```js
price: "50000"
```

because calculations become less reliable.

---

## 23.5 `quantity`

```js
quantity: 2
```

Quantity is stored as a non-negative integer.

Examples:

```text
0
1
2
15
```

A quantity of:

```text
0
```

means the product is out of stock.

---

## 23.6 Product Inventory Value

One product's inventory value is:

```text
price × quantity
```

Example:

```text
Laptop
Price: KSh 50,000
Quantity: 2
Value: KSh 100,000
```

JavaScript:

```js
const productValue =
  product.price *
  product.quantity;
```

---

# 24. DOM Structure

## 24.1 Main Interface Sections

The application needs:

1. Page heading.
2. Product form.
3. Validation messages.
4. Search input.
5. Summary section.
6. Product list.
7. Empty-state output.

---

## 24.2 Complete HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />

  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />

  <title>Product Inventory</title>

  <link
    rel="stylesheet"
    href="styles.css"
  />
</head>

<body>
  <main class="app-container">
    <header class="page-header">
      <h1>Product Inventory</h1>

      <p>
        Manage products using JavaScript
        state, the DOM, and plain loops.
      </p>
    </header>

    <section
      class="panel"
      aria-labelledby="product-form-title"
    >
      <h2 id="product-form-title">
        Add Product
      </h2>

      <form id="product-form">
        <div class="form-group">
          <label for="product-name">
            Product Name
          </label>

          <input
            id="product-name"
            type="text"
            maxlength="80"
            autocomplete="off"
          />
        </div>

        <div class="form-group">
          <label for="product-price">
            Price
          </label>

          <input
            id="product-price"
            type="number"
            min="0"
            step="0.01"
          />
        </div>

        <div class="form-group">
          <label for="product-quantity">
            Quantity
          </label>

          <input
            id="product-quantity"
            type="number"
            min="0"
            step="1"
          />
        </div>

        <div class="form-actions">
          <button
            id="submit-button"
            type="submit"
          >
            Add Product
          </button>

          <button
            id="cancel-edit-button"
            type="button"
            hidden
          >
            Cancel Edit
          </button>

          <button
            id="reset-button"
            type="button"
          >
            Reset Form
          </button>
        </div>
      </form>

      <p
        id="error-message"
        class="message error-message"
        role="alert"
      ></p>

      <p
        id="success-message"
        class="message success-message"
        role="status"
      ></p>

      <p
        id="info-message"
        class="message info-message"
        role="status"
      ></p>
    </section>

    <section
      class="panel"
      aria-labelledby="search-title"
    >
      <h2 id="search-title">
        Search Products
      </h2>

      <div class="search-row">
        <label
          class="visually-hidden"
          for="search-input"
        >
          Search by product name
        </label>

        <input
          id="search-input"
          type="search"
          placeholder="Search by product name"
          autocomplete="off"
        />

        <button
          id="clear-search-button"
          type="button"
        >
          Clear Search
        </button>
      </div>
    </section>

    <section
      class="summary-grid"
      aria-label="Inventory summary"
    >
      <article class="summary-card">
        <h2>Total Products</h2>

        <p id="total-product-count">
          0
        </p>
      </article>

      <article class="summary-card">
        <h2>Total Units</h2>

        <p id="total-unit-count">
          0
        </p>
      </article>

      <article class="summary-card">
        <h2>Inventory Value</h2>

        <p id="total-inventory-value">
          KSh 0
        </p>
      </article>

      <article class="summary-card">
        <h2>Out of Stock</h2>

        <p id="out-of-stock-count">
          0
        </p>
      </article>
    </section>

    <section
      class="panel"
      aria-labelledby="product-list-title"
    >
      <div class="section-heading-row">
        <h2 id="product-list-title">
          Products
        </h2>

        <p id="visible-product-count">
          Showing 0 products
        </p>
      </div>

      <ul id="product-list"></ul>
    </section>
  </main>

  <script src="app.js"></script>
</body>
</html>
```

---

# 25. HTML Syntax Breakdown

## 25.1 Why Use `<main>`?

```html
<main class="app-container">
```

The `<main>` element contains the main content of the page.

It improves:

* Semantic structure.
* Accessibility.
* Code organization.

---

## 25.2 Why Use Sections?

```html
<section class="panel">
```

Each major application area is grouped separately.

Examples:

* Product form.
* Search.
* Product list.
* Summary.

---

## 25.3 Why Use `type="button"`?

Buttons inside forms default to submit behavior.

Therefore:

```html
<button
  type="button"
>
  Cancel Edit
</button>
```

prevents accidental form submission.

Use `type="button"` for:

* Cancel.
* Reset.
* Edit.
* Delete.
* Clear Search.

---

## 25.4 Why Use `role="alert"`?

```html
<p
  id="error-message"
  role="alert"
></p>
```

This helps assistive technology announce important errors.

---

## 25.5 Why Use `role="status"`?

```html
<p
  id="success-message"
  role="status"
></p>
```

This is appropriate for non-critical status updates such as:

```text
Laptop was added successfully.
```

---

## 25.6 Why Is the Product List Empty in HTML?

```html
<ul id="product-list"></ul>
```

The list is not manually hard-coded.

JavaScript renders it from:

```js
products
```

This keeps the array as the source of truth.

---

# 26. CSS Structure

## 26.1 Complete CSS

Create:

```text
styles.css
```

Add:

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #f5f5f5;
  font-family:
    Arial,
    Helvetica,
    sans-serif;
  line-height: 1.5;
}

button,
input {
  font: inherit;
}

button {
  cursor: pointer;
}

button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

.app-container {
  width: min(100% - 32px, 1000px);
  margin: 0 auto;
  padding: 32px 0;
}

.page-header {
  margin-bottom: 24px;
}

.page-header h1 {
  margin-bottom: 8px;
}

.page-header p {
  margin: 0;
}

.panel {
  margin-bottom: 20px;
  padding: 18px;
  background: #ffffff;
  border: 1px solid #d5d5d5;
  border-radius: 10px;
}

.form-group {
  display: grid;
  gap: 6px;
  margin-bottom: 14px;
}

.form-group label {
  font-weight: bold;
}

.form-group input,
.search-row input {
  width: 100%;
  padding: 10px;
  border: 1px solid #999999;
  border-radius: 6px;
}

.form-actions,
.search-row,
.product-actions,
.section-heading-row {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  align-items: center;
}

.form-actions button,
.search-row button,
.product-actions button {
  padding: 9px 14px;
  border: 1px solid #777777;
  border-radius: 6px;
}

.search-row input {
  flex: 1 1 240px;
}

.message {
  min-height: 24px;
  margin-bottom: 0;
}

.error-message {
  font-weight: bold;
}

.success-message {
  font-weight: bold;
}

.info-message {
  font-style: italic;
}

.summary-grid {
  display: grid;
  grid-template-columns:
    repeat(
      auto-fit,
      minmax(180px, 1fr)
    );
  gap: 14px;
  margin-bottom: 20px;
}

.summary-card {
  padding: 16px;
  background: #ffffff;
  border: 1px solid #d5d5d5;
  border-radius: 10px;
}

.summary-card h2 {
  margin-top: 0;
  font-size: 1rem;
}

.summary-card p {
  margin-bottom: 0;
  font-size: 1.4rem;
  font-weight: bold;
}

.section-heading-row {
  justify-content: space-between;
}

#product-list {
  display: grid;
  gap: 14px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.product-card {
  padding: 16px;
  border: 1px solid #cccccc;
  border-radius: 8px;
}

.product-card h3 {
  margin-top: 0;
}

.product-details {
  display: grid;
  gap: 4px;
  margin-bottom: 12px;
}

.product-details p {
  margin: 0;
}

.out-of-stock {
  border-style: dashed;
}

.currently-editing {
  outline: 2px solid currentColor;
}

.empty-state {
  padding: 24px;
  border: 1px dashed #999999;
  border-radius: 8px;
  text-align: center;
}

.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

@media (max-width: 600px) {
  .form-actions,
  .product-actions {
    flex-direction: column;
    align-items: stretch;
  }

  .form-actions button,
  .product-actions button {
    width: 100%;
  }
}
```

---

# 27. CSS Explanation

## 27.1 Why Use `box-sizing: border-box`?

```css
* {
  box-sizing: border-box;
}
```

This makes width calculations easier.

Padding and borders are included inside an element's declared width.

---

## 27.2 Why Use a Maximum Container Width?

```css
.app-container {
  width: min(100% - 32px, 1000px);
}
```

This:

* Keeps the app readable on large screens.
* Adds side spacing on small screens.
* Avoids a very wide form.

---

## 27.3 Why Use a Grid for Summary Cards?

```css
.summary-grid {
  display: grid;
}
```

This allows the summary cards to adapt to available screen width.

---

## 27.4 Why Use an Out-of-Stock Class?

```css
.out-of-stock {
  border-style: dashed;
}
```

The class visually distinguishes products whose quantity is:

```text
0
```

The class is derived from JavaScript state.

---

## 27.5 Why Use a Currently Editing Class?

```css
.currently-editing {
  outline: 2px solid currentColor;
}
```

When a product is being edited, its card can be highlighted.

The class will be applied when:

```js
product.id === editingProductId
```

---

# 28. JavaScript Application State

Create:

```text
app.js
```

Begin with:

```js
let products = [
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
];

let nextProductId = 4;
let editingProductId = null;
let currentSearchTerm = "";
```

---

# 29. Selecting DOM Elements

```js
const productForm =
  document.getElementById(
    "product-form"
  );

const productFormTitle =
  document.getElementById(
    "product-form-title"
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

const submitButton =
  document.getElementById(
    "submit-button"
  );

const cancelEditButton =
  document.getElementById(
    "cancel-edit-button"
  );

const resetButton =
  document.getElementById(
    "reset-button"
  );

const searchInput =
  document.getElementById(
    "search-input"
  );

const clearSearchButton =
  document.getElementById(
    "clear-search-button"
  );

const errorMessage =
  document.getElementById(
    "error-message"
  );

const successMessage =
  document.getElementById(
    "success-message"
  );

const infoMessage =
  document.getElementById(
    "info-message"
  );

const totalProductCount =
  document.getElementById(
    "total-product-count"
  );

const totalUnitCount =
  document.getElementById(
    "total-unit-count"
  );

const totalInventoryValue =
  document.getElementById(
    "total-inventory-value"
  );

const outOfStockCount =
  document.getElementById(
    "out-of-stock-count"
  );

const visibleProductCount =
  document.getElementById(
    "visible-product-count"
  );

const productList =
  document.getElementById(
    "product-list"
  );
```

---

# 30. Message Functions

```js
function clearMessages() {
  errorMessage.textContent = "";
  successMessage.textContent = "";
  infoMessage.textContent = "";
}

function showError(message) {
  errorMessage.textContent =
    message;
}

function showSuccess(message) {
  successMessage.textContent =
    message;
}

function showInfo(message) {
  infoMessage.textContent =
    message;
}
```

These functions will be reused by Create, Update, Delete, and Search.

---

# 31. Calculating Summary Values with Plain Loops

## 31.1 Total Products

The number of product records is:

```js
products.length;
```

No loop is needed for this value.

---

## 31.2 Total Units

### What You Will Learn

Total units means the sum of every product quantity.

State:

```js
[
  {
    name: "Laptop",
    quantity: 2,
  },
  {
    name: "Phone",
    quantity: 5,
  },
  {
    name: "Keyboard",
    quantity: 0,
  },
]
```

Expected total:

```text
7
```

### Function

```js
function calculateTotalUnits() {
  let total = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    total +=
      products[index].quantity;
  }

  return total;
}
```

---

## 31.3 Syntax Breakdown

```js
total +=
  products[index].quantity;
```

This is equivalent to:

```js
total =
  total +
  products[index].quantity;
```

Each repetition adds the current quantity.

---

## 31.4 Total Inventory Value

```js
function calculateInventoryValue() {
  let totalValue = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    totalValue +=
      product.price *
      product.quantity;
  }

  return totalValue;
}
```

Expected values:

```text
Laptop: 50,000 × 2 = 100,000
Phone: 25,000 × 5 = 125,000
Keyboard: 3,000 × 0 = 0
```

Total:

```text
KSh 225,000
```

---

## 31.5 Counting Out-of-Stock Products

```js
function calculateOutOfStockCount() {
  let count = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].quantity === 0
    ) {
      count++;
    }
  }

  return count;
}
```

For the sample state:

```text
1
```

because Keyboard has zero quantity.

---

# 32. Rendering the Summary

```js
function renderSummary() {
  totalProductCount.textContent =
    products.length;

  totalUnitCount.textContent =
    calculateTotalUnits();

  totalInventoryValue.textContent =
    `KSh ${calculateInventoryValue().toLocaleString()}`;

  outOfStockCount.textContent =
    calculateOutOfStockCount();
}
```

This function reads the current state and updates the DOM.

---

# 33. Empty-State Rendering

## 33.1 Why an Empty State Is Needed

When:

```js
products.length === 0
```

the user should not see a blank area.

Display:

```text
No products available.
```

---

## 33.2 Empty-State Function

```js
function renderEmptyState(
  message = "No products available."
) {
  const emptyState =
    document.createElement("li");

  emptyState.classList.add(
    "empty-state"
  );

  emptyState.textContent =
    message;

  productList.appendChild(
    emptyState
  );
}
```

The optional parameter allows different messages later.

Example:

```text
No products match your search.
```

---

# 34. Rendering One Product Card

## 34.1 What You Will Learn

A product card will display:

* Product number.
* Product name.
* Price.
* Quantity.
* Inventory value.
* Stock status.
* Edit button.
* Delete button.

---

## 34.2 Complete Card Function

```js
function renderProductCard(
  product,
  displayIndex
) {
  const card =
    document.createElement("li");

  const productName =
    document.createElement("h3");

  const details =
    document.createElement("div");

  const price =
    document.createElement("p");

  const quantity =
    document.createElement("p");

  const productValue =
    document.createElement("p");

  const stockStatus =
    document.createElement("p");

  const actions =
    document.createElement("div");

  const editButton =
    document.createElement("button");

  const deleteButton =
    document.createElement("button");

  card.classList.add(
    "product-card"
  );

  card.dataset.id =
    product.id;

  if (product.quantity === 0) {
    card.classList.add(
      "out-of-stock"
    );
  }

  if (
    product.id ===
    editingProductId
  ) {
    card.classList.add(
      "currently-editing"
    );
  }

  productName.textContent =
    `${displayIndex + 1}. ${product.name}`;

  details.classList.add(
    "product-details"
  );

  price.textContent =
    `Price: KSh ${product.price.toLocaleString()}`;

  quantity.textContent =
    `Quantity: ${product.quantity}`;

  productValue.textContent =
    `Inventory value: KSh ${(
      product.price *
      product.quantity
    ).toLocaleString()}`;

  if (product.quantity === 0) {
    stockStatus.textContent =
      "Status: Out of stock";
  } else {
    stockStatus.textContent =
      "Status: In stock";
  }

  actions.classList.add(
    "product-actions"
  );

  editButton.type =
    "button";

  editButton.textContent =
    "Edit";

  editButton.dataset.action =
    "edit";

  editButton.dataset.id =
    product.id;

  editButton.disabled =
    product.id ===
    editingProductId;

  deleteButton.type =
    "button";

  deleteButton.textContent =
    "Delete";

  deleteButton.dataset.action =
    "delete";

  deleteButton.dataset.id =
    product.id;

  details.append(
    price,
    quantity,
    productValue,
    stockStatus
  );

  actions.append(
    editButton,
    deleteButton
  );

  card.append(
    productName,
    details,
    actions
  );

  productList.appendChild(
    card
  );
}
```

---

# 35. Card Function Syntax Breakdown

## 35.1 Stable Product ID

```js
card.dataset.id =
  product.id;
```

This produces:

```html
<li data-id="1">
```

The stable ID connects the DOM card to the product object.

---

## 35.2 Out-of-Stock Class

```js
if (product.quantity === 0) {
  card.classList.add(
    "out-of-stock"
  );
}
```

The DOM style is derived from state.

---

## 35.3 Editing Class

```js
if (
  product.id ===
  editingProductId
) {
  card.classList.add(
    "currently-editing"
  );
}
```

Only the actively edited product is highlighted.

---

## 35.4 Inventory Value

```js
product.price *
product.quantity
```

The card calculates the value of one product.

---

## 35.5 Edit Button Dataset

```js
editButton.dataset.action =
  "edit";

editButton.dataset.id =
  product.id;
```

Expected HTML:

```html
<button
  data-action="edit"
  data-id="1"
>
  Edit
</button>
```

---

## 35.6 Delete Button Dataset

```js
deleteButton.dataset.action =
  "delete";

deleteButton.dataset.id =
  product.id;
```

These values will later be read by event delegation.

---

# 36. Rendering All Products with a Standard Loop

## 36.1 Basic Render Function

```js
function renderProducts() {
  productList.innerHTML = "";

  renderSummary();

  if (products.length === 0) {
    visibleProductCount.textContent =
      "Showing 0 products";

    renderEmptyState();
    return;
  }

  visibleProductCount.textContent =
    `Showing ${products.length} products`;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    renderProductCard(
      products[index],
      index
    );
  }
}
```

---

## 36.2 Why Clear the List?

```js
productList.innerHTML = "";
```

Without clearing, repeated renders create duplicate cards.

---

## 36.3 Why Render Summary First?

```js
renderSummary();
```

Summary values should update even when the list becomes empty.

---

## 36.4 Why Handle the Empty Array Early?

```js
if (products.length === 0) {
  renderEmptyState();
  return;
}
```

There is no reason to run the product loop when no products exist.

---

## 36.5 Why Use `index` Only for Display Numbering?

```js
`${index + 1}. ${product.name}`
```

The index determines visible order.

It should not be used as the permanent product identity.

---

# 37. Initial Render

At the bottom of `app.js`, call:

```js
renderProducts();
```

Without this call, the array exists in memory but the page remains empty.

---

# 38. Complete Part 1 JavaScript

```js
let products = [
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
];

let nextProductId = 4;
let editingProductId = null;
let currentSearchTerm = "";

const productForm =
  document.getElementById(
    "product-form"
  );

const productFormTitle =
  document.getElementById(
    "product-form-title"
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

const submitButton =
  document.getElementById(
    "submit-button"
  );

const cancelEditButton =
  document.getElementById(
    "cancel-edit-button"
  );

const resetButton =
  document.getElementById(
    "reset-button"
  );

const searchInput =
  document.getElementById(
    "search-input"
  );

const clearSearchButton =
  document.getElementById(
    "clear-search-button"
  );

const errorMessage =
  document.getElementById(
    "error-message"
  );

const successMessage =
  document.getElementById(
    "success-message"
  );

const infoMessage =
  document.getElementById(
    "info-message"
  );

const totalProductCount =
  document.getElementById(
    "total-product-count"
  );

const totalUnitCount =
  document.getElementById(
    "total-unit-count"
  );

const totalInventoryValue =
  document.getElementById(
    "total-inventory-value"
  );

const outOfStockCount =
  document.getElementById(
    "out-of-stock-count"
  );

const visibleProductCount =
  document.getElementById(
    "visible-product-count"
  );

const productList =
  document.getElementById(
    "product-list"
  );

function clearMessages() {
  errorMessage.textContent = "";
  successMessage.textContent = "";
  infoMessage.textContent = "";
}

function showError(message) {
  errorMessage.textContent =
    message;
}

function showSuccess(message) {
  successMessage.textContent =
    message;
}

function showInfo(message) {
  infoMessage.textContent =
    message;
}

function calculateTotalUnits() {
  let total = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    total +=
      products[index].quantity;
  }

  return total;
}

function calculateInventoryValue() {
  let totalValue = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    totalValue +=
      product.price *
      product.quantity;
  }

  return totalValue;
}

function calculateOutOfStockCount() {
  let count = 0;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].quantity === 0
    ) {
      count++;
    }
  }

  return count;
}

function renderSummary() {
  totalProductCount.textContent =
    products.length;

  totalUnitCount.textContent =
    calculateTotalUnits();

  totalInventoryValue.textContent =
    `KSh ${calculateInventoryValue().toLocaleString()}`;

  outOfStockCount.textContent =
    calculateOutOfStockCount();
}

function renderEmptyState(
  message = "No products available."
) {
  const emptyState =
    document.createElement("li");

  emptyState.classList.add(
    "empty-state"
  );

  emptyState.textContent =
    message;

  productList.appendChild(
    emptyState
  );
}

function renderProductCard(
  product,
  displayIndex
) {
  const card =
    document.createElement("li");

  const productName =
    document.createElement("h3");

  const details =
    document.createElement("div");

  const price =
    document.createElement("p");

  const quantity =
    document.createElement("p");

  const productValue =
    document.createElement("p");

  const stockStatus =
    document.createElement("p");

  const actions =
    document.createElement("div");

  const editButton =
    document.createElement("button");

  const deleteButton =
    document.createElement("button");

  card.classList.add(
    "product-card"
  );

  card.dataset.id =
    product.id;

  if (product.quantity === 0) {
    card.classList.add(
      "out-of-stock"
    );
  }

  if (
    product.id ===
    editingProductId
  ) {
    card.classList.add(
      "currently-editing"
    );
  }

  productName.textContent =
    `${displayIndex + 1}. ${product.name}`;

  details.classList.add(
    "product-details"
  );

  price.textContent =
    `Price: KSh ${product.price.toLocaleString()}`;

  quantity.textContent =
    `Quantity: ${product.quantity}`;

  productValue.textContent =
    `Inventory value: KSh ${(
      product.price *
      product.quantity
    ).toLocaleString()}`;

  if (product.quantity === 0) {
    stockStatus.textContent =
      "Status: Out of stock";
  } else {
    stockStatus.textContent =
      "Status: In stock";
  }

  actions.classList.add(
    "product-actions"
  );

  editButton.type =
    "button";

  editButton.textContent =
    "Edit";

  editButton.dataset.action =
    "edit";

  editButton.dataset.id =
    product.id;

  editButton.disabled =
    product.id ===
    editingProductId;

  deleteButton.type =
    "button";

  deleteButton.textContent =
    "Delete";

  deleteButton.dataset.action =
    "delete";

  deleteButton.dataset.id =
    product.id;

  details.append(
    price,
    quantity,
    productValue,
    stockStatus
  );

  actions.append(
    editButton,
    deleteButton
  );

  card.append(
    productName,
    details,
    actions
  );

  productList.appendChild(
    card
  );
}

function renderProducts() {
  productList.innerHTML = "";

  renderSummary();

  if (products.length === 0) {
    visibleProductCount.textContent =
      "Showing 0 products";

    renderEmptyState();
    return;
  }

  visibleProductCount.textContent =
    `Showing ${products.length} products`;

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    renderProductCard(
      products[index],
      index
    );
  }
}

renderProducts();
```

---

# 39. Expected Initial DOM Output

```text
Product Inventory

Total Products
3

Total Units
7

Inventory Value
KSh 225,000

Out of Stock
1

Products
Showing 3 products

1. Laptop
Price: KSh 50,000
Quantity: 2
Inventory value: KSh 100,000
Status: In stock
[Edit] [Delete]

2. Phone
Price: KSh 25,000
Quantity: 5
Inventory value: KSh 125,000
Status: In stock
[Edit] [Delete]

3. Keyboard
Price: KSh 3,000
Quantity: 0
Inventory value: KSh 0
Status: Out of stock
[Edit] [Delete]
```

---

# 40. Final State After Initial Render

Rendering does not change the array.

State remains:

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
]
```

Rendering is a Read operation.

---

# 41. Three Complete Examples

## Example 1: Calculate Total Units

### Problem

Calculate the total number of units in inventory.

### Input Data

```js
const products = [
  {
    name: "Laptop",
    quantity: 2,
  },
  {
    name: "Phone",
    quantity: 5,
  },
  {
    name: "Keyboard",
    quantity: 0,
  },
];
```

### Complete Code

```js
const products = [
  {
    name: "Laptop",
    quantity: 2,
  },
  {
    name: "Phone",
    quantity: 5,
  },
  {
    name: "Keyboard",
    quantity: 0,
  },
];

let totalUnits = 0;

for (
  let index = 0;
  index < products.length;
  index++
) {
  totalUnits +=
    products[index].quantity;
}

console.log(totalUnits);
```

### Step-by-Step Explanation

1. Start with total `0`.
2. Read Laptop quantity `2`.
3. Add it to total.
4. Read Phone quantity `5`.
5. Add it to total.
6. Read Keyboard quantity `0`.
7. Add it to total.
8. Print the final value.

### Expected Console Output

```text
7
```

### Final State

The array remains unchanged.

### Common Mistakes

* Initializing total inside the loop.
* Adding the product object instead of quantity.
* Using `index <= products.length`.
* Changing product quantities while calculating.

### Small Modification Challenge

Add:

```js
{
  name: "Mouse",
  quantity: 4,
}
```

Predict the new total.

---

## Example 2: Calculate Inventory Value

### Problem

Calculate the total financial value of all products.

### Input Data

```js
const products = [
  {
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
];
```

### Complete Code

```js
const products = [
  {
    name: "Laptop",
    price: 50000,
    quantity: 2,
  },
  {
    name: "Phone",
    price: 25000,
    quantity: 5,
  },
];

let inventoryValue = 0;

for (
  let index = 0;
  index < products.length;
  index++
) {
  const product =
    products[index];

  const currentProductValue =
    product.price *
    product.quantity;

  inventoryValue +=
    currentProductValue;
}

console.log(
  inventoryValue
);
```

### Expected Console Output

```text
225000
```

### Calculation

```text
Laptop: 50,000 × 2 = 100,000
Phone: 25,000 × 5 = 125,000

Total = 225,000
```

### Final State

No products are changed.

### Common Mistakes

* Adding prices without multiplying by quantities.
* Multiplying the total by every quantity.
* Storing prices as formatted strings such as `"KSh 50,000"`.

### Small Modification Challenge

Add an out-of-stock product and confirm that its contribution is zero.

---

## Example 3: Render Product Cards into the DOM

### Problem

Render product objects using a plain loop and safe DOM methods.

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

  <title>Render Product Inventory</title>
</head>

<body>
  <h1>Products</h1>

  <p id="product-count"></p>

  <ul id="product-list"></ul>

  <script>
    const products = [
      {
        id: 1,
        name: "Laptop",
        price: 50000,
        quantity: 2,
      },
      {
        id: 2,
        name: "Keyboard",
        price: 3000,
        quantity: 0,
      },
    ];

    const productCount =
      document.getElementById(
        "product-count"
      );

    const productList =
      document.getElementById(
        "product-list"
      );

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

        const card =
          document.createElement("li");

        const name =
          document.createElement("h2");

        const details =
          document.createElement("p");

        card.dataset.id =
          product.id;

        name.textContent =
          `${index + 1}. ${product.name}`;

        details.textContent =
          `KSh ${product.price.toLocaleString()} — ` +
          `Quantity: ${product.quantity}`;

        card.append(
          name,
          details
        );

        productList.appendChild(
          card
        );
      }
    }

    renderProducts();
  </script>
</body>
</html>
```

### Expected DOM Output

```text
Products

Total products: 2

1. Laptop
KSh 50,000 — Quantity: 2

2. Keyboard
KSh 3,000 — Quantity: 0
```

### Final State

The products array remains unchanged.

### Common Mistakes

* Forgetting the initial render.
* Not clearing the list.
* Using `innerHTML` with product names.
* Using the index as a stable ID.

### Small Modification Challenge

Display:

```text
Status: Out of stock
```

when quantity equals zero.

---

# 42. Common Beginner Mistakes

## Mistake 1: Recreating Products Inside Rendering

Incorrect:

```js
function renderProducts() {
  let products = [];
}
```

The function hides the main state and renders an empty list.

---

## Mistake 2: Modifying State During Rendering

Incorrect:

```js
function renderProducts() {
  products[0].quantity++;
}
```

Rendering should only read state.

---

## Mistake 3: Forgetting to Clear the List

Repeated renders produce duplicate cards.

---

## Mistake 4: Treating Price as Text

Incorrect:

```js
price: "KSh 50,000"
```

Correct:

```js
price: 50000
```

Format the price only when displaying it.

---

## Mistake 5: Using Indexes as IDs

Incorrect:

```js
editButton.dataset.id =
  index;
```

Correct:

```js
editButton.dataset.id =
  product.id;
```

---

## Mistake 6: Storing Derived Totals Permanently

This creates synchronization problems.

Calculate totals from current state.

---

## Mistake 7: Not Handling an Empty Array

A blank list confuses users.

---

## Mistake 8: Using Unsafe `innerHTML`

Incorrect:

```js
nameElement.innerHTML =
  product.name;
```

Correct:

```js
nameElement.textContent =
  product.name;
```

---

## Mistake 9: Calling the Render Function Inside Its Loop

This may create recursion or repeated work.

---

## Mistake 10: Forgetting That `dataset` Values Become Strings

When later reading:

```js
button.dataset.id
```

convert it with:

```js
Number()
```

---

# 43. Beginner Questions

1. What object structure is used for each product?
2. What does the `products` array represent?
3. Why is the array the source of truth?
4. What does `editingProductId` store?
5. What value represents Create mode?
6. What does `currentSearchTerm` store?
7. What is derived state?
8. Why should total products use `products.length`?
9. How is total quantity calculated?
10. How is total inventory value calculated?
11. When is a product out of stock?
12. Why should the product list be cleared before rendering?
13. Why should product names use `textContent`?
14. Why should buttons store stable IDs?
15. What function performs the initial render?

---

# 44. Intermediate Questions

1. Why should prices be stored as numbers?
2. Why should quantity be an integer?
3. Why is inventory value derived state?
4. Why should rendering not mutate the array?
5. Why is `index + 1` appropriate for display numbering?
6. Why is the index inappropriate as permanent identity?
7. Why should summary rendering occur even when the array is empty?
8. Why does an empty-state function accept an optional message?
9. Why should Edit and Delete buttons use `type="button"`?
10. How does `dataset.action` support event delegation?
11. Why is a currently editing class derived from UI state?
12. Why should out-of-stock styling be derived from quantity?
13. Why should a large application use several rendering functions?
14. What is the responsibility of `renderProductCard()`?
15. What is the responsibility of `renderProducts()`?

---

# 45. Advanced Questions

1. How could summary calculations be optimized for very large arrays?
2. What trade-offs exist between recalculating totals and storing them?
3. How could product data be normalized by ID?
4. Why can a `Map` provide faster lookup?
5. How could rendering be split into smaller components?
6. What would change if products contained nested supplier objects?
7. How could prices be represented safely for decimal currency?
8. Why can floating-point arithmetic cause currency errors?
9. How could the app support multiple currencies?
10. How could DOM updates be minimized for large inventories?
11. Why might full re-rendering still be preferable in beginner applications?
12. How could derived values be tested independently of the DOM?

---

# 46. Output Prediction Questions

## Question 1

```js
const products = [
  {
    quantity: 2,
  },
  {
    quantity: 5,
  },
];

let total = 0;

for (
  let index = 0;
  index < products.length;
  index++
) {
  total +=
    products[index].quantity;
}

console.log(total);
```

Predict the output.

---

## Question 2

```js
const product = {
  price: 50000,
  quantity: 2,
};

console.log(
  product.price *
  product.quantity
);
```

Predict the output.

---

## Question 3

```js
const products = [];

console.log(
  products.length
);
```

Predict the output.

---

## Question 4

```js
const product = {
  quantity: 0,
};

console.log(
  product.quantity === 0
);
```

Predict the output.

---

## Question 5

```js
let count = 0;

const products = [
  {
    quantity: 0,
  },
  {
    quantity: 3,
  },
  {
    quantity: 0,
  },
];

for (
  let index = 0;
  index < products.length;
  index++
) {
  if (
    products[index].quantity === 0
  ) {
    count++;
  }
}

console.log(count);
```

Predict the output.

---

## Question 6

```js
const product = {
  id: 7,
};

button.dataset.id =
  product.id;

console.log(
  button.dataset.id
);
```

What type of value will normally be read from `dataset.id`?

---

## Question 7

```js
productList.innerHTML = "";

renderProductCard(
  products[0],
  0
);

renderProductCard(
  products[1],
  1
);
```

How many cards should be visible?

---

## Question 8

```js
const product = {
  name: "<strong>Laptop</strong>",
};

nameElement.textContent =
  product.name;
```

Will the text be interpreted as HTML or displayed as text?

---

# 47. Debugging Exercises

For every exercise:

1. Identify the bug.
2. Explain why it occurs.
3. Correct the code.
4. State the expected output.

## Debugging Exercise 1: Total Resets Inside the Loop

```js
for (
  let index = 0;
  index < products.length;
  index++
) {
  let total = 0;

  total +=
    products[index].quantity;
}
```

---

## Debugging Exercise 2: Wrong Loop Condition

```js
for (
  let index = 0;
  index <= products.length;
  index++
) {
  console.log(
    products[index].name
  );
}
```

---

## Debugging Exercise 3: Price Stored as Formatted Text

```js
{
  price: "KSh 50,000"
}
```

The application needs calculations.

---

## Debugging Exercise 4: Index Used as Stable Identity

```js
deleteButton.dataset.id =
  index;
```

---

## Debugging Exercise 5: Container Is Never Cleared

```js
function renderProducts() {
  for (...) {
    productList.appendChild(
      card
    );
  }
}
```

---

## Debugging Exercise 6: Rendering Mutates Quantity

```js
function renderProductCard(
  product
) {
  product.quantity--;

  // Build card.
}
```

---

## Debugging Exercise 7: Unsafe Product Name Rendering

```js
productName.innerHTML =
  product.name;
```

---

## Debugging Exercise 8: Empty State Never Appears

```js
if (
  products.length < 0
) {
  renderEmptyState();
}
```

---

# 48. Coding Exercises

## Exercise 1: Create Product State

Create an array containing three product objects.

---

## Exercise 2: Render Product Names

Use a standard loop to display only names.

---

## Exercise 3: Render Product Prices

Format prices with `toLocaleString()`.

---

## Exercise 4: Calculate Total Units

Use a plain loop.

---

## Exercise 5: Calculate Inventory Value

Use price multiplied by quantity.

---

## Exercise 6: Count Out-of-Stock Products

Use a standard loop.

---

## Exercise 7: Render Stock Status

Display In stock or Out of stock.

---

## Exercise 8: Create Stable Action Buttons

Add Edit and Delete buttons using product IDs.

---

## Exercise 9: Highlight the Edited Product

Use `editingProductId`.

---

## Exercise 10: Render an Empty State

Test with:

```js
products = [];
```

---

## Exercise 11: Render Summary Cards

Update all four summary values.

---

## Exercise 12: Build the Complete Part 1 Interface

Create separate HTML, CSS, and JavaScript files.

---

# 49. Refactoring Exercises

## Exercise 1

Move total-unit calculation into:

```js
calculateTotalUnits();
```

## Exercise 2

Move total-value calculation into:

```js
calculateInventoryValue();
```

## Exercise 3

Move out-of-stock counting into:

```js
calculateOutOfStockCount();
```

## Exercise 4

Move one-card rendering into:

```js
renderProductCard(
  product,
  index
);
```

## Exercise 5

Move summary output into:

```js
renderSummary();
```

## Exercise 6

Move empty-state creation into:

```js
renderEmptyState(message);
```

## Exercise 7

Replace unsafe HTML output with DOM element creation.

## Exercise 8

Replace hard-coded item cards with rendering from state.

---

# 50. Conceptual Questions

1. Why is the `products` array the source of truth?
2. Why is the DOM not the main state?
3. Why should prices remain numeric in state?
4. Why is display formatting separate from stored data?
5. Why are totals considered derived state?
6. Why should rendering clear the container?
7. Why should rendering not modify products?
8. Why should IDs be stable?
9. Why are indexes suitable for display numbering?
10. Why are indexes unsuitable for Edit and Delete identity?
11. Why should an empty state be explicitly rendered?
12. Why should action buttons use dataset attributes?
13. Why does out-of-stock styling depend on quantity?
14. Why should rendering be divided into small functions?
15. How does this part prepare the application for full CRUD?

---

# 51. Part 1 Completion Task

Create this folder:

```text
product-inventory-plain-loops/
```

Inside it, create:

```text
index.html
styles.css
app.js
```

Use this state:

```js
let products = [
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
];

let nextProductId = 4;
let editingProductId = null;
let currentSearchTerm = "";
```

Required functions:

```js
clearMessages();
showError(message);
showSuccess(message);
showInfo(message);
calculateTotalUnits();
calculateInventoryValue();
calculateOutOfStockCount();
renderSummary();
renderEmptyState(message);
renderProductCard(product, displayIndex);
renderProducts();
```

Requirements:

* [ ] Build the complete product form HTML.
* [ ] Add product-name input.
* [ ] Add price input.
* [ ] Add quantity input.
* [ ] Add Add Product button.
* [ ] Add Cancel Edit button.
* [ ] Add Reset Form button.
* [ ] Add search input.
* [ ] Add Clear Search button.
* [ ] Add three message elements.
* [ ] Add four summary cards.
* [ ] Add product list container.
* [ ] Add responsive CSS.
* [ ] Render products using a standard loop.
* [ ] Render price.
* [ ] Render quantity.
* [ ] Render one-product inventory value.
* [ ] Render stock status.
* [ ] Render Edit and Delete buttons.
* [ ] Store stable IDs in `dataset`.
* [ ] Calculate total units with a plain loop.
* [ ] Calculate inventory value with a plain loop.
* [ ] Count out-of-stock products with a plain loop.
* [ ] Render the empty state.
* [ ] Use `textContent`.
* [ ] Clear the product list before rendering.
* [ ] Call the initial render.
* [ ] Do not implement Create yet.
* [ ] Do not implement Edit yet.
* [ ] Do not implement Delete yet.
* [ ] Do not implement Search yet.

Expected summary:

```text
Total Products: 3
Total Units: 7
Inventory Value: KSh 225,000
Out of Stock: 1
```

Expected products:

```text
1. Laptop
Price: KSh 50,000
Quantity: 2
Inventory value: KSh 100,000
Status: In stock

2. Phone
Price: KSh 25,000
Quantity: 5
Inventory value: KSh 125,000
Status: In stock

3. Keyboard
Price: KSh 3,000
Quantity: 0
Inventory value: KSh 0
Status: Out of stock
```

---

## Key Takeaways

* The Product Inventory uses an array of objects as its main state.
* Every product has a stable ID, name, price, and quantity.
* Form mode and search values are separate UI state.
* Counts and totals should be derived from the current array.
* Plain loops can calculate total units, inventory value, and out-of-stock totals.
* Rendering should read state without modifying it.
* A reusable render function should clear and rebuild the product list.
* Product names should be inserted with `textContent`.
* Action buttons should store stable IDs in `dataset`.
* The initial render displays the current state when the page loads.
* The next part will implement product creation and complete form validation.

---

## Continue Learning

| Direction         | Link                                                                                                 |
| ----------------- | ---------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                    |
| Previous Subtopic | [Building a Complete Shopping List with Plain Loops](./01-shopping-list-crud-with-plain-loops.md)    |
| Next Subtopic     | [Creating Products and Validating Form Data](./02-product-inventory-part-2-create-and-validation.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                    |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 1

When you are ready to continue, write:

```text
Next
```

