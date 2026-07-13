---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 3: Updating Products"
difficulty: "Beginner to Intermediate"
estimated_time: "120–150 minutes"
prerequisites:

* "Product Inventory architecture and rendering"
* "Creating products"
* "Form validation"
* "Finding items by ID"
* "Create mode and edit mode"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 2: Creating Products and Validating Form Data"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 4: Deleting Products"

---

# Building a Complete Product Inventory with Plain Loops

## Part 3: Updating Products

## Course Navigation

| Direction         | Link                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                              |
| Previous Subtopic | [Product Inventory Part 2: Creating Products and Validating Form Data](./02-product-inventory-part-2-create-and-validation.md) |
| Next Subtopic     | [Product Inventory Part 4: Deleting Products](./02-product-inventory-part-4-deleting-products.md)                              |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                              |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 3

---

## Learning Objectives

By the end of this part, you should be able to:

* Find a product by its stable ID using a plain `for` loop.
* Load an existing product into the form.
* Enter and exit edit mode.
* Use one form for both Create and Update.
* Preserve the product ID during an update.
* Update only the matching product.
* Reuse Create validation during Update.
* Ignore the currently edited product during duplicate-name checks.
* Handle stale or missing product IDs.
* Cancel editing without changing state.
* Re-render summaries and product cards after an update.
* Keep form state, application state, and DOM output synchronized.
* Explain the full Update flow from button click to visible result.

---

## What You Will Build

You will extend the Product Inventory so that clicking **Edit**:

1. Finds the selected product.
2. Loads its values into the form.
3. Changes the form to edit mode.
4. Allows the user to submit changes.
5. Updates only the matching object.
6. Preserves the original ID.
7. Returns the form to Create mode.
8. Re-renders the inventory.

Initial product:

```js
{
  id: 2,
  name: "Phone",
  price: 25000,
  quantity: 5,
}
```

Updated product:

```js
{
  id: 2,
  name: "Smartphone",
  price: 28000,
  quantity: 7,
}
```

The ID remains:

```text
2
```

---

# 87. Understanding the Update Flow

## 87.1 Complete Update Sequence

```text
User clicks Edit
        ↓
Read product ID from dataset
        ↓
Convert ID to number
        ↓
Find matching product in state
        ↓
Load current values into form
        ↓
Set editingProductId
        ↓
Change form title and buttons
        ↓
User edits values
        ↓
Submit form
        ↓
Read and validate values
        ↓
Find matching product again
        ↓
Update editable properties
        ↓
Preserve stable ID
        ↓
Reset form to Create mode
        ↓
Render current state
        ↓
Show success message
```

---

# 88. Finding a Product by ID

```js
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
```

This function returns:

* The matching product object.
* `null` when no match exists.

---

# 89. Why Search by ID Instead of Name?

Names can:

* Change.
* Repeat accidentally.
* Differ in capitalization.
* Contain extra spaces.

IDs should remain stable.

Correct:

```js
product.id === productId
```

Less reliable:

```js
product.name === productName
```

---

# 90. Loading Product Values into the Form

```js
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
```

This changes DOM form values only.

It does not update the product object.

---

# 91. Form State Versus Application State

Suppose the current state contains:

```js
{
  id: 2,
  name: "Phone",
}
```

The form loads:

```text
Phone
```

The user types:

```text
Smartphone
```

At this moment:

```js
productNameInput.value
```

is:

```text
Smartphone
```

but application state still contains:

```text
Phone
```

State changes only after successful submission.

---

# 92. Entering Edit Mode

```js
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
```

Create mode:

```js
editingProductId === null;
```

Edit mode:

```js
editingProductId === product.id;
```

---

# 93. Starting Product Editing

```js
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
```

---

# 94. Why Re-render When Edit Mode Starts?

The render function applies:

```js
currently-editing
```

when:

```js
product.id ===
editingProductId
```

Re-rendering:

* Highlights the selected product.
* Disables its Edit button.
* Keeps the DOM consistent with UI state.

