---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 5: Searching, Filtering, Totals, and Complete Source Code"
difficulty: "Beginner to Intermediate"
estimated_time: "150–180 minutes"
prerequisites:

* "Product Inventory architecture and rendering"
* "Creating products"
* "Updating products"
* "Deleting products"
* "Standard for loops"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 4: Deleting Products"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 6: Progressive Examples and Code Analysis"

---

# Building a Complete Product Inventory with Plain Loops

## Part 5: Searching, Filtering, Totals, and Complete Source Code

## Course Navigation

| Direction         | Link                                                                                                                       |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                          |
| Previous Subtopic | [Product Inventory Part 4: Deleting Products](./02-product-inventory-part-4-deleting-products.md)                          |
| Next Subtopic     | [Product Inventory Part 6: Progressive Examples and Code Analysis](./02-product-inventory-part-6-examples-and-analysis.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                          |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 5

---

## Learning Objectives

By the end of this part, you should be able to:

* Search products by name using a standard `for` loop.
* Perform a case-insensitive search.
* Store the active search term as UI state.
* Distinguish between main state and filtered display data.
* Render matching products without changing the original array.
* Clear an active search.
* Display a search-specific empty state.
* Keep inventory summaries based on all products.
* Display a separate visible-product count.
* Preserve search state after Create, Update, and Delete operations.
* Explain derived state.
* Integrate Create, Read, Update, Delete, Search, validation, event delegation, and totals.
* Run the complete Product Inventory directly in a browser.

---

## What You Will Complete

The finished Product Inventory will support:

* Create a product.
* Read and display products.
* Update a product.
* Delete a product.
* Search products.
* Clear search.
* Detect duplicate names.
* Calculate total products.
* Calculate total units.
* Calculate total inventory value.
* Count out-of-stock products.
* Show visible search-result totals.
* Show empty-state messages.
* Use one form for Create and Update.
* Use event delegation.
* Keep JavaScript state and the DOM synchronized.

---

# 160. Understanding Product Search

## 160.1 Search Is Usually a Read Operation

Searching should not permanently remove products from the main state.

Main state:

```js
let products = [
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 2,
    name: "Phone",
  },
  {
    id: 3,
    name: "Keyboard",
  },
];
```

Search term:

```text
phone
```

Displayed result:

```text
Phone
```

Main state remains:

```js
[
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 2,
    name: "Phone",
  },
  {
    id: 3,
    name: "Keyboard",
  },
]
```

Search changes what the user sees, not the main array.

---

## 160.2 Search Flow

```text
User types in search input
        ↓
Input event runs
        ↓
Read and normalize search text
        ↓
Store currentSearchTerm
        ↓
Find matching products
        ↓
Render matching products
        ↓
Display visible result count
```

---

# 161. Search State

Use:

```js
let currentSearchTerm = "";
```

An empty string means:

```text
No active search
```

A value such as:

```js
"phone"
```

means the visible list should contain products whose names include that text.

---

# 162. Normalizing Search Input

```js
function normalizeSearchTerm(
  searchTerm
) {
  return searchTerm
    .trim()
    .toLowerCase();
}
```

Input:

```text
  PHone
```

Normalized value:

```text
phone
```

This makes search:

* Case-insensitive.
* Resistant to accidental outer spaces.

---

# 163. Matching Product Names

```js
const normalizedName =
  product.name.toLowerCase();

const matches =
  normalizedName.includes(
    currentSearchTerm
  );
```

Examples:

```js
"smartphone".includes("phone");
```

returns:

```text
true
```

```js
"laptop".includes("phone");
```

returns:

```text
false
```

---

# 164. Searching with a Standard `for` Loop

```js
function getVisibleProducts() {
  if (currentSearchTerm === "") {
    return products;
  }

  const matchingProducts = [];

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    const normalizedName =
      product.name.toLowerCase();

    if (
      normalizedName.includes(
        currentSearchTerm
      )
    ) {
      matchingProducts.push(
        product
      );
    }
  }

  return matchingProducts;
}
```

---

# 165. Syntax Breakdown

## `matchingProducts`

```js
const matchingProducts = [];
```

This temporary array stores products that pass the search test.

It is not the main application state.

---

## Current Product

```js
const product =
  products[index];
```

Each repetition reads one product from state.

---

## Name Normalization

```js
product.name.toLowerCase();
```

This allows case-insensitive comparison.

---

## Partial Matching

```js
normalizedName.includes(
  currentSearchTerm
);
```

This returns a boolean.

---

## Adding a Match

```js
matchingProducts.push(
  product
);
```

The matching product reference is added to the temporary result array.

---

# 166. Why Return `products` When Search Is Empty?

```js
if (currentSearchTerm === "") {
  return products;
}
```

No filtering is needed.

The full product list should be displayed.

The render function only reads the returned array.

---

# 167. Search Results Are Derived State

Main state:

```js
products
```

Search state:

```js
currentSearchTerm
```

Derived visible data:

```js
getVisibleProducts()
```

The visible result is calculated from existing state.

Do not permanently store and maintain another array unless necessary.

---

# 168. Do Not Replace the Main State During Search

Incorrect:

```js
products =
  matchingProducts;
```

This permanently loses non-matching products from the current state.

Correct:

```js
const visibleProducts =
  getVisibleProducts();
```

Then render:

```js
visibleProducts
```

without replacing:

```js
products
```

---

# 169. Updating the Render Function

The render function should use:

```js
const visibleProducts =
  getVisibleProducts();
```

Complete structure:

```js
function renderProducts() {
  productList.innerHTML = "";

  renderSummary();

  const visibleProducts =
    getVisibleProducts();

  visibleProductCount.textContent =
    `Showing ${visibleProducts.length} of ${products.length} products`;

  if (
    visibleProducts.length === 0
  ) {
    if (
      currentSearchTerm === ""
    ) {
      renderEmptyState(
        "No products available."
      );
    } else {
      renderEmptyState(
        "No products match your search."
      );
    }

    return;
  }

  for (
    let index = 0;
    index < visibleProducts.length;
    index++
  ) {
    renderProductCard(
      visibleProducts[index],
      index
    );
  }
}
```

---

# 170. Visible Count Versus Total Count

Suppose state contains five products.

Search finds two.

Summary:

```text
Total Products: 5
```

Visible count:

```text
Showing 2 of 5 products
```

These values represent different information.

---

# 171. Should Summary Totals Use All Products?

For this application:

```text
Yes
```

The summary represents the entire inventory.

Therefore:

```js
calculateTotalUnits();
calculateInventoryValue();
calculateOutOfStockCount();
```

use:

```js
products
```

not:

```js
visibleProducts
```

Search changes the displayed list, not the overall inventory totals.

---

# 172. Alternative Summary Behavior

Another application could display filtered totals.

For example:

```text
Value of visible products
```

That would require passing:

```js
visibleProducts
```

into summary functions.

This course will keep the main summaries based on all products.

---

# 173. Handling Search Input

```js
function handleSearchInput(
  event
) {
  currentSearchTerm =
    normalizeSearchTerm(
      event.target.value
    );

  renderProducts();
}
```

Attach:

```js
searchInput.addEventListener(
  "input",
  handleSearchInput
);
```

The `input` event runs whenever the value changes.

---

# 174. Why Use the `input` Event?

The `input` event responds to:

* Typing.
* Deleting characters.
* Pasting.
* Mobile keyboard input.
* Voice input.
* Browser input changes.

This gives immediate search results.

---

# 175. Clearing Search

```js
function clearSearch() {
  currentSearchTerm = "";

  searchInput.value = "";

  renderProducts();

  searchInput.focus();
}
```

Attach:

```js
clearSearchButton.addEventListener(
  "click",
  function () {
    clearMessages();

    clearSearch();

    showInfo(
      "Search was cleared."
    );
  }
);
```

---

# 176. Search-Specific Empty State

When the main array is empty:

```text
No products available.
```

When products exist but none match:

```text
No products match your search.
```

These are different situations.

```js
if (
  visibleProducts.length === 0
) {
  if (
    products.length === 0
  ) {
    renderEmptyState(
      "No products available."
    );
  } else {
    renderEmptyState(
      "No products match your search."
    );
  }

  return;
}
```

---

# 177. Search After Creating a Product

Suppose the active search is:

```text
mouse
```

The user creates:

```js
{
  name: "Mouse",
}
```

After:

```js
renderProducts();
```

Mouse appears because it matches the active term.

The search state should remain:

```js
currentSearchTerm === "mouse";
```

Create should not automatically clear search unless that is an explicit design decision.

---

# 178. Search After Updating a Product

Suppose the active search is:

```text
phone
```

The user renames Phone to:

```text
Tablet
```

After re-rendering:

```text
No products match your search.
```

The product still exists in state, but it no longer matches the visible search.

---

# 179. Search After Deleting a Product

Suppose only Phone matches:

```text
phone
```

After Phone is deleted:

```text
No products match your search.
```

The total product summary also decreases because main state changed.

---

# 180. Editing a Search Result

Edit buttons use stable IDs.

Even though the visible list is temporary and may have different positions, the button stores:

```js
product.id
```

The application finds the correct product in the main state.

This is why stable IDs are essential.

---

# 181. Display Numbering During Search

Visible results can be numbered:

```text
1. Phone
2. Smartphone Case
```

using the visible array index.

This number is only a display number.

It is not the product's ID or original main-state index.

---

# 182. Exactly Three Complete Search Examples

## Example 1: Search Names Without the DOM

### Problem

Find products whose names contain `"phone"`.

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
  {
    id: 3,
    name: "Smartphone Case",
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
  {
    id: 3,
    name: "Smartphone Case",
  },
];

const searchTerm =
  "phone";

const matches = [];

for (
  let index = 0;
  index < products.length;
  index++
) {
  const product =
    products[index];

  if (
    product.name
      .toLowerCase()
      .includes(
        searchTerm
      )
  ) {
    matches.push(
      product
    );
  }
}

for (
  let index = 0;
  index < matches.length;
  index++
) {
  console.log(
    matches[index].name
  );
}
```

### Expected Console Output

```text
Phone
Smartphone Case
```

### Final State

The original products array remains unchanged.

### Common Mistakes

* Replacing the products array with matches.
* Forgetting lowercase conversion.
* Comparing with strict equality instead of `includes()`.
* Returning after the first match.

### Small Modification Challenge

Search for:

```text
top
```

Predict the matching product.

---

## Example 2: Search and Visible Count

### Problem

Calculate matching products and display a visible count.

### Complete Code

```js
const products = [
  {
    name: "Laptop",
  },
  {
    name: "Phone",
  },
  {
    name: "Keyboard",
  },
  {
    name: "Headphones",
  },
];

const searchTerm =
  "phone";

const visibleProducts = [];

for (
  let index = 0;
  index < products.length;
  index++
) {
  const product =
    products[index];

  if (
    product.name
      .toLowerCase()
      .includes(
        searchTerm
      )
  ) {
    visibleProducts.push(
      product
    );
  }
}

console.log(
  `Showing ${visibleProducts.length} of ${products.length} products`
);
```

### Expected Console Output

```text
Showing 2 of 4 products
```

### Final State

The main array still contains four products.

### Common Mistakes

* Using the total array length for both values.
* Counting characters instead of matching products.
* Clearing state when no match exists.

### Small Modification Challenge

Print every matching name after the count.

---

## Example 3: Live DOM Search

### Problem

Render matching products while the user types.

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

  <title>Product Search</title>
</head>

<body>
  <h1>Product Search</h1>

  <input
    id="search-input"
    type="search"
    placeholder="Search products"
  />

  <button
    id="clear-search-button"
    type="button"
  >
    Clear
  </button>

  <p id="visible-count"></p>

  <ul id="product-list"></ul>

  <script>
    const products = [
      {
        id: 1,
        name: "Laptop",
      },
      {
        id: 2,
        name: "Phone",
      },
      {
        id: 3,
        name: "Keyboard",
      },
      {
        id: 4,
        name: "Headphones",
      },
    ];

    let currentSearchTerm = "";

    const searchInput =
      document.getElementById(
        "search-input"
      );

    const clearSearchButton =
      document.getElementById(
        "clear-search-button"
      );

    const visibleCount =
      document.getElementById(
        "visible-count"
      );

    const productList =
      document.getElementById(
        "product-list"
      );

    function getVisibleProducts() {
      if (
        currentSearchTerm === ""
      ) {
        return products;
      }

      const matches = [];

      for (
        let index = 0;
        index < products.length;
        index++
      ) {
        const product =
          products[index];

        if (
          product.name
            .toLowerCase()
            .includes(
              currentSearchTerm
            )
        ) {
          matches.push(
            product
          );
        }
      }

      return matches;
    }

    function renderProducts() {
      productList.innerHTML = "";

      const visibleProducts =
        getVisibleProducts();

      visibleCount.textContent =
        `Showing ${visibleProducts.length} of ${products.length} products`;

      if (
        visibleProducts.length === 0
      ) {
        const emptyItem =
          document.createElement(
            "li"
          );

        emptyItem.textContent =
          "No products match your search.";

        productList.appendChild(
          emptyItem
        );

        return;
      }

      for (
        let index = 0;
        index < visibleProducts.length;
        index++
      ) {
        const listItem =
          document.createElement(
            "li"
          );

        listItem.textContent =
          visibleProducts[index].name;

        productList.appendChild(
          listItem
        );
      }
    }

    searchInput.addEventListener(
      "input",
      function (event) {
        currentSearchTerm =
          event.target.value
            .trim()
            .toLowerCase();

        renderProducts();
      }
    );

    clearSearchButton.addEventListener(
      "click",
      function () {
        currentSearchTerm = "";
        searchInput.value = "";

        renderProducts();
        searchInput.focus();
      }
    );

    renderProducts();
  </script>
</body>
</html>
```

### Expected Initial DOM

```text
Showing 4 of 4 products

Laptop
Phone
Keyboard
Headphones
```

### Expected DOM for Search `phone`

```text
Showing 2 of 4 products

Phone
Headphones
```

### Expected DOM for Search `camera`

```text
Showing 0 of 4 products

No products match your search.
```

### Final State

The products array remains unchanged throughout searching.

### Common Mistakes

* Mutating the main array.
* Forgetting to render after input changes.
* Clearing search state but not the input.
* Clearing the input but not search state.

### Small Modification Challenge

Display:

```text
No products available.
```

when the main state array is empty.

---

# 183. Complete Integrated HTML

Create or update:

```text
index.html
```

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
        Manage inventory using JavaScript
        state, the DOM, and plain loops.
      </p>
    </header>

    <section class="panel">
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
        class="message"
        role="alert"
      ></p>

      <p
        id="success-message"
        class="message"
        role="status"
      ></p>

      <p
        id="info-message"
        class="message"
        role="status"
      ></p>
    </section>

    <section class="panel">
      <h2>Search Products</h2>

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
        <p id="total-product-count">0</p>
      </article>

      <article class="summary-card">
        <h2>Total Units</h2>
        <p id="total-unit-count">0</p>
      </article>

      <article class="summary-card">
        <h2>Inventory Value</h2>
        <p id="total-inventory-value">
          KSh 0
        </p>
      </article>

      <article class="summary-card">
        <h2>Out of Stock</h2>
        <p id="out-of-stock-count">0</p>
      </article>
    </section>

    <section class="panel">
      <div class="section-heading-row">
        <h2>Products</h2>

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

# 184. Complete Integrated CSS

Create or update:

```text
styles.css
```

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #f4f4f4;
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

.page-header,
.panel,
.summary-grid {
  margin-bottom: 20px;
}

.panel,
.summary-card {
  padding: 18px;
  background: #ffffff;
  border: 1px solid #d0d0d0;
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

.summary-grid {
  display: grid;
  grid-template-columns:
    repeat(
      auto-fit,
      minmax(180px, 1fr)
    );
  gap: 14px;
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

# 185. Complete Integrated JavaScript

Create or replace:

```text
app.js
```

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

function generateProductId() {
  const productId =
    nextProductId;

  nextProductId++;

  return productId;
}

function findProductById(
  productId
) {
  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].id ===
      productId
    ) {
      return products[index];
    }
  }

  return null;
}

