---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 6: CRUD Applications with Plain Loops"
subtopic: "Subtopic 2: Building a Complete Product Inventory with Plain Loops — Part 4: Deleting Products"
difficulty: "Beginner to Intermediate"
estimated_time: "120–150 minutes"
prerequisites:

* "Product Inventory architecture and rendering"
* "Creating products"
* "Updating products"
* "Finding an item index with a plain loop"
* "Removing items with splice()"
  previous_topic: "CRUD Applications with Plain Loops"
  previous_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 3: Updating Products"
  next_topic: "CRUD Applications with Plain Loops"
  next_subtopic: "Building a Complete Product Inventory with Plain Loops — Part 5: Searching, Totals, and Complete Source Code"

---

# Building a Complete Product Inventory with Plain Loops

## Part 4: Deleting Products

## Course Navigation

| Direction         | Link                                                                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                                         |
| Previous Subtopic | [Product Inventory Part 3: Updating Products](./02-product-inventory-part-3-updating-products.md)                                         |
| Next Subtopic     | [Product Inventory Part 5: Searching, Totals, and Complete Source Code](./02-product-inventory-part-5-search-totals-and-complete-code.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                                         |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 4

---

## Learning Objectives

By the end of this part, you should be able to:

* Find a product's current array index using a standard `for` loop.
* Explain the difference between a stable product ID and an array index.
* Ask for confirmation before deleting a product.
* Remove exactly one product with `splice()`.
* Capture the deleted product returned by `splice()`.
* Handle invalid and missing IDs safely.
* Prevent accidental `splice(-1, 1)` deletion.
* Reset edit mode when the deleted product is being edited.
* Preserve edit mode when another product is deleted.
* Re-render the inventory after deletion.
* Refresh totals and stock summaries.
* Show an empty state after deleting the final product.
* Use event delegation for Edit and Delete buttons.
* Separate event handling, confirmation, state mutation, and rendering.

---

## What You Will Build

You will complete the Delete feature in the Product Inventory application.

When the user clicks:

```text
[Delete]
```

the browser should ask:

```text
Are you sure you want to delete Phone?
```

If the user clicks **Cancel**:

```text
Phone was not deleted.
```

State remains unchanged.

If the user clicks **OK**:

```text
Phone was deleted successfully.
```

The product disappears from:

* The `products` array.
* The rendered product list.
* Summary calculations.
* Search results.

---

# 127. Understanding the Product Delete Flow

## 127.1 Complete Delete Sequence

```text
User clicks Delete
        ↓
Read action and product ID
        ↓
Convert ID from string to number
        ↓
Validate ID
        ↓
Find product object
        ↓
Handle missing product
        ↓
Ask for confirmation
        ↓
Stop if user cancels
        ↓
Find the current array index
        ↓
Check for -1
        ↓
Remove one item with splice()
        ↓
Capture deleted product
        ↓
Reset matching edit mode
        ↓
Render current products
        ↓
Update all summaries
        ↓
Show success feedback
```

---

# 128. ID Versus Array Index

Consider:

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

| Product  | Stable ID | Current Index |
| -------- | --------: | ------------: |
| Laptop   |       `1` |           `0` |
| Phone    |       `2` |           `1` |
| Keyboard |       `3` |           `2` |

The ID identifies the product.

The index identifies its current position in the array.

---

# 129. Why Delete Needs the Current Index

`splice()` removes by position.

```js
products.splice(
  productIndex,
  1
);
```

It does not search for an object whose ID matches a value.

Therefore, the application must:

1. Read the stable ID.
2. Find the current array index.
3. Pass that index to `splice()`.

---

# 130. Finding the Product Index with a Plain Loop

```js
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
```

This function returns:

```text
0, 1, 2, ...
```

when found.

It returns:

```text
-1
```

when not found.

---

# 131. Why Return `-1`?

Valid indexes begin at:

```text
0
```

Therefore, `-1` clearly means:

```text
No matching product exists.
```

Always check:

```js
if (productIndex === -1) {
  // Handle failure.
}
```

Do not use:

```js
if (!productIndex)
```

because index `0` is valid but falsy.

---

# 132. The Danger of `splice(-1, 1)`

Suppose:

```js
const productIndex = -1;
```

Then:

```js
products.splice(
  productIndex,
  1
);
```

removes the final product in the array.

> **Warning:** A missing-item result must always be checked before calling `splice()`.

---

# 133. Confirming Product Deletion

```js
function confirmProductDeletion(
  product
) {
  return window.confirm(
    `Are you sure you want to delete ${product.name}?`
  );
}
```

This returns:

```js
true
```

when the user clicks OK.

It returns:

```js
false
```

when the user clicks Cancel.

---

# 134. Why Find the Product Before Confirmation?

The confirmation message should identify the exact target.

Less clear:

```text
Are you sure you want to delete this product?
```

Better:

```text
Are you sure you want to delete Phone?
```

Therefore, first find:

```js
const product =
  findProductById(productId);
```

Then use:

```js
product.name
```

in the message.

---

# 135. Cancellation Must Stop the Flow

```js
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
```

Without `return`, deletion continues after cancellation.

---

# 136. Low-Level Delete Function

```js
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
```

---

# 137. Why Return a Result Object?

A result object can clearly describe:

* Whether deletion succeeded.
* Which product was removed.
* What message should be displayed.

Success:

```js
{
  success: true,
  deletedProduct: {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 5
  },
  message: "Product deleted successfully."
}
```

Failure:

```js
{
  success: false,
  deletedProduct: null,
  message:
    "The selected product no longer exists."
}
```

---

# 138. What `splice()` Returns

```js
const removedProducts =
  products.splice(
    productIndex,
    1
  );
```

Even though one product is removed, the return value is an array.

Example:

```js
[
  {
    id: 2,
    name: "Phone",
  },
]
```

Access the deleted object with:

```js
removedProducts[0];
```

---

# 139. Deleting the Product Being Edited

Suppose:

```js
editingProductId = 2;
```

and product ID `2` is deleted.

The form is now editing an object that no longer exists.

Reset:

```js
if (
  editingProductId ===
  deletedProduct.id
) {
  resetFormToCreateMode();
}
```

---

# 140. Deleting Another Product During Edit Mode

Suppose:

```js
editingProductId = 1;
```

The user deletes product ID `2`.

This condition is false:

```js
editingProductId ===
deletedProduct.id
```

The Laptop edit should remain active.

Do not reset edit mode for unrelated deletion.

---

# 141. Complete Delete Request Function

```js
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
```

---

# 142. Why Re-render After Delete?

Deleting changes:

```js
products
```

The DOM still contains the old product card until:

```js
renderProducts();
```

The render refresh also updates:

* Total products.
* Total units.
* Inventory value.
* Out-of-stock count.
* Visible product count.
* Product numbering.
* Search output.
* Empty state.

---

# 143. Updated Product List Click Handler

Replace the Part 3 handler with:

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

    return;
  }

  if (action === "delete") {
    handleDeleteRequest(
      productId
    );
  }
}
```

One listener now supports:

```text
Edit
Delete
```

---

# 144. Deleting and Search State

Suppose a search is active:

```js
currentSearchTerm = "phone";
```

Phone is deleted.

After:

```js
renderProducts();
```

the search view should show:

```text
No products match your search.
```

Search will be completed in the next part.

The important principle is:

> Delete changes the main state first. Rendering then recalculates the visible list.

---

# 145. Deleting the Final Product

Suppose state contains:

```js
[
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 0,
  },
]
```

After deletion:

```js
products
```

becomes:

```js
[]
```

Expected summary:

```text
Total Products: 0
Total Units: 0
Inventory Value: KSh 0
Out of Stock: 0
```

Expected list output:

```text
No products available.
```

---

# 146. Remaining IDs Must Stay Stable

Before:

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

After deleting Phone:

```js
[
  {
    id: 1,
    name: "Laptop",
  },
  {
    id: 3,
    name: "Keyboard",
  },
]
```

Do not change Keyboard's ID from:

```text
3
```

to:

```text
2
```

Display numbering can change, but stable identity should not.

---

# 147. Complete Part 4 JavaScript Additions

Add these functions:

```js
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
```

Update:

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

    return;
  }

  if (action === "delete") {
    handleDeleteRequest(
      productId
    );
  }
}
```