---

# 95. Preserving Edit State During Rendering

The render function should read:

```js
editingProductId
```

but should not reset it.

Incorrect:

```js
function renderProducts() {
  editingProductId = null;
}
```

Correct:

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

---

# 96. Canceling Edit Mode

```js
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
```

Canceling:

* Clears temporary form values.
* Removes edit highlighting.
* Hides the Cancel button.
* Does not change the products array.

---

# 97. Update Validation

The same validation function used by Create can be reused.

```js
const validationResult =
  validateProductData(
    rawData
  );
```

The duplicate check already receives:

```js
editingProductId
```

```js
productNameExists(
  data.name,
  editingProductId
);
```

---

# 98. Why Ignore the Current Product in Duplicate Validation?

State:

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
]
```

The user edits Phone but keeps the same name.

A duplicate checker sees `"Phone"` in the array.

Without ignoring ID `2`, it would reject the product as a duplicate of itself.

Correct behavior:

```js
if (
  product.id ===
  ignoredProductId
) {
  continue;
}
```

---

# 99. Updating with a Standard `for` Loop

```js
function updateProduct(
  data
) {
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
```

This function:

* Searches by stable ID.
* Updates only one object.
* Preserves the ID.
* Returns the updated product.
* Returns `null` when missing.

---

# 100. What Should Be Updated?

Editable properties:

```js
name
price
quantity
```

Stable identity:

```js
id
```

Do not write:

```js
products[index].id =
  generateProductId();
```

That would incorrectly create a new identity.

---

# 101. Why Return the Updated Product?

The caller can use the returned object for:

* Success messages.
* Testing.
* Logging.
* Additional UI updates.

Example:

```js
const updatedProduct =
  updateProduct(data);

if (
  updatedProduct === null
) {
  // Handle failure.
}
```

---

# 102. Handling a Missing Product During Update

A product may disappear after edit mode begins.

Example:

```js
editingProductId = 2;
```

but state no longer contains ID `2`.

Then:

```js
updateProduct(data)
```

returns:

```text
null
```

Handle it:

```js
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
```

---

# 103. Updated Submit Handler

Replace the earlier Create-only handler with:

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
```

---

# 104. Why Store the Updated Name Before Reset?

After:

```js
resetFormToCreateMode();
```

the form input becomes empty.

If you try:

```js
productNameInput.value
```

after reset, the success message may contain no name.

Use the returned product:

```js
const updatedName =
  updatedProduct.name;
```

---

# 105. Handling Edit Button Clicks

Use event delegation on the product list.

```js
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
  }
}
```

Attach it once:

```js
productList.addEventListener(
  "click",
  handleProductListClick
);
```

---

# 106. Why Use `closest("button")`?

A button might contain another element:

```html
<button>
  <span>Edit</span>
</button>
```

If the user clicks the `<span>`, then:

```js
event.target
```

is the span.

Using:

```js
event.target.closest(
  "button"
)
```

finds the surrounding button.

---

# 107. Cancel Edit Button

```js
cancelEditButton.addEventListener(
  "click",
  function () {
    clearMessages();

    cancelProductEdit();
  }
);
```

The button already uses:

```html
type="button"
```

so it will not submit the form.

---

# 108. Reset Button During Edit Mode

The Reset Form button should also return to Create mode.

```js
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
```

---

# 109. Switching Directly Between Products

Suppose Laptop is being edited.

The user clicks Edit on Phone.

`startEditingProduct(2)`:

* Replaces `editingProductId`.
* Loads Phone values.
* Updates highlight.
* Keeps the form in edit mode.

No state mutation occurs.

The unfinished Laptop changes are discarded from the form.

---

# 110. Optional Unsaved-Changes Warning

For a more advanced application, you might track:

```js
let formHasUnsavedChanges =
  false;
```

Then ask before switching products.

This is not required for the current implementation.

---

# 111. Re-rendered Summaries After Update

Suppose Phone changes from:

```js
{
  price: 25000,
  quantity: 5,
}
```

to:

```js
{
  price: 28000,
  quantity: 7,
}
```

Old value:

```text
25,000 × 5 = 125,000
```

New value:

```text
28,000 × 7 = 196,000
```

The summary must be recalculated after:

```js
renderProducts();
```

---

# 112. Example Update Calculation

Initial state:

```text
Laptop: 50,000 × 2 = 100,000
Phone: 25,000 × 5 = 125,000
Keyboard: 3,000 × 0 = 0
```

Total:

```text
KSh 225,000
```

After updating Phone:

```text
Laptop: 100,000
Smartphone: 28,000 × 7 = 196,000
Keyboard: 0
```

New total:

```text
KSh 296,000
```

Total units:

```text
2 + 7 + 0 = 9
```

---

# 113. Complete Part 3 JavaScript Additions

Add or update these functions in `app.js`:

```js
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

function updateProduct(
  data
) {
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
  }
}
```

Use these listeners once:

```js
productForm.addEventListener(
  "submit",
  handleProductFormSubmit
);

productList.addEventListener(
  "click",
  handleProductListClick
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
```

Initialize:

```js
setCreateModeUI();
renderProducts();
```

> Ensure the submit and list listeners are registered only once. Do not leave older duplicate listeners in the file.

---

# 114. Expected Initial Edit Flow

User clicks Edit on Phone.

Form changes from:

```text
Add Product

Name: empty
Price: empty
Quantity: empty

[Add Product] [Reset Form]
```

to:

```text
Edit Product

Name: Phone
Price: 25000
Quantity: 5

[Update Product] [Cancel Edit] [Reset Form]
```

Product card:

```text
2. Phone
...
Edit button disabled
Card highlighted
```

---

# 115. Expected Update Result

User changes:

```text
Name: Smartphone
Price: 28000
Quantity: 7
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
    name: "Smartphone",
    price: 28000,
    quantity: 7,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
]
```

Expected feedback:

```text
Smartphone was updated successfully.
```

Expected summary:

```text
Total Products: 3
Total Units: 9
Inventory Value: KSh 296,000
Out of Stock: 1
```

---

# 116. Three Complete Examples

## Example 1: Update Without the DOM

### Problem

Update the product with ID `2`.

### Complete Code

```js
const products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
  },
];

const editingProductId = 2;

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
      "Smartphone";

    products[index].price =
      28000;

    break;
  }
}

console.log(products);
```

### Expected Console Output

```text
[
  {
    id: 1,
    name: "Laptop",
    price: 50000
  },
  {
    id: 2,
    name: "Smartphone",
    price: 28000
  }
]
```

### Final State

Only product ID `2` changed.

### Common Mistakes

* Generating a new ID.
* Updating every product.
* Comparing against the array index.
* Forgetting `break`.

### Small Modification Challenge

Also update quantity.

---

## Example 2: Duplicate Validation During Edit

### Problem

Allow Phone to keep its own name but reject Laptop.

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
  name,
  ignoredId
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
      ignoredId
    ) {
      continue;
    }

    if (
      product.name
        .trim()
        .toLowerCase() ===
      normalizedName
    ) {
      return true;
    }
  }

  return false;
}

