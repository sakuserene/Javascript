---

title: "CRUD with Plain Loops, Array Methods, DOM, and JavaScript State"
phase: "Phase 1: JavaScript Application Foundations"
module: "Module 1: CRUD, State, and DOM Foundations"
topic: "Topic 1: CRUD Operations with JavaScript State and the DOM"
subtopic: "Subtopic 1: Understanding CRUD"
difficulty: "Beginner"
estimated_time: "60–90 minutes"
prerequisites:

* "Basic JavaScript variables"
* "Basic JavaScript arrays"
* "Basic JavaScript objects"
* "Basic JavaScript functions"
  previous_topic: "Course Introduction"
  previous_subtopic: "JavaScript CRUD Course Overview"
  next_topic: "JavaScript State and DOM Synchronization"
  next_subtopic: "Understanding JavaScript State"

---

# CRUD with Plain Loops, Array Methods, DOM, and JavaScript State

## Course Navigation

| Direction         | Link                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Previous Topic    | [Course Introduction](../00-course-introduction/README.md)                                |
| Previous Subtopic | [JavaScript CRUD Course Overview](../00-course-introduction/01-course-overview.md)        |
| Next Subtopic     | [Understanding JavaScript State](./02-understanding-javascript-state.md)                  |
| Next Topic        | [JavaScript State and DOM Synchronization](../02-state-and-dom-synchronization/README.md) |

> **Current Position:** Phase 1 → Module 1 → Topic 1 → Subtopic 1

---

## Current Position

* **Phase:** Phase 1 — JavaScript Application Foundations
* **Module:** Module 1 — CRUD, State, and DOM Foundations
* **Topic:** Topic 1 — CRUD Operations with JavaScript State and the DOM
* **Subtopic:** Subtopic 1 — Understanding CRUD
* **Previous Topic:** Course Introduction
* **Previous Subtopic:** JavaScript CRUD Course Overview
* **Next Subtopic:** Understanding JavaScript State
* **Next Topic:** JavaScript State and DOM Synchronization

---

## Learning Objectives

By the end of this subtopic, you should be able to:

* Explain what CRUD means.
* Identify Create, Read, Update, and Delete operations.
* Recognize CRUD operations in real applications.
* Explain why CRUD is important in web applications.
* Store simple items inside a JavaScript array.
* Represent an item using a JavaScript object.
* Explain why items usually need unique IDs.
* Distinguish between an item ID and an array index.
* Identify which CRUD operation is needed for a given requirement.
* Describe the basic lifecycle of an application item.

---

## Prerequisites

You should have a basic understanding of:

* Variables declared with `const` and `let`.
* Arrays.
* Objects.
* Functions.
* Console output using `console.log()`.

You do not need advanced knowledge of:

* Array methods.
* DOM rendering.
* Event delegation.
* State-management libraries.
* Frameworks such as React, Vue, or Angular.

Everything will be introduced gradually.

---

## What You Will Build

Throughout this topic, you will build applications that manage arrays of objects.

The three main applications will be:

### 1. Simple Shopping List

```js
{
  id: 1,
  name: "Milk",
  purchased: false
}
```

The application will allow users to:

* Add shopping items.
* Display shopping items.
* Edit item names.
* Mark items as purchased.
* Delete items.

### 2. Product Inventory

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

The application will allow users to:

* Add products.
* Display products.
* Update products.
* Delete products.
* Search products.
* Detect duplicates.
* Calculate inventory totals.
* Show out-of-stock products.

### 3. Task Manager

```js
{
  id: 1,
  title: "Study JavaScript",
  completed: false,
  priority: "high"
}
```

The application will allow users to:

* Add tasks.
* Display tasks.
* Edit tasks.
* Delete tasks.
* Mark tasks as completed.
* Filter tasks.
* Count completed and incomplete tasks.

You will first build CRUD operations using plain loops.

After understanding the plain-loop approach, you will rebuild the same operations using modern JavaScript methods.

---

# 1. Understanding CRUD

## 1.1 What You Will Learn

In this subtopic, you will learn:

* What CRUD means.
* Why CRUD exists.
* How CRUD relates to application data.
* How users trigger CRUD operations.
* How arrays and objects can represent application data.
* Why unique IDs are important.
* How CRUD appears in shopping lists, inventories, task managers, and other systems.

### Why This Matters

Most interactive applications manage information.

Examples include:

* A task application managing tasks.
* A shop managing products.
* A hospital managing patients.
* A school managing students.
* A business managing employees.
* A banking system managing transactions.
* A social platform managing posts and comments.

The four most common actions performed on this information are:

1. Add it.
2. View it.
3. Change it.
4. Remove it.

These four actions are called CRUD.

### Where CRUD Is Used

CRUD is used in:

* Web applications.
* Mobile applications.
* Desktop applications.
* Backend APIs.
* Databases.
* Administrative dashboards.
* E-commerce systems.
* School systems.
* Inventory systems.
* Content-management systems.

### Connection to State and the DOM

Later, the application data will be stored in a JavaScript array.

```js
let items = [];
```

The webpage will display that data through the DOM.

```text
JavaScript state → displayed DOM
```

When a CRUD operation changes the state, the page must be rendered again so the user can see the change.

### Common Beginner Mistakes

Beginners commonly:

* Confuse an item ID with an array index.
* Change HTML without changing the JavaScript array.
* Change the array without updating the webpage.
* Delete the wrong item.
* Add duplicate IDs.
* Forget to validate input.
* confuse Create with Update.
* confuse Read with Search.

---

## 1.2 What Does CRUD Mean?

CRUD is an acronym.

| Letter | Operation | Meaning                         |
| ------ | --------- | ------------------------------- |
| C      | Create    | Add new data                    |
| R      | Read      | Access or display existing data |
| U      | Update    | Change existing data            |
| D      | Delete    | Remove existing data            |

Consider this array:

```js
let items = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  }
];
```

The array contains one item.

Each CRUD operation affects or uses the array differently.

---

## 1.3 Create

Create means adding a new item.

Initial state:

```js
let items = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  }
];
```

New item:

```js
const newItem = {
  id: 2,
  name: "Phone",
  price: 25000,
  quantity: 4
};
```

Create operation:

```js
items.push(newItem);
```

Final state:

```js
[
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  }
]
```

### Real Application Examples

Create operations include:

* Adding a task.
* Registering a user.
* Adding a product.
* Creating a comment.
* Adding a student record.
* Creating an expense entry.
* Adding an item to a shopping cart.

### Important Question

A Create operation answers:

> What new item should be added to the application?

---

## 1.4 Read

Read means accessing or displaying existing data.

Example state:

```js
const items = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  }
];
```

Reading one item:

```js
console.log(items[0]);
```

Expected output:

```text
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

Reading one property:

```js
console.log(items[0].name);
```

Expected output:

```text
Laptop
```

Reading all names:

```js
for (let index = 0; index < items.length; index++) {
  console.log(items[index].name);
}
```

Expected output:

```text
Laptop
Phone
```

### Read Does Not Necessarily Change State

This code reads the item:

```js
console.log(items[0].name);
```

The original array remains unchanged.

### Real Application Examples

Read operations include:

* Displaying all products.
* Viewing one task.
* Searching for an employee.
* Filtering completed tasks.
* Displaying a user profile.
* Showing transaction history.

### Important Question

A Read operation answers:

> What existing information should be accessed or displayed?

---

## 1.5 Update

Update means changing an existing item.

Initial item:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

Suppose the quantity changes from `2` to `5`.

```js
items[0].quantity = 5;
```

Updated item:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 5
}
```

The object already existed.

The operation did not create another product. It changed an existing property.

### Real Application Examples

Update operations include:

* Changing a product price.
* Renaming a task.
* Marking a task as completed.
* Updating a user profile.
* Changing an employee’s department.
* Marking a shopping item as purchased.
* Updating an inventory quantity.

### Important Question

An Update operation answers:

> Which existing item should change, and what should its new values be?

---

## 1.6 Delete

Delete means removing an existing item.

Initial state:

```js
let items = [
  {
    id: 1,
    name: "Laptop"
  },
  {
    id: 2,
    name: "Phone"
  }
];
```

Delete the first item:

```js
items.splice(0, 1);
```

Final state:

```js
[
  {
    id: 2,
    name: "Phone"
  }
]
```

### Real Application Examples

Delete operations include:

* Removing a task.
* Deleting a product.
* Removing a student record.
* Deleting an expense.
* Removing a shopping-cart item.
* Deleting a comment.

### Important Question

A Delete operation answers:

> Which existing item should be removed?

---

## 1.7 CRUD as an Item Lifecycle

An item can pass through several stages.

Consider a task.

### Before Create

The task does not exist.

```text
No task exists.
```

### After Create

```js
{
  id: 1,
  title: "Study JavaScript",
  completed: false
}
```

### During Read

The application displays:

```text
1. Study JavaScript — Incomplete
```

### After Update

```js
{
  id: 1,
  title: "Study JavaScript",
  completed: true
}
```

The displayed result becomes:

```text
1. Study JavaScript — Completed
```

### After Delete

The object is removed.

```text
No tasks available.
```

The complete lifecycle is:

```text
Item does not exist
        ↓
      Create
        ↓
Item exists in state
        ↓
       Read
        ↓
Item is displayed
        ↓
      Update
        ↓
Item contains new values
        ↓
      Delete
        ↓
Item no longer exists
```

---

## 1.8 Identifying CRUD Operations

Before writing code, identify the required CRUD operation.

### Requirement 1

> Allow users to add a new product.