function findProductIndexById(
  productId
) {
  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].id ===
      productId
    ) {
      return index;
    }
  }

  return -1;
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
        "Product price is required.",
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

function updateProduct(data) {
  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].id ===
      editingProductId
    ) {
      products[index].name =
        data.name;

      products[index].price =
        data.price;

      products[index].quantity =
        data.quantity;

      return products[index];
    }
  }

  return null;
}

function confirmProductDeletion(
  product
) {
  return window.confirm(
    `Are you sure you want to delete ${product.name}?`
  );
}

function deleteProductById(
  productId
) {
  const productIndex =
    findProductIndexById(
      productId
    );

  if (
    productIndex === -1
  ) {
    return {
      success: false,
      deletedProduct: null,
      message:
        "The selected product no longer exists.",
    };
  }

  const removedProducts =
    products.splice(
      productIndex,
      1
    );

  return {
    success: true,
    deletedProduct:
      removedProducts[0],
    message:
      "Product deleted successfully.",
  };
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
      products[index].quantity ===
      0
    ) {
      count++;
    }
  }

  return count;
}

function normalizeSearchTerm(
  searchTerm
) {
  return searchTerm
    .trim()
    .toLowerCase();
}

function getVisibleProducts() {
  if (
    currentSearchTerm === ""
  ) {
    return products;
  }

  const matchingProducts = [];

  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    const product =
      products[index];

    const normalizedName =
      product.name.toLowerCase();

    if (
      normalizedName.includes(
        currentSearchTerm
      )
    ) {
      matchingProducts.push(
        product
      );
    }
  }

  return matchingProducts;
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
  message
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

  stockStatus.textContent =
    product.quantity === 0
      ? "Status: Out of stock"
      : "Status: In stock";

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

  const visibleProducts =
    getVisibleProducts();

  visibleProductCount.textContent =
    `Showing ${visibleProducts.length} of ${products.length} products`;

  if (
    visibleProducts.length === 0
  ) {
    if (products.length === 0) {
      renderEmptyState(
        "No products available."
      );
    } else {
      renderEmptyState(
        "No products match your search."
      );
    }

    return;
  }

  for (
    let index = 0;
    index < visibleProducts.length;
    index++
  ) {
    renderProductCard(
      visibleProducts[index],
      index
    );
  }
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