console.log(
  productNameExists(
    "Phone",
    2
  )
);

console.log(
  productNameExists(
    "Laptop",
    2
  )
);
```

### Expected Console Output

```text
false
true
```

### Explanation

Phone is ignored because it is the currently edited product.

Laptop is a different product, so it is a real duplicate.

### Small Modification Challenge

Test with:

```text
 laptop
```

---

## Example 3: Edit and Update in the DOM

### Problem

Load and update products using one form.

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

  <title>Edit Products</title>
</head>

<body>
  <h1>Products</h1>

  <h2 id="form-title">
    Add Product
  </h2>

  <form id="product-form">
    <input
      id="product-name"
      placeholder="Name"
    />

    <input
      id="product-price"
      type="number"
      placeholder="Price"
    />

    <button
      id="submit-button"
      type="submit"
    >
      Add Product
    </button>

    <button
      id="cancel-button"
      type="button"
      hidden
    >
      Cancel Edit
    </button>
  </form>

  <p id="message"></p>

  <ul id="product-list"></ul>

  <script>
    let products = [
      {
        id: 1,
        name: "Laptop",
        price: 50000,
      },
      {
        id: 2,
        name: "Phone",
        price: 25000,
      },
    ];

    let editingProductId =
      null;

    const form =
      document.getElementById(
        "product-form"
      );

    const formTitle =
      document.getElementById(
        "form-title"
      );

    const nameInput =
      document.getElementById(
        "product-name"
      );

    const priceInput =
      document.getElementById(
        "product-price"
      );

    const submitButton =
      document.getElementById(
        "submit-button"
      );

    const cancelButton =
      document.getElementById(
        "cancel-button"
      );

    const message =
      document.getElementById(
        "message"
      );

    const list =
      document.getElementById(
        "product-list"
      );

    function findProductById(id) {
      for (
        let index = 0;
        index < products.length;
        index++
      ) {
        if (
          products[index].id ===
          id
        ) {
          return products[index];
        }
      }

      return null;
    }

    function render() {
      list.innerHTML = "";

      for (
        let index = 0;
        index < products.length;
        index++
      ) {
        const product =
          products[index];

        const item =
          document.createElement(
            "li"
          );

        const text =
          document.createElement(
            "span"
          );

        const editButton =
          document.createElement(
            "button"
          );

        text.textContent =
          `${product.name} — ` +
          `KSh ${product.price.toLocaleString()}`;

        editButton.type =
          "button";

        editButton.textContent =
          "Edit";

        editButton.dataset.id =
          product.id;

        editButton.dataset.action =
          "edit";

        item.append(
          text,
          editButton
        );

        list.appendChild(item);
      }
    }

    function resetForm() {
      editingProductId = null;

      form.reset();

      formTitle.textContent =
        "Add Product";

      submitButton.textContent =
        "Add Product";

      cancelButton.hidden =
        true;
    }

    list.addEventListener(
      "click",
      function (event) {
        const button =
          event.target.closest(
            '[data-action="edit"]'
          );

        if (button === null) {
          return;
        }

        const id =
          Number(
            button.dataset.id
          );

        const product =
          findProductById(id);

        if (product === null) {
          message.textContent =
            "Product not found.";

          return;
        }

        editingProductId =
          product.id;

        nameInput.value =
          product.name;

        priceInput.value =
          product.price;

        formTitle.textContent =
          "Edit Product";

        submitButton.textContent =
          "Update Product";

        cancelButton.hidden =
          false;
      }
    );

    form.addEventListener(
      "submit",
      function (event) {
        event.preventDefault();

        if (
          editingProductId ===
          null
        ) {
          message.textContent =
            "Create is not included in this focused example.";

          return;
        }

        const name =
          nameInput.value.trim();

        const price =
          Number(
            priceInput.value
          );

        if (
          name === "" ||
          !Number.isFinite(price) ||
          price < 0
        ) {
          message.textContent =
            "Invalid values.";

          return;
        }

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
              name;

            products[index].price =
              price;

            break;
          }
        }

        resetForm();
        render();

        message.textContent =
          `${name} was updated.`;
      }
    );

    cancelButton.addEventListener(
      "click",
      function () {
        resetForm();
        render();

        message.textContent =
          "Editing canceled.";
      }
    );

    render();
  </script>
</body>
</html>
```