Operation:

```text
Create
```

### Requirement 2

> Display all available products.

Operation:

```text
Read
```

### Requirement 3

> Allow users to change a product price.

Operation:

```text
Update
```

### Requirement 4

> Allow users to remove a product.

Operation:

```text
Delete
```

### Requirement 5

> Mark a task as completed.

Operation:

```text
Update
```

The task already exists. Its `completed` property changes.

### Requirement 6

> Search for products by name.

Operation:

```text
Read
```

Searching retrieves matching information.

### Requirement 7

> Add an item to a shopping cart.

Operation:

```text
Create
```

A new cart item is added.

### Requirement 8

> Increase the quantity of an existing cart item.

Operation:

```text
Update
```

The cart item already exists.

---

## 1.9 Arrays as Collections

An array stores multiple values.

```js
const itemNames = [
  "Laptop",
  "Phone",
  "Keyboard"
];
```

Indexes begin at `0`.

```js
console.log(itemNames[0]);
console.log(itemNames[1]);
console.log(itemNames[2]);
```

Expected output:

```text
Laptop
Phone
Keyboard
```

An array is useful because an application usually manages many items.

For example:

```js
let tasks = [];
let products = [];
let students = [];
let expenses = [];
```

---

## 1.10 Objects as Items

An object groups related information.

```js
const product = {
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
};
```

Accessing properties:

```js
console.log(product.id);
console.log(product.name);
console.log(product.price);
console.log(product.quantity);
```

Expected output:

```text
1
Laptop
50000
2
```

An object is useful because a product contains several related values.

Without an object, the values would be disconnected:

```js
const productName = "Laptop";
const productPrice = 50000;
const productQuantity = 2;
```

With an object, they belong together:

```js
const product = {
  name: "Laptop",
  price: 50000,
  quantity: 2
};
```

---

## 1.11 Arrays of Objects

A CRUD application commonly uses an array of objects.

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 5
  }
];
```

The array stores the collection.

```js
products
```

Each object represents one product.

```js
products[0]
```

Expected value:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

Each property stores one detail about the product.

```js
products[0].name;
products[0].price;
products[0].quantity;
```

---

## 1.12 Why Items Need Unique IDs

Consider these products:

```js
const products = [
  {
    id: 1,
    name: "Keyboard",
    price: 3000
  },
  {
    id: 2,
    name: "Keyboard",
    price: 4500
  }
];
```

Both products have the same name.

The names cannot reliably identify the correct product.

The IDs are different:

```text
First keyboard ID: 1
Second keyboard ID: 2
```

A unique ID helps the application answer:

* Which item should be displayed?
* Which item should be updated?
* Which item should be deleted?
* Which item did the user click?

For example:

```js
const selectedId = 2;
```

This means:

```text
Work with the item whose ID is 2.
```

---

## 1.13 ID Versus Array Index

An array index represents the current position of an item.

```js
const products = [
  { id: 10, name: "Laptop" },
  { id: 25, name: "Phone" }
];
```

The first product has:

```text
Index: 0
ID: 10
```

The second product has:

```text
Index: 1
ID: 25
```

The ID and index are not the same.

```js
products[0].id;
```

Expected value:

```text
10
```

After deleting the first product:

```js
products.splice(0, 1);
```

The phone moves to index `0`, but its ID remains `25`.

```js
[
  {
    id: 25,
    name: "Phone"
  }
]
```

Therefore:

* Indexes can change.
* IDs should remain stable.

---

## 1.14 Syntax Breakdown

### Create Syntax

```js
items.push(newItem);
```

#### `items`

The array that stores all items.

```js
let items = [];
```

#### `push()`

Adds a value to the end of an array.

```js
items.push(...);
```

#### `newItem`

The object being added.

```js
const newItem = {
  id: 1,
  name: "Laptop"
};
```

Complete example:

```js
let items = [];

const newItem = {
  id: 1,
  name: "Laptop"
};

items.push(newItem);
```

Final state:

```js
[
  {
    id: 1,
    name: "Laptop"
  }
]
```

---

### Read Syntax

```js
console.log(items[0].name);
```

#### `items[0]`

Accesses the first object.

#### `.name`

Accesses the object’s `name` property.

Expected output:

```text
Laptop
```

---

### Update Syntax

```js
items[0].name = "Gaming Laptop";
```

#### `items[0].name`

The property that will be changed.

#### `=`

The assignment operator.

#### `"Gaming Laptop"`

The new value.

Updated object:

```js
{
  id: 1,
  name: "Gaming Laptop"
}
```

---

### Delete Syntax

```js
items.splice(0, 1);
```

#### First argument: `0`

The index where removal starts.

#### Second argument: `1`

The number of items to remove.

Initial state:

```js
[
  { id: 1, name: "Laptop" },
  { id: 2, name: "Phone" }
]
```

Final state:

```js
[
  { id: 2, name: "Phone" }
]
```

---

# 1.15 Three Complete Examples

## Example 1: Basic CRUD with Strings

### Problem

Manage a simple array of item names.

You will:

* Add a name.
* Display all names.
* Update one name.
* Delete one name.

This example does not use the DOM.

### Input Data

```js
let itemNames = ["Laptop", "Phone"];
```

### Complete Code

```js
let itemNames = ["Laptop", "Phone"];