function loadProductIntoForm(
  product
) {
  productNameInput.value =
    product.name;

  productPriceInput.value =
    product.price;

  productQuantityInput.value =
    product.quantity;
}

function setEditModeUI(
  product
) {
  editingProductId =
    product.id;

  productFormTitle.textContent =
    "Edit Product";

  submitButton.textContent =
    "Update Product";

  cancelEditButton.hidden =
    false;
}

function startEditingProduct(
  productId
) {
  clearMessages();

  const product =
    findProductById(
      productId
    );

  if (product === null) {
    showError(
      "The selected product no longer exists."
    );

    renderProducts();

    return false;
  }

  loadProductIntoForm(
    product
  );

  setEditModeUI(
    product
  );

  renderProducts();

  productNameInput.focus();
  productNameInput.select();

  return true;
}

function cancelProductEdit() {
  const wasEditing =
    editingProductId !== null;

  resetFormToCreateMode();
  renderProducts();

  if (wasEditing) {
    showInfo(
      "Product editing was canceled."
    );
  }
}

function handleDeleteRequest(
  productId
) {
  clearMessages();

  const product =
    findProductById(
      productId
    );

  if (product === null) {
    showError(
      "The selected product no longer exists."
    );

    renderProducts();

    return false;
  }

  const shouldDelete =
    confirmProductDeletion(
      product
    );

  if (!shouldDelete) {
    showInfo(
      `${product.name} was not deleted.`
    );

    return false;
  }

  const result =
    deleteProductById(
      productId
    );

  if (!result.success) {
    showError(
      result.message
    );

    renderProducts();

    return false;
  }

  if (
    editingProductId ===
    result.deletedProduct.id
  ) {
    resetFormToCreateMode();
  }

  renderProducts();

  showSuccess(
    `${result.deletedProduct.name} was deleted successfully.`
  );

  return true;
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
    const newProduct =
      createProduct(
        validationResult.normalizedData
      );

    resetFormToCreateMode();
    renderProducts();

    showSuccess(
      `${newProduct.name} was added successfully.`
    );

    return;
  }

  const updatedProduct =
    updateProduct(
      validationResult.normalizedData
    );

  if (
    updatedProduct === null
  ) {
    showError(
      "The selected product could not be updated because it no longer exists."
    );

    resetFormToCreateMode();
    renderProducts();

    return;
  }

  const updatedName =
    updatedProduct.name;

  resetFormToCreateMode();
  renderProducts();

  showSuccess(
    `${updatedName} was updated successfully.`
  );
}