### Expected DOM After Editing Phone

```text
Edit Product

Name: Phone
Price: 25000

[Update Product] [Cancel Edit]
```

### Expected DOM After Updating

```text
Laptop — KSh 50,000
Smartphone — KSh 28,000
```

### Final State

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
  },
  {
    id: 2,
    name: "Smartphone",
    price: 28000,
  },
]
```

### Small Modification Challenge

Add quantity to the focused example.

---

# 117. Common Beginner Mistakes

## Mistake 1: Creating a New Object During Update

Incorrect:

```js
products.push({
  id: generateProductId(),
  name: data.name,
});
```

This creates a duplicate instead of updating.

---

## Mistake 2: Changing the ID

Incorrect:

```js
product.id =
  generateProductId();
```

The product must preserve identity.

---

## Mistake 3: Updating Before Validation

Invalid values enter state.

---

## Mistake 4: Duplicate Check Rejects the Product Itself

Pass:

```js
editingProductId
```

as the ignored ID.

---

## Mistake 5: Storing the Array Index as Edit State

Indexes change after sorting and deletion.

Store the stable ID.

---

## Mistake 6: Updating Form Values Only

```js
productNameInput.value =
  "Smartphone";
```

This changes the DOM form but not state.

---

## Mistake 7: Updating State Without Rendering

The list and summaries remain stale.

---

## Mistake 8: Canceling Mutates the Product

Cancel should only reset UI state.

---

## Mistake 9: Declaring `editingProductId` Inside the Edit Handler

The submit handler cannot access it later.

---

## Mistake 10: Adding Event Listeners During Every Render

This may duplicate behavior.

Use one delegated list listener.

---

# 118. Beginner Questions

1. What value represents Create mode?
2. What does `editingProductId` store?
3. Why should Edit use a stable ID?
4. How is a product found?
5. What should be returned when no product exists?
6. How are product values loaded into the form?
7. Does loading the form change state?
8. What changes when edit mode begins?
9. Which product properties may be updated?
10. Which product property should remain unchanged?
11. Why is validation reused?
12. Why should duplicate checking ignore the current product?
13. What happens after a successful update?
14. What should Cancel Edit do?
15. Why must the list re-render?

---

# 119. Intermediate Questions

1. Why should the product be found again during submission?
2. What is stale edit state?
3. Why is a returned updated object useful?
4. Why should success data be stored before form reset?
5. Why does edit-mode rendering disable the selected Edit button?
6. Why should rendering preserve `editingProductId`?
7. Why should Cancel not change application state?
8. How can one form support Create and Update?
9. Why is a shared validator preferable to duplicate validators?
10. Why should the currently edited item be ignored only in duplicate validation?
11. What happens when the user selects another product while editing?
12. Why should DOM IDs be converted to numbers?
13. Why should a missing item return the form to Create mode?
14. Why are totals recalculated after update?
15. How does event delegation support new cards after rendering?

---

# 120. Advanced Questions

1. How could unsaved changes be detected?
2. How could switching products require confirmation?
3. What are the trade-offs of editing a draft copy?
4. Why might immutable updates be easier to debug?
5. How could nested product data complicate direct mutation?
6. How would asynchronous API updates change edit mode?
7. What is optimistic updating?
8. How should the UI recover after a server update fails?
9. How could product version numbers prevent conflicting updates?
10. How can Update logic be unit-tested separately from the DOM?
11. How could a state machine model Create and Edit modes?
12. Why can normalized state improve lookup?

---

# 121. Output Prediction Questions

## Question 1

```js
let editingProductId = null;