// CREATE
itemNames.push("Keyboard");

console.log("After create:");

// READ
for (let index = 0; index < itemNames.length; index++) {
  console.log(itemNames[index]);
}

// UPDATE
itemNames[1] = "Smartphone";

console.log("After update:");

for (let index = 0; index < itemNames.length; index++) {
  console.log(itemNames[index]);
}

// DELETE
itemNames.splice(0, 1);

console.log("After delete:");

for (let index = 0; index < itemNames.length; index++) {
  console.log(itemNames[index]);
}

console.log("Final state:", itemNames);
```

### Step-by-Step Explanation

#### Initial State

```js
["Laptop", "Phone"]
```

#### Create

```js
itemNames.push("Keyboard");
```

State becomes:

```js
["Laptop", "Phone", "Keyboard"]
```

#### Read

The loop displays each value:

```js
for (let index = 0; index < itemNames.length; index++) {
  console.log(itemNames[index]);
}
```

#### Update

```js
itemNames[1] = "Smartphone";
```

The value at index `1` changes.

State becomes:

```js
["Laptop", "Smartphone", "Keyboard"]
```

#### Delete

```js
itemNames.splice(0, 1);
```

The value at index `0` is removed.

Final state:

```js
["Smartphone", "Keyboard"]
```

### Expected Console Output

```text
After create:
Laptop
Phone
Keyboard
After update:
Laptop
Smartphone
Keyboard
After delete:
Smartphone
Keyboard
Final state: ["Smartphone", "Keyboard"]
```

### Final State

```js
["Smartphone", "Keyboard"]
```

### Common Mistakes

#### Confusing an index with an ID

```js
itemNames[1]
```

The number `1` is an index, not a permanent ID.

#### Removing too many items

```js
itemNames.splice(0);
```

This removes everything from index `0` onward.

#### Updating an invalid position

```js
itemNames[20] = "Mouse";
```

This creates empty positions in the array.

### Small Modification Challenge

Modify the example so that:

1. `"Monitor"` is added.
2. `"Laptop"` becomes `"Gaming Laptop"`.
3. `"Phone"` is deleted.
4. The final state is displayed.

---

## Example 2: CRUD with Product Objects

### Problem

Manage products containing:

* An ID.
* A name.
* A price.
* A quantity.

You will use plain loops to find the correct product.

### Input Data

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  }
];
```

### Complete Code

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  }
];

// CREATE
const newProduct = {
  id: 3,
  name: "Keyboard",
  price: 3000,
  quantity: 5
};

products.push(newProduct);

// READ
console.log("Products after create:");

for (let index = 0; index < products.length; index++) {
  const product = products[index];

  console.log(
    `${product.id}. ${product.name} - KSh ${product.price} - Quantity: ${product.quantity}`
  );
}

// UPDATE
const selectedId = 2;
let productWasUpdated = false;

for (let index = 0; index < products.length; index++) {
  if (products[index].id === selectedId) {
    products[index].name = "Smartphone";
    products[index].price = 27000;

    productWasUpdated = true;
    break;
  }
}

if (!productWasUpdated) {
  console.log("Product to update was not found.");
}

// DELETE
const idToDelete = 1;
let indexToDelete = -1;

for (let index = 0; index < products.length; index++) {
  if (products[index].id === idToDelete) {
    indexToDelete = index;
    break;
  }
}

if (indexToDelete !== -1) {
  products.splice(indexToDelete, 1);
} else {
  console.log("Product to delete was not found.");
}