The existing listener remains:

```js
productList.addEventListener(
  "click",
  handleProductListClick
);
```

Do not register another product-list listener.

---

# 148. Expected Delete Result

Before:

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

Delete Phone and confirm.

After:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
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
Phone was deleted successfully.
```

Expected summary:

```text
Total Products: 2
Total Units: 2
Inventory Value: KSh 100,000
Out of Stock: 1
```

---

# 149. Exactly Three Complete Examples

## Example 1: Delete an Object Without the DOM

### Problem

Delete the product with ID `2`.

### Input Data

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

### Complete Code

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

const selectedId = 2;

let selectedIndex = -1;

for (
  let index = 0;
  index < products.length;
  index++
) {
  if (
    products[index].id ===
    selectedId
  ) {
    selectedIndex = index;
    break;
  }
}

if (selectedIndex !== -1) {
  const removedProducts =
    products.splice(
      selectedIndex,
      1
    );

  console.log(
    removedProducts[0]
  );
}

console.log(products);
```

### Step-by-Step Explanation

1. Store the stable ID.
2. Initialize the index as `-1`.
3. Search with a standard loop.
4. Stop when IDs match.
5. Confirm the index is valid.
6. Remove one object.
7. Capture the removed object.
8. Print the final state.

### Expected Console Output