editingProductId = 2;

console.log(
  editingProductId
);
```

Predict the output.

---

## Question 2

```js
const product = {
  id: 2,
  name: "Phone",
};

product.name =
  "Smartphone";

console.log(
  product.id
);

console.log(
  product.name
);
```

Predict both outputs.

---

## Question 3

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

let result = null;

for (
  let index = 0;
  index < products.length;
  index++
) {
  if (
    products[index].id === 2
  ) {
    result =
      products[index];

    break;
  }
}

console.log(
  result.name
);
```

Predict the output.

---

## Question 4

```js
const duplicate =
  productNameExists(
    "Phone",
    2
  );

console.log(
  duplicate
);
```

Assume the only Phone has ID `2`.

Predict the output.

---

## Question 5

```js
let editingProductId = 2;

editingProductId = null;

console.log(
  editingProductId
);
```

Predict the output.

---

## Question 6

```js
const product = {
  name: "Phone",
};

const input = {
  value: product.name,
};

input.value =
  "Smartphone";

console.log(
  product.name
);
```

Predict the output.

---

## Question 7

```js
const products = [
  {
    id: 1,
    quantity: 2,
  },
  {
    id: 2,
    quantity: 5,
  },
];

for (
  let index = 0;
  index < products.length;
  index++
) {
  if (
    products[index].id === 2
  ) {
    products[index].quantity =
      7;

    break;
  }
}

console.log(
  products[1].quantity
);
```

