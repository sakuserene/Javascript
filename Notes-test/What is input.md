
# Module 0 – Foundations for CRUD

# Lesson 0.3 – Objects Review for CRUD (Part 1)
## Understanding JavaScript Objects

> **Goal:** Build a deep understanding of JavaScript objects, why they exist, how they are stored in memory, and why they are the foundation of nearly every CRUD application.

---

# Table of Contents

- [Learning Objectives](#learning-objectives)
- [Prerequisites](#prerequisites)
- [Estimated Study Time](#estimated-study-time)
- [What Is an Object?](#what-is-an-object)
- [Why Do Objects Exist?](#why-do-objects-exist)
- [Objects vs Variables](#objects-vs-variables)
- [Real-World Objects](#real-world-objects)
- [Object Syntax](#object-syntax)
- [Properties and Values](#properties-and-values)
- [Dot Notation](#dot-notation)
- [Bracket Notation](#bracket-notation)
- [When to Use Each](#when-to-use-each)
- [How JavaScript Stores Objects](#how-javascript-stores-objects)
- [Memory Diagram](#memory-diagram)
- [Mental Model](#mental-model)
- [Step-by-Step Execution](#step-by-step-execution)
- [Summary](#summary)

---

# Learning Objectives

After completing this lesson you should be able to:

- Explain what an object is.
- Explain why objects exist.
- Create objects.
- Read object properties.
- Access properties using dot notation.
- Access properties using bracket notation.
- Explain how JavaScript stores objects.
- Explain why objects are reference values.

---

# Prerequisites

You should already understand:

- Variables
- Primitive values
- Arrays

---

# Estimated Study Time

| Activity | Time |
|----------|------|
| Reading | 20 min |
| Coding | 30 min |
| Practice | 40 min |

---

# What Is an Object?

An **object** is a collection of **related information** stored together using **key–value pairs**.

Think of an object as a digital record describing one thing.

Example:

```javascript
const student = {
    id: 1,
    name: "John",
    age: 20
};
```

This object describes one student.

---

# Why Do Objects Exist?

Suppose you want to represent a student.

Without objects:

```javascript
let id = 1;
let name = "John";
let age = 20;
```

This works for one student.

But imagine 5,000 students.

You would end up with thousands of unrelated variables.

Objects solve this problem by grouping related data together.

```javascript
const student = {
    id: 1,
    name: "John",
    age: 20
};
```

Everything about the student stays together.

---

# Objects vs Variables

Instead of:

```javascript
let productName = "Laptop";
let productPrice = 75000;
let productQuantity = 5;
```

Use:

```javascript
const product = {
    name: "Laptop",
    price: 75000,
    quantity: 5
};
```

The second approach is easier to read, easier to maintain, and scales much better.

---

# Real-World Objects

Almost everything in software can be modeled as an object.

Student

```javascript
const student = {
    id: 1,
    name: "Ali",
    age: 19,
    grade: "A"
};
```

Book

```javascript
const book = {
    title: "Eloquent JavaScript",
    author: "Marijn Haverbeke",
    pages: 472
};
```

Car

```javascript
const car = {
    make: "Mazda",
    model: "Demio",
    year: 2015
};
```

Notice the pattern:

One real-world thing → One JavaScript object.

---

# Object Syntax

General syntax:

```javascript
const objectName = {
    key: value,
    key: value,
    key: value
};
```

Example:

```javascript
const person = {
    firstName: "Jane",
    lastName: "Doe",
    age: 24,
    employed: true
};
```

---

# Properties and Values

Every object consists of **properties**.

```javascript
const student = {
    id: 1,
    name: "John",
    age: 20
};
```

| Property | Value |
|----------|-------|
| id | 1 |
| name | "John" |
| age | 20 |

The **property** is the name.

The **value** is the data stored under that name.

---

# Dot Notation

The most common way to access a property.

```javascript
console.log(student.name);
```

Output

```text
John
```

More examples

```javascript
console.log(student.id);

console.log(student.age);
```

Output

```text
1
20
```

---

# Bracket Notation

Another way to access properties.

```javascript
console.log(student["name"]);
```

Output

```text
John
```

Example

```javascript
console.log(student["age"]);
```

Output

```text
20
```

---

# When to Use Each

## Dot Notation

Use when you already know the property name.

Example

```javascript
student.name
```

Easy to read.

Preferred in most situations.

---

## Bracket Notation

Use when the property name is stored in a variable.

Example

```javascript
const property = "name";

console.log(student[property]);
```

Output

```text
John
```

This is extremely useful when writing reusable CRUD functions.

---

# Why Bracket Notation Matters in CRUD

Imagine searching different properties.

```javascript
function printProperty(student, property) {
    console.log(student[property]);
}

printProperty(student, "age");

printProperty(student, "name");
```

One function can now access any property dynamically.

---

# How JavaScript Stores Objects

Objects are stored differently from primitive values.

When you create:

```javascript
const student = {
    id: 1,
    name: "John"
};
```

JavaScript creates the object somewhere in memory.

The variable stores only a reference.

---

# Memory Diagram

```text
student
   │
   │
   ▼
Memory Address A1
   │
   ▼
┌───────────────────────┐
│ id   → 1              │
│ name → "John"         │
└───────────────────────┘
```

The variable **does not store the object itself**.

It stores the location of the object.

---

# Mental Model

Think of an object as a student file.

```text
Student File

ID:
1

Name:
John

Age:
20

Grade:
A
```

Each label is a property.

Each value describes the same student.

---

# Step-by-Step Execution

```javascript
const student = {
    id: 1,
    name: "John"
};

console.log(student.name);
```

Execution:

Step 1

JavaScript creates the object.

↓

Step 2

Stores it in memory.

↓

Step 3

Stores the memory reference in `student`.

↓

Step 4

Looks for property `"name"`.

↓

Step 5

Returns `"John"`.

---

# Common Mistakes

❌ Treating objects like arrays.

```javascript
student[0]
```

Wrong.

Objects use property names, not numeric indexes.

---

❌ Misspelling property names.

```javascript
student.nmae
```

Returns

```text
undefined
```

---

❌ Forgetting quotes in bracket notation.

Wrong

```javascript
student[name]
```

Correct

```javascript
student["name"]
```

Unless `name` is a variable.

---

# Debugging Tips

Always inspect the whole object.

```javascript
console.log(student);
```

Check whether a property exists.

```javascript
console.log(student.name);
```

Log the property name when using bracket notation.

```javascript
console.log(property);
console.log(student[property]);
```

---

# Best Practices

- Group related data into objects.
- Use descriptive property names.
- Prefer dot notation when the property name is known.
- Use bracket notation for dynamic property access.
- Keep object structures consistent across similar records.

---

# Summary

Objects let us represent real-world entities by grouping related information into key–value pairs. They are reference values stored in memory, and they form the building blocks of CRUD applications. Understanding object creation, property access, and how objects are stored prepares you for working with collections of objects—the most common data structure in real-world JavaScript applications.

---

# Key Takeaways

- An object represents one entity.
- Objects store data as key–value pairs.
- Dot notation is the preferred way to access known properties.
- Bracket notation enables dynamic property access.
- Objects are stored by reference in memory.
- Nearly every CRUD application manipulates arrays of objects.

---

## Next

**Lesson 0.3 – Part 2: Nested Objects, Arrays of Objects, and Object Traversal**

You'll learn how professional applications model complex data such as students with courses, products with categories, and orders with multiple items, and how to traverse these structures using plain loops.