function handleProductListClick(
  event
) {
  const clickedButton =
    event.target.closest(
      "button"
    );

  if (
    clickedButton === null
  ) {
    return;
  }

  const action =
    clickedButton.dataset.action;

  const productId =
    Number(
      clickedButton.dataset.id
    );

  if (
    !Number.isInteger(productId) ||
    productId <= 0
  ) {
    clearMessages();

    showError(
      "The selected product ID is invalid."
    );

    return;
  }

  if (action === "edit") {
    startEditingProduct(
      productId
    );

    return;
  }

  if (action === "delete") {
    handleDeleteRequest(
      productId
    );
  }
}

function handleSearchInput(
  event
) {
  currentSearchTerm =
    normalizeSearchTerm(
      event.target.value
    );

  renderProducts();
}

function clearSearch() {
  currentSearchTerm = "";

  searchInput.value = "";

  renderProducts();

  searchInput.focus();
}

productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);

productList.addEventListener(
  "click",
  handleProductListClick
);

searchInput.addEventListener(
  "input",
  handleSearchInput
);

cancelEditButton.addEventListener(
  "click",
  function () {
    clearMessages();

    cancelProductEdit();
  }
);

resetButton.addEventListener(
  "click",
  function () {
    clearMessages();

    const wasEditing =
      editingProductId !== null;

    resetFormToCreateMode();
    renderProducts();

    if (wasEditing) {
      showInfo(
        "Editing was canceled and the form was reset."
      );
    } else {
      showInfo(
        "The form was reset."
      );
    }
  }
);