console.log("Final state:", products);
```

### Step-by-Step Explanation

#### Create

```js
products.push(newProduct);
```

The keyboard is added to the array.

#### Read

```js
for (let index = 0; index < products.length; index++) {
  const product = products[index];
  console.log(product.name);
}
```

The loop visits every object.

#### Update

```js
if (products[index].id === selectedId)
```

This checks whether the current product has the selected ID.

When the product is found:

```js
products[index].name = "Smartphone";
products[index].price = 27000;
```

The `break` statement stops the loop.

#### Delete

The code first finds the matching array index:

```js
let indexToDelete = -1;
```

`-1` means:

```text
No matching index has been found.
```

When a match exists:

```js
indexToDelete = index;
```

The product is removed only when the index is valid:

```js
if (indexToDelete !== -1) {
  products.splice(indexToDelete, 1);
}
```

### Expected Console Output

```text
Products after create:
1. Laptop - KSh 50000 - Quantity: 2
2. Phone - KSh 25000 - Quantity: 4
3. Keyboard - KSh 3000 - Quantity: 5
Final state: [
  {
    id: 2,
    name: "Smartphone",
    price: 27000,
    quantity: 4
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 5
  }
]
```

### Final State

```js
[
  {
    id: 2,
    name: "Smartphone",
    price: 27000,
    quantity: 4
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 5
  }
]
```

### Common Mistakes

#### Comparing a numeric ID with a string ID

```js
const selectedId = "2";
```

This comparison fails:

```js
products[index].id === selectedId
```

Because:

```text
2 !== "2"
```

Convert the value:

```js
const selectedId = Number("2");
```

#### Deleting when the index is `-1`

Incorrect:

```js
products.splice(-1, 1);
```

This removes the last product.

Correct:

```js
if (indexToDelete !== -1) {
  products.splice(indexToDelete, 1);
}
```

#### Forgetting `break`

If IDs are unique, the loop can stop after finding the match.

### Small Modification Challenge

Add this product:

```js
{
  id: 4,
  name: "Mouse",
  price: 1500,
  quantity: 8
}
```

Then:

1. Change its quantity to `12`.
2. Delete the product with ID `3`.
3. Display the final products.

---

## Example 3: Basic CRUD Concepts in the Browser

### Problem

Build a small browser application that can:

* Add items.
* Display items.
* Rename the first item.
* Delete the last item.
* Show an empty-state message.
* Validate blank input.

This example introduces the relationship between JavaScript data and displayed HTML.

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

  <title>CRUD Introduction</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      max-width: 700px;
      margin: 0 auto;
      padding: 24px;
      font-family: Arial, sans-serif;
      line-height: 1.5;
    }

    form {
      display: flex;
      gap: 8px;
      margin-bottom: 8px;
    }

    input {
      flex: 1;
      padding: 10px;
    }

    button {
      padding: 10px 14px;
      cursor: pointer;
    }

    .actions {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-bottom: 24px;
    }

    .item {
      margin-bottom: 8px;
      padding: 10px;
      border: 1px solid #cccccc;
      border-radius: 4px;
    }

    .error {
      min-height: 24px;
      margin-top: 0;
    }
  </style>
</head>

<body>
  <h1>Item Manager</h1>

  <form id="item-form">
    <label for="item-name">Item name:</label>

    <input
      id="item-name"
      name="itemName"
      type="text"
      autocomplete="off"
    />

    <button type="submit">Add Item</button>
  </form>

  <p id="error-message" class="error"></p>

  <div class="actions">
    <button id="rename-first-button" type="button">
      Rename First Item
    </button>

    <button id="delete-last-button" type="button">
      Delete Last Item
    </button>
  </div>

  <h2>Items</h2>

  <ul id="item-list"></ul>

  <script>
    let items = [];
    let nextItemId = 1;

    const itemForm = document.getElementById("item-form");
    const itemNameInput = document.getElementById("item-name");
    const itemList = document.getElementById("item-list");
    const errorMessage = document.getElementById("error-message");

    const renameFirstButton = document.getElementById(
      "rename-first-button"
    );

    const deleteLastButton = document.getElementById(
      "delete-last-button"
    );

    function showError(message) {
      errorMessage.textContent = message;
    }

    function clearError() {
      errorMessage.textContent = "";
    }

    function renderItems() {
      itemList.innerHTML = "";

      if (items.length === 0) {
        const emptyMessage = document.createElement("li");

        emptyMessage.textContent = "No items available.";

        itemList.appendChild(emptyMessage);
        return;
      }

      for (let index = 0; index < items.length; index++) {
        const item = items[index];

        const listItem = document.createElement("li");

        listItem.classList.add("item");

        listItem.textContent = `${item.id}. ${item.name}`;

        itemList.appendChild(listItem);
      }
    }

    itemForm.addEventListener("submit", function (event) {
      event.preventDefault();

      clearError();

      const itemName = itemNameInput.value.trim();

      if (itemName === "") {
        showError("Please enter an item name.");
        return;
      }

      const newItem = {
        id: nextItemId,
        name: itemName
      };

      items.push(newItem);

      nextItemId += 1;

      itemNameInput.value = "";

      renderItems();
    });

    renameFirstButton.addEventListener("click", function () {
      clearError();

      if (items.length === 0) {
        showError("There is no item to rename.");
        return;
      }

      items[0].name = "Updated Item";

      renderItems();
    });

    deleteLastButton.addEventListener("click", function () {
      clearError();

      if (items.length === 0) {
        showError("There is no item to delete.");
        return;
      }

      items.splice(items.length - 1, 1);

      renderItems();
    });

    renderItems();
  </script>
</body>
</html>
```

### Step-by-Step Explanation

