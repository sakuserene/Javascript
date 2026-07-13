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