clearSearchButton.addEventListener(
  "click",
  function () {
    clearMessages();

    clearSearch();

    showInfo(
      "Search was cleared."
    );
  }
);

setCreateModeUI();
renderProducts();
```

---

# 186. Complete Application Flow

## Create

```text
Form submit
→ validate
→ create object
→ push to products
→ reset form
→ render
```

## Read

```text
renderProducts()
→ calculate visible products
→ create DOM cards
→ display summaries
```

## Update

```text
Edit click
→ load product
→ submit updated values
→ mutate matching object
→ reset form
→ render
```

## Delete

```text
Delete click
→ confirm
→ find current index
→ splice one product
→ render
```

## Search

```text
Input changes
→ update currentSearchTerm
→ calculate matching products
→ render visible results
```

---

# 187. Expected Initial Output

```text
Total Products: 3
Total Units: 7
Inventory Value: KSh 225,000
Out of Stock: 1

Showing 3 of 3 products

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

# 188. Expected Search Output

Search:

```text
phone
```

Expected:

```text
Total Products: 3
Total Units: 7
Inventory Value: KSh 225,000
Out of Stock: 1

Showing 1 of 3 products

1. Phone
Price: KSh 25,000
Quantity: 5
Inventory value: KSh 125,000
Status: In stock
[Edit] [Delete]
```