#### Step 1: Create the state array

```js
let items = [];
```

The application begins with no items.

#### Step 2: Track the next ID

```js
let nextItemId = 1;
```

The first item receives ID `1`.

#### Step 3: Select DOM elements

```js
const itemForm = document.getElementById("item-form");
const itemNameInput = document.getElementById("item-name");
const itemList = document.getElementById("item-list");
```

These variables store references to elements in the webpage.

#### Step 4: Render the state

```js
function renderItems() {
  itemList.innerHTML = "";
}
```

The existing displayed list is cleared before rebuilding it.

#### Step 5: Handle an empty array

```js
if (items.length === 0) {
  const emptyMessage = document.createElement("li");
  emptyMessage.textContent = "No items available.";
  itemList.appendChild(emptyMessage);
  return;
}
```

Expected DOM output:

```text
Items

No items available.
```

#### Step 6: Create list elements

```js
const listItem = document.createElement("li");
```

The element is created in memory.

It becomes visible after:

```js
itemList.appendChild(listItem);
```

#### Step 7: Use `textContent`

```js
listItem.textContent = `${item.id}. ${item.name}`;
```

This inserts the item name as text.

> Avoid placing untrusted user input directly inside `innerHTML`. It may be interpreted as HTML instead of plain text.

#### Step 8: Listen for form submission

```js
itemForm.addEventListener("submit", function (event) {
  // Create operation
});
```

#### Step 9: Prevent the page from reloading

```js
event.preventDefault();
```

Without this line, the form may reload the page.

#### Step 10: Read the input

```js
const itemName = itemNameInput.value.trim();
```

* `.value` gets the input text.
* `.trim()` removes spaces from the beginning and end.

#### Step 11: Validate the input

```js
if (itemName === "") {
  showError("Please enter an item name.");
  return;
}
```

A blank item is not created.

#### Step 12: Create an object

```js
const newItem = {
  id: nextItemId,
  name: itemName
};
```

#### Step 13: Add it to the array

```js
items.push(newItem);
```

#### Step 14: Clear the input

```js
itemNameInput.value = "";
```

#### Step 15: Render the updated state

```js
renderItems();
```

#### Step 16: Update the first item

```js
items[0].name = "Updated Item";
```

The array changes first.

Then:

```js
renderItems();
```

The webpage is updated.

#### Step 17: Delete the last item

```js
items.splice(items.length - 1, 1);
```

Then:

```js
renderItems();
```

The removed item disappears from the webpage.

### Expected DOM Output

After adding `Laptop`:

```text
Items

1. Laptop
```

State:

```js
[
  {
    id: 1,
    name: "Laptop"
  }
]
```

After adding `Phone`:

```text
Items

1. Laptop
2. Phone
```

State:

```js
[
  {
    id: 1,
    name: "Laptop"
  },
  {
    id: 2,
    name: "Phone"
  }
]
```

After clicking **Rename First Item**:

```text
Items

1. Updated Item
2. Phone
```

After clicking **Delete Last Item**:

```text
Items

1. Updated Item
```

Final state:

```js
[
  {
    id: 1,
    name: "Updated Item"
  }
]
```

### Common Mistakes

#### Forgetting `preventDefault()`

```js
event.preventDefault();
```

Without it, the page may reload and lose the current state.

#### Updating state without rendering

```js
items.push(newItem);
```

The object is added, but the page will not show it until:

```js
renderItems();
```

#### Changing only the DOM

```js
listItem.textContent = "Updated Item";
```

This changes what the user sees but does not automatically update the object in `items`.

#### Recreating state inside the event handler

Incorrect:

```js
itemForm.addEventListener("submit", function () {
  let items = [];
});
```

A new empty array would be created during every submission.

#### Using `innerHTML` with user input

Unsafe:

```js
listItem.innerHTML = itemName;
```

Safer:

```js
listItem.textContent = itemName;
```

### Small Modification Challenge

Modify the application so that:

1. The rename button changes the first item to `"Featured Item"`.
2. The delete button asks for confirmation.
3. The empty-state message returns after deleting the final item.
4. Whitespace-only names are rejected.
5. The input receives focus after a successful addition.

---

## 1.16 CRUD Comparison

| Operation | Main Question                        | Typical Result                      |
| --------- | ------------------------------------ | ----------------------------------- |
| Create    | What new item should be added?       | Array becomes longer                |
| Read      | What information should be accessed? | State usually stays unchanged       |
| Update    | Which item should change?            | Existing object receives new values |
| Delete    | Which item should be removed?        | Array becomes shorter               |

---

## 1.17 Common Beginner Mistakes

### Mistake 1: Confusing Create and Update

This creates a new product:

```js
products.push({
  id: 3,
  name: "Mouse"
});
```

This updates an existing product:

```js
products[0].name = "Gaming Laptop";
```

---