Predict the output.

---

## Question 8

```js
const id =
  Number("2");

console.log(
  typeof id
);

console.log(
  Number.isInteger(id)
);
```

Predict both outputs.

---

# 122. Debugging Exercises

For every exercise:

1. Identify the bug.
2. Explain why it occurs.
3. Correct the code.
4. State the expected result.

## Debugging Exercise 1: New ID During Update

```js
products[index].id =
  generateProductId();
```

---

## Debugging Exercise 2: Update Pushes a New Product

```js
products.push({
  id: generateProductId(),
  name: data.name,
  price: data.price,
});
```

---

## Debugging Exercise 3: Duplicate Check Rejects Itself

```js
productNameExists(
  data.name
);
```

---

## Debugging Exercise 4: Edit ID Is Local

```js
function startEditing(id) {
  let editingProductId =
    id;
}
```

---

## Debugging Exercise 5: Form Load Mutates State

```js
product.name =
  productNameInput.value;
```

This runs when Edit is first clicked.

---

## Debugging Exercise 6: No Missing-Item Check

```js
const product =
  findProductById(id);

productNameInput.value =
  product.name;
```

---

## Debugging Exercise 7: State Changes but DOM Does Not

```js
updateProduct(data);

showSuccess(
  "Updated."
);
```

---

## Debugging Exercise 8: Cancel Deletes Products

```js
function cancelEdit() {
  products = [];
}
```

---

# 123. Coding Exercises

## Exercise 1: Find Product by ID

Return the object or `null`.

## Exercise 2: Load Form Values

Load name, price, and quantity.

## Exercise 3: Enter Edit Mode

Change title, button text, and Cancel visibility.

## Exercise 4: Highlight the Edited Card

Use the stable ID.

## Exercise 5: Update One Product

Use a standard loop.

## Exercise 6: Preserve the ID

Prove it remains unchanged.

## Exercise 7: Ignore Current Duplicate

Allow the current name during Update.

## Exercise 8: Reject Another Product's Name

Try renaming Phone to Laptop.

## Exercise 9: Cancel Editing

Reset form state without changing products.

## Exercise 10: Switch Between Products

Load several products in sequence.

## Exercise 11: Handle Missing Edit IDs

Display an error and recover safely.

## Exercise 12: Re-render Summaries

Confirm totals change after updating price or quantity.

---

# 124. Refactoring Exercises

## Exercise 1

Move product lookup into:

```js
findProductById(id);
```

## Exercise 2

Move form loading into:

```js
loadProductIntoForm(product);
```

## Exercise 3

Move mode changes into:

```js
setEditModeUI(product);
setCreateModeUI();
```

## Exercise 4

Move state mutation into:

```js
updateProduct(data);
```

## Exercise 5

Move edit startup into:

```js
startEditingProduct(id);
```

## Exercise 6

Move cancel logic into:

```js
cancelProductEdit();
```

## Exercise 7

Use one form handler for Create and Update.

## Exercise 8

Use one delegated list listener for future Edit and Delete actions.

---

# 125. Conceptual Questions

1. Why is editing an Update operation?
2. Why should the form load existing values?
3. Why should form changes remain temporary before submission?
4. Why should the product ID remain unchanged?
5. Why should Update find the product by ID?
6. Why should duplicate validation ignore the current product?
7. Why should validation occur before mutation?
8. Why should one form handle Create and Update?
9. Why should edit mode be represented in JavaScript?
10. Why should cancellation not mutate products?
11. Why should a missing product be handled safely?
12. Why should the DOM be rendered after Update?
13. Why should summaries be derived from current state?
14. Why should event listeners not be recreated unnecessarily?
15. How does this part prepare for Delete?