The main summaries remain based on all three products.

---

# 189. Common Beginner Mistakes

## Mistake 1: Replacing State with Search Results

```js
products =
  getVisibleProducts();
```

This loses non-matching products.

---

## Mistake 2: Searching the Rendered DOM Instead of State

The array should remain the source of truth.

---

## Mistake 3: Forgetting Case Normalization

Search becomes unexpectedly case-sensitive.

---

## Mistake 4: Clearing the Input but Not Search State

```js
searchInput.value = "";
```

without:

```js
currentSearchTerm = "";
```

The list may remain filtered.

---

## Mistake 5: Clearing Search State but Not the Input

The full list appears while the input still shows old text.

---

## Mistake 6: Using Filtered Totals Accidentally

The main summary should use the full array in this application.

---

## Mistake 7: Using Visible Index as Product ID

Search positions are temporary.

---

## Mistake 8: Mutating State During `getVisibleProducts()`

Search should only read state.

---

## Mistake 9: Forgetting to Re-render on Input

The displayed list does not change.

---

## Mistake 10: Displaying the Wrong Empty-State Message

Distinguish an empty inventory from no search matches.

---

# 190. Beginner Questions

1. Is search a Create, Read, Update, or Delete operation?
2. What does `currentSearchTerm` store?
3. What does an empty search term mean?
4. Why should the search term be trimmed?
5. Why should search use lowercase values?
6. What does `includes()` return?
7. What does `getVisibleProducts()` return?
8. Should search replace the main products array?
9. Why is the matching array temporary?
10. What does the `input` event respond to?
11. Why should Clear Search reset two values?
12. Why are total products and visible products different?
13. Which array should inventory totals use?
14. What message appears when no search results exist?
15. Why do Edit and Delete still use stable IDs?

---

# 191. Intermediate Questions

1. Why are search results considered derived state?
2. Why should `getVisibleProducts()` avoid mutation?
3. Why can the full products array be returned when search is empty?
4. What happens when an edited product stops matching the search?
5. What happens when the only matching product is deleted?
6. Why should search state normally survive Create?
7. Why should summary values remain based on all products?
8. When might filtered summaries be preferable?
9. Why is event delegation useful for filtered lists?
10. Why should visible numbering not be treated as identity?
11. Why should empty inventory and empty search results have different messages?
12. Why should search logic be separated from rendering?
13. How does `includes()` differ from equality comparison?
14. How does search remain synchronized after CRUD operations?
15. Why does a reusable render function simplify this application?

---

# 192. Advanced Questions

1. How could search be debounced for a large inventory?
2. What is the performance cost of searching on every keystroke?
3. How could multiple fields be searched?
4. How could price-range filtering be added?
5. How could products be sorted while preserving stable IDs?
6. How could active filters be represented in one UI-state object?
7. Why might derived results be memoized?
8. How could search terms be highlighted safely?
9. How could URL query parameters preserve search state?
10. How could server-side search differ from client-side search?
11. How could asynchronous search results become stale?
12. How can search functionality be unit-tested?

---

# 193. Output Prediction Questions

## Question 1

```js
console.log(
  "smartphone"
    .includes("phone")
);
```

Predict the output.

---

## Question 2

```js
console.log(
  "Laptop"
    .toLowerCase()
);
```

Predict the output.

---

## Question 3

```js
const products = [
  {
    name: "Phone",
  },
];

const matches = [];

matches.push(
  products[0]
);

console.log(
  matches.length
);

console.log(
  products.length
);
```

Predict both outputs.

---