### Mistake 2: Confusing Read and Update

This reads:

```js
console.log(products[0].name);
```

This updates:

```js
products[0].name = "Desktop";
```

---

### Mistake 3: Treating an Index as a Permanent ID

```js
products[0]
```

The `0` represents the current array position.

It is not necessarily the product’s ID.

---

### Mistake 4: Deleting Without Checking the Index

```js
products.splice(-1, 1);
```

This removes the last product.

Always verify that the matching index exists.

---

### Mistake 5: Reusing IDs

Incorrect:

```js
const products = [
  { id: 1, name: "Laptop" },
  { id: 1, name: "Phone" }
];
```

Both items have ID `1`.

This makes updates and deletions unreliable.

---

### Mistake 6: Changing State Without Updating the DOM

```js
items.push(newItem);
```

The array changes, but the user may not see the new item until rendering occurs.

---

### Mistake 7: Changing the DOM Without Updating State

```js
listItem.textContent = "Changed Product";
```

The displayed text changes, but the JavaScript array may still contain the old name.

---

## 1.18 Beginner Questions

1. What does CRUD stand for?
2. What does the Create operation do?
3. What does the Read operation do?
4. What does the Update operation do?
5. What does the Delete operation do?
6. Which CRUD operation adds a new task?
7. Which CRUD operation displays products?
8. Which CRUD operation changes a product price?
9. Which CRUD operation removes a task?
10. Why are arrays useful in CRUD applications?
11. Why are objects useful for representing products?
12. What is the purpose of an item ID?
13. What is an array index?
14. Are an ID and an index always the same?
15. Does reading data always change the state?

---

## 1.19 Intermediate Questions

1. Why is marking a task as completed an Update operation?
2. Why is searching considered a Read operation?
3. Why should IDs be unique?
4. What problem occurs when two objects have the same ID?
5. Why can array indexes change?
6. Why should IDs normally remain stable?
7. What happens when `splice(-1, 1)` runs?
8. Why should input be validated before creating an object?
9. Why should the state array not be recreated inside every event handler?
10. Why must a webpage be rendered after state changes?
11. What happens when only the DOM changes?
12. What happens when only the array changes?
13. Why is `2 === "2"` false?
14. How can a string ID be converted to a number?
15. Why is `break` useful after finding a unique ID?

---

## 1.20 Advanced Questions

1. Why is CRUD an operation model rather than a specific JavaScript technique?
2. Why can both `splice()` and `filter()` represent deletion?
3. Why can direct object mutation become difficult to track?
4. Why might immutable updates be useful?
5. Why should the JavaScript state become the main source of truth?
6. What problems occur when the DOM and state disagree?
7. How would you prevent duplicate IDs?
8. Should filtered results permanently replace the original state?
9. How can CRUD logic be separated from DOM-rendering logic?
10. Why should invalid application state be prevented before rendering?

---

## 1.21 Output Prediction Questions

### Question 1

```js
let items = ["Laptop"];

items.push("Phone");

console.log(items);
```

Predict the output and final state.

---

### Question 2

```js
const product = {
  id: 1,
  name: "Laptop"
};

console.log(product.name);

product.name = "Desktop";

console.log(product.name);
```

Predict both outputs.

---

### Question 3

```js
let items = ["Laptop", "Phone", "Keyboard"];

items.splice(1, 1);

console.log(items);
```

Predict the final array.

---

### Question 4

```js
let items = ["Laptop", "Phone", "Keyboard"];

items.splice(-1, 1);

console.log(items);
```

Which item is removed?

---

### Question 5

```js
const itemId = 2;
const selectedId = "2";

console.log(itemId === selectedId);
```

Predict the output.

---

### Question 6

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    quantity: 2
  }
];

products[0].quantity += 3;

console.log(products[0].quantity);
```

Predict the output.

---

### Question 7

```js
let tasks = [];

tasks.push({
  id: 1,
  title: "Study",
  completed: false
});

tasks[0].completed = true;

console.log(tasks);
```

Predict the final state.

---

### Question 8

```js
let items = [];

items.push({
  id: 1,
  name: "Laptop"
});
```

Assume there is a webpage but no render function is called.

What exists in the array?

What does the user see?

---

## 1.22 Debugging Exercises

For every exercise:

1. Identify the bug.
2. Explain why it happens.
3. Correct the code.
4. State the expected result.

### Debugging Exercise 1: Form Reloads

```js
itemForm.addEventListener("submit", function (event) {
  const newItem = {
    id: 1,
    name: itemNameInput.value
  };

  items.push(newItem);
});
```

---

### Debugging Exercise 2: State Resets

```js
function addItem(itemName) {
  let items = [];

  items.push({
    id: 1,
    name: itemName
  });
}
```

---

### Debugging Exercise 3: Wrong ID Type

```js
const item = {
  id: 4,
  name: "Keyboard"
};