```text
{
  id: 2,
  name: "Phone"
}

[
  {
    id: 1,
    name: "Laptop"
  },
  {
    id: 3,
    name: "Keyboard"
  }
]
```

### Final State

Phone is removed.

Remaining IDs are unchanged.

### Common Mistakes

* Using the ID as the `splice()` index.
* Forgetting to check for `-1`.
* Omitting the delete count.
* Renumbering remaining IDs.

### Small Modification Challenge

Attempt to delete ID `99` and confirm that state remains unchanged.

---

## Example 2: Confirmed Deletion with a Result Object

### Problem

Return a descriptive result for successful and failed deletion.

### Complete Code

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
];

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

function deleteProductById(
  productId
) {
  const index =
    findProductIndexById(
      productId
    );

  if (index === -1) {
    return {
      success: false,
      deletedProduct: null,
    };
  }

  const removed =
    products.splice(
      index,
      1
    );

  return {
    success: true,
    deletedProduct:
      removed[0],
  };
}

console.log(
  deleteProductById(2)
);

console.log(
  deleteProductById(99)
);

console.log(products);
```

### Expected Console Output

```text
{
  success: true,
  deletedProduct: {
    id: 2,
    name: "Phone"
  }
}

{
  success: false,
  deletedProduct: null
}

[
  {
    id: 1,
    name: "Laptop"
  }
]
```

### Final State

Only Laptop remains.

### Common Mistakes

* Returning `removed` instead of `removed[0]`.
* Returning success when no item exists.
* Mutating state before validation.

### Small Modification Challenge

Add a `message` property to both result objects.

---

## Example 3: Confirmed DOM Deletion

### Problem

Render products, confirm deletion, update state, and re-render.

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

  <title>Delete Inventory Products</title>

  <style>
    body {
      max-width: 720px;
      margin: 0 auto;
      padding: 24px;
      font-family: Arial, sans-serif;
    }

    #product-list {
      padding: 0;
      list-style: none;
    }

    .product-card {
      margin-bottom: 12px;
      padding: 14px;
      border: 1px solid #cccccc;
    }
  </style>
</head>

<body>
  <h1>Products</h1>

  <p id="message"></p>
  <p id="count"></p>

  <ul id="product-list"></ul>

  <script>
    let products = [
      {
        id: 1,
        name: "Laptop",
      },
      {
        id: 2,
        name: "Phone",
      },
    ];

    const message =
      document.getElementById(
        "message"
      );

    const count =
      document.getElementById(
        "count"
      );

    const productList =
      document.getElementById(
        "product-list"
      );

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

    function renderProducts() {
      productList.innerHTML = "";

      count.textContent =
        `Total products: ${products.length}`;

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

        const card =
          document.createElement(
            "li"
          );

        const name =
          document.createElement(
            "span"
          );

        const deleteButton =
          document.createElement(
            "button"
          );

        card.classList.add(
          "product-card"
        );

        name.textContent =
          product.name;

        deleteButton.type =
          "button";

        deleteButton.textContent =
          "Delete";

        deleteButton.dataset.action =
          "delete";

        deleteButton.dataset.id =
          product.id;

        card.append(
          name,
          deleteButton
        );

        productList.appendChild(
          card
        );
      }
    }

    productList.addEventListener(
      "click",
      function (event) {
        const deleteButton =
          event.target.closest(
            '[data-action="delete"]'
          );

        if (deleteButton === null) {
          return;
        }

        const productId =
          Number(
            deleteButton.dataset.id
          );

        const product =
          findProductById(
            productId
          );

        if (product === null) {
          message.textContent =
            "Product not found.";

          renderProducts();
          return;
        }

        const confirmed =
          window.confirm(
            `Delete ${product.name}?`
          );

        if (!confirmed) {
          message.textContent =
            `${product.name} was not deleted.`;

          return;
        }

        const productIndex =
          findProductIndexById(
            productId
          );

        if (
          productIndex === -1
        ) {
          message.textContent =
            "Product no longer exists.";

          renderProducts();
          return;
        }

        products.splice(
          productIndex,
          1
        );

        renderProducts();

        message.textContent =
          `${product.name} was deleted.`;
      }
    );

    renderProducts();
  </script>
</body>
</html>
```