## Question 4

```js
let currentSearchTerm =
  "phone";

currentSearchTerm = "";

console.log(
  currentSearchTerm
);
```

Predict the output.

---

## Question 5

```js
const products = [
  {
    name: "Laptop",
  },
  {
    name: "Phone",
  },
];

let count = 0;

for (
  let index = 0;
  index < products.length;
  index++
) {
  if (
    products[index].name
      .toLowerCase()
      .includes("p")
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
const visibleProducts = [];
const products = [
  {
    name: "Laptop",
  },
];

console.log(
  visibleProducts.length
);

console.log(
  products.length
);
```

Predict both outputs.

---

## Question 7

```js
const searchTerm =
  "  PHONE  "
    .trim()
    .toLowerCase();

console.log(
  searchTerm
);
```

Predict the output.

---

## Question 8

```js
const product = {
  id: 7,
};

const visibleIndex = 0;

console.log(
  product.id
);

console.log(
  visibleIndex
);
```

Which value should an Edit button store?

---

# 194. Debugging Exercises

For every exercise:

1. Identify the bug.
2. Explain why it occurs.
3. Correct the code.
4. State the expected result.

## Debugging Exercise 1: Search Destroys Main State

```js
products =
  matchingProducts;
```

---

## Debugging Exercise 2: Case-Sensitive Search

```js
if (
  product.name.includes(
    searchTerm
  )
) {
  // Match.
}
```

---

## Debugging Exercise 3: False Returned Too Early

```js
function hasMatch(term) {
  for (
    let index = 0;
    index < products.length;
    index++
  ) {
    if (
      products[index].name
        .includes(term)
    ) {
      return true;
    }

    return false;
  }
}
```

---

## Debugging Exercise 4: Search State Not Cleared

```js
searchInput.value = "";

renderProducts();
```

---

## Debugging Exercise 5: Search Input Not Cleared

```js
currentSearchTerm = "";

renderProducts();
```

---

## Debugging Exercise 6: Wrong Visible Count

```js
visibleProductCount.textContent =
  products.length;
```

A search is active.

---

## Debugging Exercise 7: Filtered Index Used as ID

```js
editButton.dataset.id =
  displayIndex;
```

---

## Debugging Exercise 8: No Search Re-render

```js
searchInput.addEventListener(
  "input",
  function (event) {
    currentSearchTerm =
      event.target.value;
  }
);
```

---

# 195. Coding Exercises

## Exercise 1: Normalize Search

Create:

```js
normalizeSearchTerm(term);
```

## Exercise 2: Find Name Matches

Use a standard loop and `includes()`.

## Exercise 3: Preserve Main State

Return a new temporary matching array.

## Exercise 4: Handle Empty Search

Display all products.

## Exercise 5: Display Visible Count

Show visible and total values.

## Exercise 6: Search-Specific Empty State

Show the correct message.

## Exercise 7: Live Search

Use the `input` event.

## Exercise 8: Clear Search

Reset state, input, DOM, and focus.

## Exercise 9: Edit During Search

Use the product's stable ID.

## Exercise 10: Delete During Search

Confirm results update correctly.

## Exercise 11: Create During Search

Keep the active search after creation.

## Exercise 12: Update Away from Search

Rename a matching product so it disappears.

---

# 196. Refactoring Exercises

## Exercise 1

Move normalization into:

```js
normalizeSearchTerm(term);
```

## Exercise 2

Move visible-product calculation into:

```js
getVisibleProducts();
```

## Exercise 3

Move search events into:

```js
handleSearchInput(event);
```

## Exercise 4

Move reset logic into:

```js
clearSearch();
```

## Exercise 5

Refactor rendering to accept visible products.

## Exercise 6

Separate total summary from visible-result count.

## Exercise 7

Replace repeated lowercasing with normalized variables.

## Exercise 8

Refactor one large application into state, validation, CRUD, search, render, and event sections.

---

# 197. Conceptual Questions

1. Why should searching not mutate the main array?
2. Why are search results derived state?
3. Why should the active search term be stored?
4. Why should normalization happen before comparison?
5. Why is partial matching useful?
6. Why should search results preserve product references and IDs?
7. Why should total inventory summaries use all products?
8. Why should visible count use filtered results?
9. Why should the render function handle both full and filtered lists?
10. Why should Clear Search update both JavaScript and DOM state?
11. Why should filtered positions not be used for Update or Delete?
12. Why does search remain correct after Create, Update, and Delete?
13. Why should no-match output differ from no-products output?
14. Why is event delegation compatible with search rendering?
15. How does this application demonstrate state-driven UI design?

---

# 198. Part 5 Completion Task