const selectedId = "4";

if (item.id === selectedId) {
  item.name = "Mechanical Keyboard";
}
```

---

### Debugging Exercise 4: Wrong Item Deleted

```js
let items = [
  { id: 1, name: "Laptop" },
  { id: 2, name: "Phone" }
];

const indexToDelete = -1;

items.splice(indexToDelete, 1);
```

---

### Debugging Exercise 5: DOM Does Not Change

```js
items.push({
  id: 3,
  name: "Keyboard"
});
```

Assume `renderItems()` already exists.

---

### Debugging Exercise 6: State Does Not Change

```js
const listItem = document.querySelector(".item");

listItem.textContent = "Updated Laptop";
```

The object in `items` still contains `"Laptop"`.

---

### Debugging Exercise 7: Blank Item Created

```js
const itemName = itemNameInput.value;

items.push({
  id: 1,
  name: itemName
});
```

---

### Debugging Exercise 8: Unsafe Input

```js
listItem.innerHTML = itemNameInput.value;
```

Explain the danger and replace it with a safer approach.

---

## 1.23 Coding Exercises

### Exercise 1: Create a Product

Start with:

```js
let products = [];
```

Add:

```js
{
  id: 1,
  name: "Laptop",
  price: 50000,
  quantity: 2
}
```

---

### Exercise 2: Read Product Names

```js
const products = [
  { id: 1, name: "Laptop" },
  { id: 2, name: "Phone" },
  { id: 3, name: "Keyboard" }
];
```

Use a standard `for` loop to display every name.

Expected output:

```text
Laptop
Phone
Keyboard
```

---

### Exercise 3: Update a Product

Change the product with ID `2` so its quantity becomes `10`.

```js
let products = [
  { id: 1, name: "Laptop", quantity: 2 },
  { id: 2, name: "Phone", quantity: 4 }
];
```

---

### Exercise 4: Delete a Product Safely

Delete the product with ID `3`.

```js
let products = [
  { id: 1, name: "Laptop" },
  { id: 2, name: "Phone" },
  { id: 3, name: "Keyboard" }
];
```

Use:

* A standard loop.
* An `indexToDelete` variable.
* A check for `-1`.
* `splice()`.

---

### Exercise 5: Classify CRUD Requirements

Write `Create`, `Read`, `Update`, or `Delete` next to each:

1. Add a new employee.
2. Display all employees.
3. Change an employee’s department.
4. Remove a former employee.
5. Search for an employee.
6. Mark a notification as read.
7. Add an expense.
8. Remove an item from a cart.

---

### Exercise 6: Create and Read in the DOM

Build a webpage containing:

* An empty state array.
* A form.
* A text input.
* An Add button.
* A `<ul>`.
* Input validation.
* An empty-state message.
* A reusable render function.

Implement only Create and Read.

---

## 1.24 Subtopic Completion Task

Create a file named:

```text
crud-foundations-practice.js
```

Add this starting state:

```js
let products = [
  {
    id: 1,
    name: "Laptop",
    price: 50000,
    quantity: 2
  },
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 4
  }
];
```

Complete these operations:

1. Add a keyboard with ID `3`.
2. Display all products using a standard `for` loop.
3. Update the phone quantity to `7`.
4. Delete the laptop safely.
5. Display the final state.
6. Add comments identifying Create, Read, Update, and Delete.

Expected final state:

```js
[
  {
    id: 2,
    name: "Phone",
    price: 25000,
    quantity: 7
  },
  {
    id: 3,
    name: "Keyboard",
    price: 3000,
    quantity: 5
  }
]
```

---

## Key Takeaways

* CRUD means Create, Read, Update, and Delete.
* Create adds new information.
* Read accesses or displays information.
* Update changes existing information.
* Delete removes information.
* Arrays store collections of items.
* Objects store the properties of individual items.
* Arrays of objects are commonly used as application state.
* IDs help identify specific items.
* IDs and array indexes are different.
* Indexes can change when items are added or deleted.
* State changes do not automatically update the webpage.
* DOM changes do not automatically update JavaScript state.
* CRUD operations should validate IDs and user input.
* The next subtopic will explain JavaScript state in detail.

---

## Continue Learning

| Direction         | Link                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Previous Topic    | [Course Introduction](../00-course-introduction/README.md)                                |
| Previous Subtopic | [JavaScript CRUD Course Overview](../00-course-introduction/01-course-overview.md)        |
| Next Subtopic     | [Understanding JavaScript State](./02-understanding-javascript-state.md)                  |
| Next Topic        | [JavaScript State and DOM Synchronization](../02-state-and-dom-synchronization/README.md) |

> **Current Position:** Phase 1 → Module 1 → Topic 1 → Subtopic 1

When you are ready to continue to **Understanding JavaScript State**, write:

```text
Next
```