### Expected DOM Before Deletion

```text
Total products: 2

Laptop [Delete]
Phone [Delete]
```

### Expected DOM After Confirming Phone Deletion

```text
Phone was deleted.
Total products: 1

Laptop [Delete]
```

### Final State

```js
[
  {
    id: 1,
    name: "Laptop",
  },
]
```

### Common Mistakes

* Removing the card without changing state.
* Showing success after cancellation.
* Forgetting the initial render.
* Using direct listeners that are lost after re-rendering.

### Small Modification Challenge

Delete Laptop and confirm that the empty-state message appears.

---

# 150. Common Beginner Mistakes

## Mistake 1: Using the Product ID as an Index

Incorrect:

```js
products.splice(
  productId,
  1
);
```

Correct:

```js
const productIndex =
  findProductIndexById(
    productId
  );
```

---

## Mistake 2: Forgetting to Check `-1`

This can delete the final product accidentally.

---

## Mistake 3: Omitting the Delete Count

```js
products.splice(
  productIndex
);
```

This removes every product from that index onward.

Use:

```js
products.splice(
  productIndex,
  1
);
```

---

## Mistake 4: Ignoring Confirmation

```js
window.confirm(
  "Delete?"
);

deleteProductById(id);
```

The result must be checked.

---

## Mistake 5: Forgetting `return` After Cancel

Deletion still continues.

---

## Mistake 6: Deleting Only the DOM Card

The product remains in state and returns after the next render.

---

## Mistake 7: Resetting Edit Mode for Every Delete

Preserve unrelated edits.

---

## Mistake 8: Renumbering Stable IDs

Only display numbering should change.

---

## Mistake 9: Forgetting to Re-render Summaries

Counts and totals remain stale.

---

## Mistake 10: Adding Another List Listener

One delegated listener should handle both Edit and Delete.

---

# 151. Beginner Questions

1. Why does deletion require an array index?
2. What is the difference between ID and index?
3. What does `findProductIndexById()` return when missing?
4. Why is index `0` valid?
5. What does `splice(index, 1)` mean?
6. What does `splice()` return?
7. Why should deletion ask for confirmation?
8. What does `window.confirm()` return?
9. What should happen when the user cancels?
10. Why should the product be found before confirmation?
11. Why should `-1` be checked?
12. Why should the list re-render after deletion?
13. What should happen when the final item is deleted?
14. When should edit mode reset?
15. Why should remaining IDs stay unchanged?

---

# 152. Intermediate Questions

1. Why should the index be found close to the mutation?
2. Why is a stable ID safer than a stored index?
3. Why should low-level deletion return a result object?
4. Why should confirmation logic be separate from state mutation?
5. Why should cancellation not be treated as an error?
6. Why should missing-item handling sometimes re-render the list?
7. Why should another active edit be preserved?
8. How does event delegation survive deletion and re-rendering?
9. Why should deleted-product data be captured?
10. Why should the success message use the deleted product object?
11. Why should summaries be derived again after deletion?
12. Why can repeated Delete clicks reference stale state?
13. Why should the state array be changed before the DOM?
14. Why should visible numbering differ from stable IDs?
15. How will Search interact with deletion?

---

# 153. Advanced Questions

1. What is the time complexity of finding and splicing an item?
2. Why can deleting from the start of an array be more expensive?
3. How could a `Map` improve lookup speed?
4. What is immutable deletion?
5. How will `filter()` differ from `splice()`?
6. What is optimistic deletion with an API?
7. How can failed deletion be rolled back?
8. What is soft deletion?
9. How could undo deletion be implemented?
10. How can concurrent users create stale deletion requests?
11. Why should server-side deletion verify permissions?
12. How might a custom confirmation modal improve user experience?