Complete:

```text
product-inventory-plain-loops/
├── index.html
├── styles.css
└── app.js
```

Required search functions:

```js
normalizeSearchTerm(searchTerm);
getVisibleProducts();
handleSearchInput(event);
clearSearch();
```

Required integrated functions:

```js
generateProductId();
findProductById(productId);
findProductIndexById(productId);
productNameExists(name, ignoredId);
readProductForm();
validateProductData(data);
focusInvalidField(field);
createProduct(data);
updateProduct(data);
deleteProductById(productId);
calculateTotalUnits();
calculateInventoryValue();
calculateOutOfStockCount();
renderSummary();
renderEmptyState(message);
renderProductCard(product, displayIndex);
renderProducts();
setCreateModeUI();
resetFormToCreateMode();
loadProductIntoForm(product);
setEditModeUI(product);
startEditingProduct(productId);
cancelProductEdit();
handleDeleteRequest(productId);
handleProductFormSubmit(event);
handleProductListClick(event);
handleSearchInput(event);
clearSearch();
```

Feature checklist:

* [ ] Add products.
* [ ] Display products.
* [ ] Edit products.
* [ ] Delete products.
* [ ] Confirm deletion.
* [ ] Search by partial product name.
* [ ] Perform case-insensitive search.
* [ ] Clear search.
* [ ] Preserve the main state during search.
* [ ] Preserve stable IDs.
* [ ] Validate names.
* [ ] Validate prices.
* [ ] Validate quantities.
* [ ] Prevent duplicates.
* [ ] Use one form for Create and Update.
* [ ] Include Cancel Edit.
* [ ] Include Reset Form.
* [ ] Use event delegation.
* [ ] Render total products.
* [ ] Render total units.
* [ ] Render inventory value.
* [ ] Render out-of-stock count.
* [ ] Render visible-product count.
* [ ] Show inventory empty state.
* [ ] Show search empty state.
* [ ] Re-render after every state mutation.
* [ ] Keep search active after CRUD actions.
* [ ] Use `textContent` for user-controlled names.
* [ ] Avoid duplicate event listeners.
* [ ] Avoid duplicate rendering.
* [ ] Run directly in the browser.

Manual testing checklist:

* [ ] Search `phone`.
* [ ] Search `PHOne`.
* [ ] Search with outer spaces.
* [ ] Search for a partial name.
* [ ] Search for no matches.
* [ ] Clear the search.
* [ ] Add a product matching the current search.
* [ ] Add a product not matching the current search.
* [ ] Rename a matching product so it stops matching.
* [ ] Rename a non-matching product so it begins matching.
* [ ] Delete the only matching product.
* [ ] Edit a filtered result.
* [ ] Delete a filtered result.
* [ ] Confirm summary values still use all products.
* [ ] Confirm visible count uses filtered products.
* [ ] Delete all products.
* [ ] Confirm `No products available`.
* [ ] Add a product again.
* [ ] Confirm the active search still behaves correctly.
* [ ] Confirm no cards are duplicated.

Expected search example:

```text
Search: phone

Showing 1 of 3 products

1. Phone
Price: KSh 25,000
Quantity: 5
Inventory value: KSh 125,000
Status: In stock
[Edit] [Delete]
```

Expected no-match output:

```text
Showing 0 of 3 products

No products match your search.
```

Expected empty-inventory output:

```text
Total Products: 0
Total Units: 0
Inventory Value: KSh 0
Out of Stock: 0

Showing 0 of 0 products

No products available.
```

---

## Key Takeaways

* Searching is a Read operation.
* Search should not replace or mutate the main products array.
* `currentSearchTerm` stores the active search UI state.
* Search values should be trimmed and normalized.
* A standard loop can build a temporary matching-products array.
* Search results are derived state.
* Stable IDs remain essential in filtered views.
* Main inventory summaries can remain based on all products.
* Visible-result counts should use the filtered list.
* CRUD actions should re-render using the active search state.
* Empty inventory and no search matches require different messages.
* The complete Product Inventory now supports Create, Read, Update, Delete, Search, validation, event delegation, totals, and empty-state handling.
* The next part will reinforce the application through progressive examples and detailed code analysis.

---

## Continue Learning

| Direction         | Link                                                                                                                       |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                          |
| Previous Subtopic | [Product Inventory Part 4: Deleting Products](./02-product-inventory-part-4-deleting-products.md)                          |
| Next Subtopic     | [Product Inventory Part 6: Progressive Examples and Code Analysis](./02-product-inventory-part-6-examples-and-analysis.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                          |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 5

When you are ready to continue, write:

```text
Next
```