---

# 126. Part 3 Completion Task

Continue working in:

```text
product-inventory-plain-loops/
```

Required new or updated functions:

```js
findProductById(productId);
loadProductIntoForm(product);
setEditModeUI(product);
startEditingProduct(productId);
updateProduct(data);
cancelProductEdit();
handleProductFormSubmit(event);
handleProductListClick(event);
```

Requirements:

* [ ] Add one delegated product-list click listener.
* [ ] Detect Edit button clicks.
* [ ] Read the stable product ID.
* [ ] Convert the ID to a number.
* [ ] Validate the ID.
* [ ] Find the selected product.
* [ ] Handle missing products.
* [ ] Load name.
* [ ] Load price.
* [ ] Load quantity.
* [ ] Set `editingProductId`.
* [ ] Change form title to `Edit Product`.
* [ ] Change submit text to `Update Product`.
* [ ] Show Cancel Edit.
* [ ] Highlight the selected card.
* [ ] Disable its Edit button.
* [ ] Reuse Create validation.
* [ ] Ignore the current ID in duplicate checks.
* [ ] Update using a standard `for` loop.
* [ ] Update only name, price, and quantity.
* [ ] Preserve the product ID.
* [ ] Return the updated object.
* [ ] Handle stale edit state.
* [ ] Reset to Create mode after success.
* [ ] Re-render the list.
* [ ] Re-render all summary values.
* [ ] Show a success message.
* [ ] Implement Cancel Edit.
* [ ] Reset the form button while editing.
* [ ] Allow switching between products.
* [ ] Do not implement Delete yet.
* [ ] Do not implement Search yet.

Manual testing checklist:

* [ ] Edit Laptop without changing anything.
* [ ] Rename Phone to Smartphone.
* [ ] Change price.
* [ ] Change quantity.
* [ ] Change quantity from zero to a positive value.
* [ ] Change quantity from positive to zero.
* [ ] Keep the same name.
* [ ] Reject another product's name.
* [ ] Reject blank name.
* [ ] Reject negative price.
* [ ] Reject decimal quantity.
* [ ] Cancel editing.
* [ ] Reset the form while editing.
* [ ] Switch directly between two products.
* [ ] Simulate a missing product ID.
* [ ] Confirm IDs remain unchanged.
* [ ] Confirm summaries update.
* [ ] Confirm cards do not duplicate.

Expected state after updating Phone:

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
    name: "Smartphone",
    price: 28000,
    quantity: 7,
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
]
```

Expected summary:

```text
Total Products: 3
Total Units: 9
Inventory Value: KSh 296,000
Out of Stock: 1
```

Expected feedback:

```text
Smartphone was updated successfully.
```

---

## Key Takeaways

* Update begins by locating a product with its stable ID.
* Loading form values changes the DOM but not application state.
* `editingProductId` distinguishes Create from Edit mode.
* The same validation rules can support both Create and Update.
* Duplicate validation must ignore the currently edited product.
* A plain `for` loop can update only the matching object.
* The stable ID must never change during Update.
* Canceling edit should reset only temporary UI state.
* A missing product must be handled safely.
* Re-rendering updates cards, stock status, counts, units, and inventory value.
* The next part will implement confirmed product deletion using a plain loop and `splice()`.

---

## Continue Learning

| Direction         | Link                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                              |
| Previous Subtopic | [Product Inventory Part 2: Creating Products and Validating Form Data](./02-product-inventory-part-2-create-and-validation.md) |
| Next Subtopic     | [Product Inventory Part 4: Deleting Products](./02-product-inventory-part-4-deleting-products.md)                              |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                              |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 3

When you are ready to continue, write:

```text
Next
```