---

# 154. Output Prediction Questions

## Question 1

```js
const products = [
  "Laptop",
  "Phone",
];

const removed =
  products.splice(1, 1);

console.log(products);
console.log(removed);
```

Predict both outputs.

---

## Question 2

```js
const products = [
  "Laptop",
  "Phone",
  "Keyboard",
];

products.splice(-1, 1);

console.log(products);
```

Predict the output.

---

## Question 3

```js
const index = 0;

if (!index) {
  console.log(
    "Not found"
  );
}
```

Predict the output and explain the bug.

---

## Question 4

```js
const confirmed = false;

if (!confirmed) {
  console.log(
    "Canceled"
  );
} else {
  console.log(
    "Deleted"
  );
}
```

Predict the output.

---

## Question 5

```js
let editingProductId = 2;

const deletedProduct = {
  id: 2,
};

if (
  editingProductId ===
  deletedProduct.id
) {
  editingProductId = null;
}

console.log(
  editingProductId
);
```

Predict the output.

---

## Question 6

```js
const removed =
  products.splice(0, 1);

console.log(
  Array.isArray(removed)
);
```

Predict the output.

---

## Question 7

```js
const products = [
  {
    id: 1,
  },
  {
    id: 3,
  },
];

console.log(
  products[1].id
);
```

Does deletion require remaining IDs to be renumbered?

---

## Question 8

```js
productCard.remove();

console.log(
  products.length
);
```

Does DOM removal change state length?

---

# 155. Debugging Exercises

For each exercise:

1. Identify the bug.
2. Explain why it occurs.
3. Correct the code.
4. State the expected result.

## Debugging Exercise 1: ID Used as Index

```js
products.splice(
  productId,
  1
);
```

---

## Debugging Exercise 2: Missing-Index Deletion

```js
const index =
  findProductIndexById(id);

products.splice(
  index,
  1
);
```

---

## Debugging Exercise 3: Confirmation Ignored

```js
window.confirm(
  "Delete product?"
);

deleteProductById(id);
```

---

## Debugging Exercise 4: Missing Return After Cancel

```js
if (!confirmed) {
  showInfo(
    "Canceled."
  );
}

deleteProductById(id);
```

---

## Debugging Exercise 5: Too Many Products Removed

```js
products.splice(
  productIndex
);
```

---

## Debugging Exercise 6: DOM-Only Delete

```js
deleteButton
  .closest("li")
  .remove();
```

---

## Debugging Exercise 7: Every Delete Cancels Editing

```js
resetFormToCreateMode();

deleteProductById(id);
```

---

## Debugging Exercise 8: No Render After State Change

```js
deleteProductById(id);

showSuccess(
  "Deleted."
);
```

---

# 156. Coding Exercises

## Exercise 1: Find a Product Index

Return the index or `-1`.

## Exercise 2: Delete the First Product

Confirm index `0` is handled correctly.

## Exercise 3: Delete the Middle Product

Use `splice(index, 1)`.

## Exercise 4: Capture the Deleted Product

Display its name.

## Exercise 5: Handle a Missing ID

Return a failure result without changing state.

## Exercise 6: Confirm Deletion

Include the product name in the prompt.

## Exercise 7: Handle Cancellation

Show neutral feedback.

## Exercise 8: Reset Matching Edit Mode

Reset only when IDs match.

## Exercise 9: Preserve Unrelated Edit Mode

Delete a different product.

## Exercise 10: Refresh Summary Values

Confirm totals update after deletion.

## Exercise 11: Render the Empty State

Delete every product.

## Exercise 12: Handle Repeated Deletion

Try deleting the same ID twice safely.

---

# 157. Refactoring Exercises

## Exercise 1

Move index lookup into:

```js
findProductIndexById(id);
```

## Exercise 2

Move confirmation into:

```js
confirmProductDeletion(product);
```

## Exercise 3

Move state mutation into:

```js
deleteProductById(id);
```

## Exercise 4

Move workflow coordination into:

```js
handleDeleteRequest(id);
```

## Exercise 5

Use a structured result rather than `null`.

## Exercise 6

Use one delegated handler for Edit and Delete.

## Exercise 7

Replace DOM-only removal with state mutation and rendering.

## Exercise 8

Replace button indexes with stable IDs.

---

# 158. Conceptual Questions

1. Why is Delete more than removing a DOM element?
2. Why should the array remain the source of truth?
3. Why must the product ID be converted to a current index?
4. Why is `-1` dangerous when passed to `splice()`?
5. Why should confirmation happen before state mutation?
6. Why should cancellation stop the function?
7. Why should the deleted product be returned?
8. Why should edit mode reset conditionally?
9. Why should summaries be recalculated after deletion?
10. Why should stable IDs not be renumbered?
11. Why should event delegation be used?
12. Why should missing products be handled safely?
13. Why is `splice()` considered mutable?
14. Why might `filter()` later be preferred?
15. How does this part complete CRUD before Search is added?

---

# 159. Part 4 Completion Task

Continue working in:

```text
product-inventory-plain-loops/
```

Required new functions:

```js
findProductIndexById(
  productId
);

confirmProductDeletion(
  product
);

deleteProductById(
  productId
);

handleDeleteRequest(
  productId
);
```

Update:

```js
handleProductListClick(
  event
);
```

Requirements:

* [ ] Detect Delete button clicks.
* [ ] Use the existing delegated listener.
* [ ] Read the stable ID from `dataset`.
* [ ] Convert the ID to a number.
* [ ] Validate the ID.
* [ ] Find the product object.
* [ ] Handle a missing product.
* [ ] Include the product name in confirmation.
* [ ] Stop when the user cancels.
* [ ] Show neutral cancellation feedback.
* [ ] Find the current index with a plain loop.
* [ ] Return `-1` when missing.
* [ ] Check for `-1`.
* [ ] Remove exactly one product with `splice()`.
* [ ] Capture the removed product.
* [ ] Return a structured result.
* [ ] Reset edit mode only when deleting the edited product.
* [ ] Preserve an unrelated active edit.
* [ ] Re-render after successful deletion.
* [ ] Refresh total products.
* [ ] Refresh total units.
* [ ] Refresh inventory value.
* [ ] Refresh out-of-stock count.
* [ ] Refresh product numbering.
* [ ] Show the empty state when no products remain.
* [ ] Show a success message.
* [ ] Preserve all remaining stable IDs.
* [ ] Handle repeated deletion attempts.
* [ ] Do not implement Search yet.
* [ ] Do not add another list listener.

Manual testing checklist:

* [ ] Delete Laptop and cancel.
* [ ] Delete Laptop and confirm.
* [ ] Delete Phone and confirm.
* [ ] Delete Keyboard and confirm.
* [ ] Delete the first array item.
* [ ] Delete the final array item.
* [ ] Delete the product currently being edited.
* [ ] Delete another product while editing.
* [ ] Attempt an invalid ID.
* [ ] Attempt a missing ID.
* [ ] Attempt the same deletion twice.
* [ ] Delete all products.
* [ ] Confirm the empty state appears.
* [ ] Confirm totals become zero.
* [ ] Confirm remaining IDs are unchanged.
* [ ] Confirm no duplicate cards appear.

Expected state after deleting Phone:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2,
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
Total Products: 2
Total Units: 2
Inventory Value: KSh 100,000
Out of Stock: 1
```

Expected feedback:

```text
Phone was deleted successfully.
```

---

## Key Takeaways

* Delete buttons should store stable product IDs, not array indexes.
* `splice()` requires the current array index.
* A plain loop can return the matching index or `-1`.
* Always check for `-1` before deletion.
* Confirmation must happen before mutation.
* Canceling should leave state unchanged.
* `splice(index, 1)` removes exactly one product.
* `splice()` returns an array of removed items.
* The deleted product should be captured for feedback.
* Edit mode should reset only when its product is deleted.
* Re-rendering refreshes cards, summaries, numbering, and the empty state.
* The next part will add product searching, connect it to rendering, and provide the complete integrated source code.

---

## Continue Learning

| Direction         | Link                                                                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Previous Topic    | [CRUD Applications with Plain Loops](./README.md)                                                                                         |
| Previous Subtopic | [Product Inventory Part 3: Updating Products](./02-product-inventory-part-3-updating-products.md)                                         |
| Next Subtopic     | [Product Inventory Part 5: Searching, Totals, and Complete Source Code](./02-product-inventory-part-5-search-totals-and-complete-code.md) |
| Next Topic        | [CRUD Applications with Plain Loops](./README.md)                                                                                         |

> **Current Position:** Phase 1 → Module 1 → Topic 6 → Subtopic 2 → Part 4

When you are ready to continue, write:

```text
Next
```

