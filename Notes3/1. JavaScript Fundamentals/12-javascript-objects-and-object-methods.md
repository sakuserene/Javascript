# Lesson 12: JavaScript Objects and Object Methods

**Course:** JavaScript Master Course  
**Level:** Beginner to Advanced  
**Previous:** [Lesson 11 - Arrays and Array Methods](11-javascript-arrays-and-array-methods.md)  
**Next:** [Lesson 13 - Object-Oriented Programming](13-object-oriented-programming-in-javascript.md)

## Table of Contents

1. [What is an object?](#1-what-is-an-object)
2. [Objects versus arrays](#2-objects-versus-arrays)
3. [Creating objects](#3-creating-objects)
4. [Accessing properties](#4-accessing-properties)
5. [Adding, updating, and deleting properties](#5-adding-updating-and-deleting-properties)
6. [Checking whether a property exists](#6-checking-whether-a-property-exists)
7. [Object methods](#7-object-methods)
8. [Understanding this](#8-understanding-this)
9. [Nested objects](#9-nested-objects)
10. [Optional chaining and nullish coalescing](#10-optional-chaining-and-nullish-coalescing)
11. [Object destructuring](#11-object-destructuring)
12. [Property shorthand](#12-property-shorthand)
13. [Computed property names](#13-computed-property-names)
14. [Object.keys, Object.values, and Object.entries](#14-objectkeys-objectvalues-and-objectentries)
15. [Iterating with for...in](#15-iterating-with-forin)
16. [Object references and equality](#16-object-references-and-equality)
17. [Shallow and deep copying](#17-shallow-and-deep-copying)
18. [Object.freeze and Object.seal](#18-objectfreeze-and-objectseal)
19. [Getters and setters](#19-getters-and-setters)
20. [Property descriptors](#20-property-descriptors)
21. [Practical project: inventory manager](#21-practical-project-inventory-manager)
22. [Ten interview questions and answers](#22-ten-interview-questions-and-answers)
23. [Ten coding exercises with complete solutions](#23-ten-coding-exercises-with-complete-solutions)
24. [Common mistakes and debugging](#24-common-mistakes-and-debugging)
25. [Lesson summary](#25-lesson-summary)
26. [Navigation](#26-navigation)

---

## 1. What is an object?

A JavaScript object groups related information into **properties**. A property is an association between a key (a string or symbol) and a value (which can be any JavaScript value).

```javascript
const product = {
  name: "Laptop",
  price: 50000,
  quantity: 3,
  available: true
};

console.log(product.name); // Laptop
```

### Explain every part

| Part | Explanation |
| --- | --- |
| `const` | Declares a binding that cannot be reassigned. The object itself can still change. |
| `product` | Name of the variable containing the object reference. |
| `=` | Assignment operator; initializes `product`. |
| `{` and `}` | Begin and end an object literal. |
| `name` | Property key, interpreted as the string `"name"`. |
| `:` | Separates a property key from its value. |
| `"Laptop"` | String value of `name`. |
| `,` | Separates property definitions. |
| `price: 50000` | Numeric property. |
| `available: true` | Boolean property. |
| `;` | Terminates the declaration. |

An object is **not** an array. Its values are normally accessed by named properties rather than numeric positions.

## 2. Objects versus arrays

```javascript
const studentArray = ["Alice", 20, 85];
const studentObject = { name: "Alice", age: 20, score: 85 };

console.log(studentArray[0]); // Alice
console.log(studentObject.name); // Alice
```

- Array indexes are normally numeric positions, starting at zero.
- Object keys describe what a value means.
- Arrays are specialized objects with array-specific methods and `length` behavior.
- A normal object has no built-in array-style `length` property.

## 3. Creating objects

### Object literal (most common)

```javascript
const person = { name: "Alice", age: 25 };
```

### Object constructor

```javascript
const person = new Object();
person.name = "Alice";
person.age = 25;
console.log(person); // { name: 'Alice', age: 25 }
```

`new` constructs an object; `Object()` is the built-in constructor. The assignments create properties.

### Object.create(null)

```javascript
const record = Object.create(null);
record.name = "Alice";
console.log(record.name); // Alice
```

This object has no prototype. For example, it does not inherit `toString` from `Object.prototype`.

### Factory function

```javascript
function createProduct(name, price) {
  return { name, price };
}

const first = createProduct("Laptop", 50000);
const second = createProduct("Mouse", 1500);
console.log(first); // { name: 'Laptop', price: 50000 }
console.log(second); // { name: 'Mouse', price: 1500 }
```

**Trace for `createProduct("Laptop", 50000)`:** (1) arguments are evaluated; (2) `name` becomes `"Laptop"`; (3) `price` becomes `50000`; (4) `{ name, price }` creates a new object; (5) `return` sends that object to the caller; (6) `first` receives its reference.

## 4. Accessing properties

```javascript
const student = { name: "Alice", age: 20, score: 85 };
console.log(student.name);     // Alice
console.log(student["name"]); // Alice

const property = "score";
console.log(student[property]); // 85
console.log(student.property);  // undefined
```

**Dot notation:** `student.name` looks for a property literally called `"name"`.

**Bracket notation:** `student[property]` first evaluates the expression `property`, obtaining `"score"`, and then reads `student["score"]`.

The expression `student.property` is different: it looks for the literal key `"property"`.

Use bracket notation for keys containing spaces:

```javascript
const person = { "first name": "Alice" };
console.log(person["first name"]); // Alice
```

Missing properties normally evaluate to `undefined`. Trying to access a property of `null` or `undefined` throws unless optional chaining is used.

## 5. Adding, updating, and deleting properties

```javascript
const product = { name: "Laptop", price: 50000 };
product.quantity = 3;    // Add
product.price = 45000;   // Update
delete product.quantity; // Delete
console.log(product);    // { name: 'Laptop', price: 45000 }
```

**Execution trace:**

| Step | Statement | State |
| --- | --- | --- |
| 1 | Object literal | `{ name: 'Laptop', price: 50000 }` |
| 2 | `product.quantity = 3` | Adds `quantity: 3` |
| 3 | `product.price = 45000` | Replaces price |
| 4 | `delete product.quantity` | Removes the property |

`delete` removes an object property when permitted; it does not destroy the object. `const` prevents reassigning `product`, not modifying its properties.

## 6. Checking whether a property exists

```javascript
const record = { result: undefined };
console.log("result" in record);              // true
console.log(Object.hasOwn(record, "result")); // true
console.log(record.result === undefined);     // true
```

- `in` checks own **and inherited** properties.
- `Object.hasOwn(object, key)` checks only own properties.
- Comparing to `undefined` does not prove that a property is missing: it might exist with an `undefined` value.

## 7. Object methods

A function stored as a property is commonly called a method.

```javascript
const calculator = {
  add(a, b) {
    return a + b;
  },
  multiply(a, b) {
    return a * b;
  }
};

console.log(calculator.add(10, 5));      // 15
console.log(calculator.multiply(10, 5)); // 50
```

`add(a, b) { ... }` is method shorthand for defining a function-valued property. In `calculator.add(10, 5)`, the dot selects the method, parentheses invoke it, and `10`, `5` become arguments for `a`, `b`.

## 8. Understanding this

For an ordinary function invoked as `object.method()`, `this` generally refers to the receiver object.

```javascript
const person = {
  name: "Alice",
  introduce() {
    return "My name is " + this.name;
  }
};
console.log(person.introduce()); // My name is Alice
```

**Trace:** `person.introduce()` calls the function with `person` as receiver. `this.name` reads `person.name`, yielding `"Alice"`. String concatenation produces `"My name is Alice"`.

**Important difference:** Arrow functions do not have their own `this`. They capture `this` from the surrounding lexical environment, so this is usually inappropriate when a method needs the receiver:

```javascript
const person = {
  name: "Alice",
  introduce: () => this.name // Does NOT bind this to person
};
```

Extracting a method also changes how it is called:

```javascript
const greet = person.introduce; // function reference, not a method call
// greet() does not receive person as this
```

To preserve a receiver for an ordinary method, use `.bind(person)` or call the function through the object.

## 9. Nested objects

```javascript
const student = {
  name: "Alice",
  address: { city: "Nairobi", country: "Kenya" },
  subjects: [
    { name: "Math", score: 85 },
    { name: "English", score: 90 }
  ]
};

console.log(student.address.city);     // Nairobi
console.log(student.subjects[1].score); // 90
```

**Trace for `student.subjects[1].score`:** `student` -> `subjects` array -> element at index `1` (English object) -> its `score` property -> `90`.

## 10. Optional chaining and nullish coalescing

```javascript
const student = { name: "Alice" };
const city = student.address?.city ?? "Unknown";
console.log(city); // Unknown
```

- `student.address` is `undefined`.
- `?.city` stops safely instead of accessing `.city` on `undefined`.
- `??` uses the right-hand value when the left-hand value is `null` or `undefined`.
- `"Unknown"` is the fallback.

**Contrast with `||`:** `0 ?? 100` returns `0`, while `0 || 100` returns `100`. The `??` operator preserves zero, `false`, and empty strings.

Optional chaining only protects the part of the chain where it is used; it is not a general error catcher.

## 11. Object destructuring

```javascript
const student = { name: "Alice", age: 20, score: 85 };
const { name, score } = student;
console.log(name, score); // Alice 85
```

`{ name, score }` is a **binding pattern**, not an object being created. It retrieves `student.name` and `student.score` into local variables.

**Rename a variable:**

```javascript
const { name: studentName } = student;
console.log(studentName); // Alice
```

`name` is the source property; `studentName` is the local variable.

**Default value:**

```javascript
const product = { name: "Laptop" };
const { quantity = 1 } = product;
console.log(quantity); // 1
```

The default is used when the property value is `undefined`, not when it is `null`.

**Nested destructuring:**

```javascript
const record = { address: { city: "Nairobi" } };
const { address: { city } } = record;
console.log(city); // Nairobi
```

This binds `city`; it does not also bind a variable named `address`.

## 12. Property shorthand

```javascript
const name = "Laptop";
const price = 50000;
const product = { name, price };
console.log(product); // { name: 'Laptop', price: 50000 }
```

`{ name, price }` in an object literal is shorthand for `{ name: name, price: price }`.

## 13. Computed property names

```javascript
const key = "score";
const student = { name: "Alice", [key]: 85 };
console.log(student.score); // 85
```

`[key]` evaluates the variable `key` and uses its value (`"score"`) as the property name. Without brackets, `key: 85` would create a property literally named `"key"`.

## 14. Object.keys, Object.values, and Object.entries

```javascript
const product = { name: "Laptop", price: 50000, quantity: 3 };
console.log(Object.keys(product));
// ['name', 'price', 'quantity']
console.log(Object.values(product));
// ['Laptop', 50000, 3]
console.log(Object.entries(product));
// [['name', 'Laptop'], ['price', 50000], ['quantity', 3]]
```

These methods work with **own, enumerable, string-keyed** properties. They omit symbol keys and inherited properties. Enumeration order is specified by JavaScript rules; integer-index-like keys are ordered differently from ordinary string keys.

```javascript
for (const [key, value] of Object.entries(product)) {
  console.log(key, value);
}
// name Laptop
// price 50000
// quantity 3
```

**Trace:** `Object.entries` makes pairs; `for...of` visits each pair; `[key, value]` destructures the pair; `console.log` prints both values.

## 15. Iterating with for...in

```javascript
const student = { name: "Alice", age: 20, score: 85 };
for (const key in student) {
  if (Object.hasOwn(student, key)) {
    console.log(key, student[key]);
  }
}
```

Output:

```text
name Alice
age 20
score 85
```

`for...in` iterates enumerable string keys, including inherited enumerable keys. `Object.hasOwn` filters inherited properties. `student[key]` uses the **current value** of the variable `key`; `student.key` would look for a literal property named `"key"`.

## 16. Object references and equality

```javascript
const a = { score: 85 };
const b = { score: 85 };
const c = a;
console.log(a === b); // false
console.log(a === c); // true
c.score = 95;
console.log(a.score); // 95
```

**Why?** `a` and `b` refer to distinct objects. `c = a` copies the reference value, so `c` and `a` refer to the same object. Strict equality for objects compares identity, not structural content.

## 17. Shallow and deep copying

```javascript
const original = { name: "Alice", address: { city: "Nairobi" } };
const shallow = { ...original };
shallow.address.city = "Mombasa";
console.log(original.address.city); // Mombasa
```

`{ ...original }` creates a new **outer** object and copies its own enumerable properties. The nested `address` value remains a reference to the same nested object.

```javascript
const original = { name: "Alice", address: { city: "Nairobi" } };
const deep = structuredClone(original);
deep.address.city = "Mombasa";
console.log(original.address.city); // Nairobi
console.log(deep.address.city);     // Mombasa
```

`structuredClone` supports many structured-cloneable values, but not ordinary functions or every JavaScript object type. Another shallow-copy option is `Object.assign({}, original)`.

## 18. Object.freeze and Object.seal

```javascript
const settings = { theme: "dark" };
Object.seal(settings);
settings.theme = "light"; // Existing writable property can change
// Adding or deleting properties is prevented.
```

```javascript
const settings = { theme: "dark" };
Object.freeze(settings);
// settings.theme = "light"; // TypeError in strict mode; otherwise may fail silently
```

| Operation | Ordinary | Sealed | Frozen |
| --- | --- | --- | --- |
| Add own property | Yes | No | No |
| Delete own property | Usually | No | No |
| Change existing writable data property | Yes | Yes | No |
| Reconfigure own property | Usually | No | No |

Both `seal` and `freeze` are **shallow**: nested objects are not recursively sealed or frozen.

## 19. Getters and setters

```javascript
const rectangle = {
  width: 10,
  height: 5,
  get area() {
    return this.width * this.height;
  }
};
console.log(rectangle.area); // 50 (no parentheses)
```

`get area()` defines a getter: reading `.area` invokes its function. `this.width * this.height` multiplies `10 * 5`.

```javascript
const account = {
  _balance: 0,
  get balance() {
    return this._balance;
  },
  set balance(amount) {
    if (typeof amount !== "number" || !Number.isFinite(amount) || amount < 0) {
      throw new Error("Invalid balance");
    }
    this._balance = amount;
  }
};
account.balance = 5000;
console.log(account.balance); // 5000
```

**Operator breakdown:** `typeof amount !== "number"` checks the input type; `||` means OR; `!Number.isFinite(amount)` negates a finite-number check; `amount < 0` rejects negatives. `_balance` is a naming convention, **not** a private field.

## 20. Property descriptors

```javascript
const product = { name: "Laptop" };
console.log(Object.getOwnPropertyDescriptor(product, "name"));
// { value: 'Laptop', writable: true, enumerable: true, configurable: true }
```

- `value`: stored value.
- `writable`: whether ordinary assignment can change the value.
- `enumerable`: whether it appears in typical key enumeration.
- `configurable`: whether it can be deleted or reconfigured.

```javascript
const product = {};
Object.defineProperty(product, "id", {
  value: 101,
  writable: false,
  enumerable: true,
  configurable: false
});
console.log(product.id); // 101
```

For accessor properties, descriptors use `get` and `set` instead of `value` and `writable`.

## 21. Practical project: inventory manager

This educational example demonstrates object methods, `this`, `find`, `filter`, and `reduce`. It is not a production inventory database; additional validation and persistence would be required.

```javascript
const inventory = {
  products: [],

  addProduct(id, name, price, quantity) {
    const existing = this.products.find(product => product.id === id);
    if (existing) return false;
    this.products.push({ id, name, price, quantity });
    return true;
  },

  updatePrice(id, newPrice) {
    const product = this.products.find(item => item.id === id);
    if (!product) return false;
    product.price = newPrice;
    return true;
  },

  calculateInventoryValue() {
    return this.products.reduce(
      (total, product) => total + product.price * product.quantity,
      0
    );
  },

  findProduct(id) {
    return this.products.find(product => product.id === id);
  },

  lowStock(threshold) {
    return this.products.filter(product => product.quantity < threshold);
  }
};

inventory.addProduct(1, "Laptop", 50000, 2);
inventory.addProduct(2, "Mouse", 1500, 5);
inventory.addProduct(3, "Keyboard", 3500, 3);
console.log(inventory.calculateInventoryValue()); // 118000
console.log(inventory.findProduct(2));
// { id: 2, name: 'Mouse', price: 1500, quantity: 5 }
console.log(inventory.lowStock(4));
// [{ id: 1, name: 'Laptop', price: 50000, quantity: 2 },
//  { id: 3, name: 'Keyboard', price: 3500, quantity: 3 }]
```

### Revenue execution trace

| Iteration | Product | `price * quantity` | Previous total | New total |
| --- | --- | ---: | ---: | ---: |
| 1 | Laptop | 50000 * 2 = 100000 | 0 | 100000 |
| 2 | Mouse | 1500 * 5 = 7500 | 100000 | 107500 |
| 3 | Keyboard | 3500 * 3 = 10500 | 107500 | 118000 |

**Explain `total + product.price * product.quantity`:** property access (`product.price`, `product.quantity`) happens before multiplication; `*` has higher precedence than `+`; the product's value is added to the accumulator; the callback returns the new accumulator. The `0` after the callback is the initial accumulator.

**Explain duplicate check:** `find` returns a matching object or `undefined`. `if (existing)` is true when an object was found, and `return false` exits early without pushing a duplicate.

## 22. Ten interview questions and answers

### Q1. How do objects differ from arrays?

**Answer:** Objects organize named properties; arrays organize indexed sequences. Arrays are specialized objects with `length` and array methods. Choose based on the intended data model, not simply whether you have multiple values.

### Q2. What is the difference between dot and bracket notation?

**Answer:** `obj.name` accesses literal key `"name"`; `obj[key]` evaluates variable/expression `key`. Example: `const key = "score"; obj[key]` accesses `score`, but `obj.key` accesses a property literally named `key`.

### Q3. What is the difference between `in` and `Object.hasOwn`?

**Answer:** `in` includes inherited properties; `Object.hasOwn` only tests own properties. Example: `"toString" in {}` is typically `true`, while `Object.hasOwn({}, "toString")` is `false`.

### Q4. Why is `{a: 1} === {a: 1}` false for two separately created objects?

**Answer:** Strict equality compares object identity. Each object literal creates a different object. Structural equality requires explicit comparison logic.

### Q5. What is a shallow copy?

**Answer:** A new outer object with nested references copied, not duplicated. `const b = {...a}` creates a shallow copy; if `a.address` is an object, `b.address` still refers to the same nested object.

### Q6. What does optional chaining do?

**Answer:** `obj.address?.city` safely returns `undefined` if `address` is nullish. It does not replace missing values; combine with `?? "Unknown"` for a fallback.

### Q7. How does `this` work in methods?

**Answer:** For ordinary functions called as `obj.method()`, `this` is typically `obj`. An arrow function captures `this` lexically and does not acquire the receiver object. Extracting a method may lose its original receiver.

### Q8. What is the difference between `freeze` and `seal`?

**Answer:** `seal` prevents adding, deleting, or reconfiguring own properties while allowing updates to writable data properties. `freeze` also prevents updates to own data property values. Both are shallow.

### Q9. What is object destructuring?

**Answer:** A binding pattern extracts properties into variables: `const {name, age} = person;`. Rename with `const {name: personName} = person;`; provide defaults with `const {age = 18} = person;`.

### Q10. What is a getter?

**Answer:** A property accessor that runs a function when the property is read: `get area() { return this.width * this.height; }`. Read it as `rectangle.area`, not `rectangle.area()`.

## 23. Ten coding exercises with complete solutions

Attempt each challenge before reading its solution. All examples below are self-contained and use generic data.

### Exercise 1: Create a student object

**Task:** Create an object with `name`, `age`, `score`, `passed` and a summary method.

**Solution:**

```javascript
const student = {
  name: "Alice",
  age: 20,
  score: 85,
  passed: true,
  summary() {
    return `${this.name}, age ${this.age}, scored ${this.score}, passed: ${this.passed}`;
  }
};
console.log(student.summary());
// Alice, age 20, scored 85, passed: true
```

**Breakdown:** `summary()` defines a method; `this` refers to `student` when invoked as `student.summary()`; backticks create a template literal; each `${...}` evaluates an expression and inserts its string representation.

**Trace:** `this.name` -> `"Alice"`; `this.age` -> `20`; `this.score` -> `85`; `this.passed` -> `true`; interpolation produces the final sentence.

### Exercise 2: Dynamic property access

**Task:** Accept an object and key, returning the value or `"Not found"` if the property does not exist as an own property.

**Solution:**

```javascript
function getProperty(object, key) {
  if (!Object.hasOwn(object, key)) {
    return "Not found";
  }
  return object[key];
}
const person = { name: "Alice", age: 20 };
console.log(getProperty(person, "name"));  // Alice
console.log(getProperty(person, "score")); // Not found
```

**Breakdown:** `Object.hasOwn(object, key)` returns a boolean; `!` negates it; `if` enters the branch when the property is missing; `return` exits; `object[key]` evaluates `key` to select the property.

**Trace:** For `"name"`, hasOwn -> true, negation -> false, skip branch, return `"Alice"`. For `"score"`, hasOwn -> false, negation -> true, return `"Not found"`.

### Exercise 3: Product total

**Task:** Add a method that calculates `price * quantity`.

**Solution:**

```javascript
const product = {
  name: "Laptop",
  price: 50000,
  quantity: 3,
  total() {
    return this.price * this.quantity;
  }
};
console.log(product.total()); // 150000
```

**Breakdown:** `this.price` -> `50000`; `this.quantity` -> `3`; `*` multiplies; `return` sends back `150000`.

**Trace:** `product.total()` -> `50000 * 3` -> `150000`.

### Exercise 4: Count properties

**Task:** Count own enumerable string-keyed properties.

**Solution:**

```javascript
function countProperties(object) {
  return Object.keys(object).length;
}
console.log(countProperties({ a: 1, b: 2, c: 3 })); // 3
```

**Breakdown:** `Object.keys(object)` returns an array of keys; `.length` reads its size; `return` sends back the count.

**Trace:** Keys -> `["a", "b", "c"]`; length -> `3`.

**Edge case:** Symbol-keyed or non-enumerable properties are not counted. For **all own keys**, use `Reflect.ownKeys(object)`.

### Exercise 5: Merge two objects

**Task:** Merge without mutating inputs; later properties override earlier ones.

**Solution:**

```javascript
const first = { name: "Alice", age: 20 };
const second = { age: 21, city: "Nairobi" };
const merged = { ...first, ...second };
console.log(merged); // { name: 'Alice', age: 21, city: 'Nairobi' }
console.log(first.age); // 20
```

**Breakdown:** `...first` copies the first object's enumerable own properties into a new object; `...second` copies second's properties afterward; its `age: 21` overwrites the copied `age: 20`.

**Trace:** Start `{}` -> copy `name`, `age: 20` -> overwrite `age: 21` -> add `city`.

### Exercise 6: Safe nested city

**Task:** Return a student's city or `"Unknown"`.

**Solution:**

```javascript
function getCity(student) {
  return student.address?.city ?? "Unknown";
}
console.log(getCity({ name: "Alice", address: { city: "Nairobi" } })); // Nairobi
console.log(getCity({ name: "Bob" })); // Unknown
```

**Breakdown:** `.address` retrieves the nested value; `?.city` accesses `city` only if `address` is not nullish; `??` chooses the fallback if the result is `null` or `undefined`.

**Trace:** Alice -> address object -> `"Nairobi"` -> fallback not used. Bob -> `undefined` address -> optional access returns `undefined` -> fallback `"Unknown"`.

### Exercise 7: Group students by grade

**Task:** Use `reduce()` to group student names by grade.

**Solution:**

```javascript
const students = [
  { name: "Alice", grade: "A" },
  { name: "Bob", grade: "B" },
  { name: "Carol", grade: "A" },
  { name: "David", grade: "C" }
];
const grouped = students.reduce((groups, student) => {
  const grade = student.grade;
  if (!Object.hasOwn(groups, grade)) {
    groups[grade] = [];
  }
  groups[grade].push(student.name);
  return groups;
}, {});
console.log(grouped);
// { A: ['Alice', 'Carol'], B: ['Bob'], C: ['David'] }
```

**Breakdown:** `reduce(callback, {})` starts with an empty object accumulator; `grade` holds the current student's grade; `Object.hasOwn` tests whether a bucket exists; `groups[grade] = []` creates one when missing; `.push(student.name)` appends the name; `return groups` carries the accumulator into the next iteration.

**Trace:** Start `{}`; Alice -> `{A:['Alice']}`; Bob -> `{A:['Alice'], B:['Bob']}`; Carol -> `{A:['Alice','Carol'], B:['Bob']}`; David -> add `C:['David']`.

**Note:** For untrusted arbitrary grouping keys, a null-prototype accumulator (`Object.create(null)`) or `Map` can avoid special-property collisions.

### Exercise 8: Bank account object

**Task:** Deposit, withdraw, and read balance; reject invalid amounts and overdrafts.

**Solution:**

```javascript
function createAccount(initialBalance = 0) {
  if (!Number.isFinite(initialBalance) || initialBalance < 0) {
    throw new Error("Invalid initial balance");
  }
  let balance = initialBalance;
  return {
    deposit(amount) {
      if (!Number.isFinite(amount) || amount <= 0) return false;
      balance += amount;
      return true;
    },
    withdraw(amount) {
      if (!Number.isFinite(amount) || amount <= 0 || amount > balance) {
        return false;
      }
      balance -= amount;
      return true;
    },
    getBalance() {
      return balance;
    }
  };
}
const account = createAccount(1000);
console.log(account.deposit(500));  // true
console.log(account.withdraw(300)); // true
console.log(account.withdraw(5000)); // false
console.log(account.getBalance());  // 1200
```

**Breakdown:** `Number.isFinite` rejects non-numbers, `NaN`, and infinities; `||` combines rejection conditions; `<= 0` rejects zero and negative amounts; `> balance` blocks overdrafts; `balance += amount` is equivalent to `balance = balance + amount`; `balance -= amount` is equivalent to `balance = balance - amount`. The returned methods form a closure over `balance`.

**Trace:** Start `1000` -> deposit `500` -> `1500` -> withdraw `300` -> `1200` -> withdraw `5000` rejected -> `1200`.

**Production note:** Financial systems should use integer minor units or decimal-safe arithmetic and persist transactions with audit trails. This is a learning example, not a banking ledger.

### Exercise 9: Compare flat objects

**Task:** Compare own enumerable string-keyed keys and strictly equal values.

**Solution:**

```javascript
function equalFlatObjects(a, b) {
  if (a === null || b === null || typeof a !== "object" || typeof b !== "object") {
    return false;
  }
  const keysA = Object.keys(a);
  const keysB = Object.keys(b);
  if (keysA.length !== keysB.length) return false;
  return keysA.every(key => Object.hasOwn(b, key) && a[key] === b[key]);
}
const a = { name: "Alice", score: 85 };
const b = { name: "Alice", score: 85 };
console.log(a === b);             // false
console.log(equalFlatObjects(a, b)); // true
```

**Breakdown:** `typeof` tests operand type; `!==` means not strictly equal; `||` joins invalid-input checks; `Object.keys` extracts enumerable own string keys; `keysA.length !== keysB.length` catches different key counts; `.every` requires every key to exist in `b` and to have a strictly equal value; `&&` means both checks must pass.

**Trace:** Both have two keys; `"name"` exists and values match; `"score"` exists and values match; `.every` returns `true`.

**Limitations:** This intentionally ignores symbol and non-enumerable keys and does not recursively compare nested objects. It also treats `NaN` as unequal to `NaN`, consistent with `===`.

### Exercise 10: Advanced inventory analysis

**Task:** Calculate inventory value, units, most expensive product, greatest stock-value product, low-stock items, ID-keyed lookup, and a summary.

**Solution:**

```javascript
const products = [
  { id: 1, name: "Laptop", price: 50000, quantity: 2 },
  { id: 2, name: "Mouse", price: 1500, quantity: 5 },
  { id: 3, name: "Keyboard", price: 3500, quantity: 3 },
  { id: 4, name: "Monitor", price: 20000, quantity: 2 }
];

function analyzeInventory(items, lowStockThreshold = 4) {
  const inventoryValue = items.reduce(
    (sum, item) => sum + item.price * item.quantity, 0
  );
  const totalUnits = items.reduce((sum, item) => sum + item.quantity, 0);
  const mostExpensive = items.reduce(
    (best, item) => best === null || item.price > best.price ? item : best,
    null
  );
  const highestStockValue = items.reduce(
    (best, item) => {
      if (best === null) return item;
      const itemValue = item.price * item.quantity;
      const bestValue = best.price * best.quantity;
      return itemValue > bestValue ? item : best;
    },
    null
  );
  const lowStock = items.filter(item => item.quantity < lowStockThreshold);
  const byId = Object.fromEntries(items.map(item => [item.id, item]));
  return { inventoryValue, totalUnits, mostExpensive, highestStockValue, lowStock, byId };
}

const summary = analyzeInventory(products);
console.log(summary.inventoryValue);      // 158000
console.log(summary.totalUnits);          // 12
console.log(summary.mostExpensive.name);  // Laptop
console.log(summary.highestStockValue.name); // Laptop
console.log(summary.lowStock.map(item => item.name));
// ['Laptop', 'Keyboard', 'Monitor']
console.log(summary.byId[2].name); // Mouse
```

**Breakdown of the advanced expressions:**

1. `items.reduce((sum, item) => sum + item.price * item.quantity, 0)` initializes `sum` at zero and adds each product's stock value. Multiplication occurs before addition.
2. `items.reduce((sum, item) => sum + item.quantity, 0)` adds quantities.
3. `best === null || item.price > best.price ? item : best` is a **ternary** expression. Its condition is `best === null || item.price > best.price`. If true, return the current `item`; otherwise, return `best`. The first item wins when `best` is initially `null`.
4. `itemValue > bestValue ? item : best` chooses whichever product has greater total stock value. Equivalent:

```javascript
if (itemValue > bestValue) {
  return item;
} else {
  return best;
}
```

5. `items.filter(item => item.quantity < lowStockThreshold)` keeps products with quantity strictly below the threshold.
6. `items.map(item => [item.id, item])` creates key-value pairs. `Object.fromEntries(...)` turns those pairs into an object keyed by the IDs (ordinary object keys become strings).
7. `return { inventoryValue, ... }` uses property shorthand to assemble the summary object.

**Full numeric trace:**

| Product | Price | Quantity | Stock value | Running value | Running units |
| --- | ---: | ---: | ---: | ---: | ---: |
| Laptop | 50000 | 2 | 100000 | 100000 | 2 |
| Mouse | 1500 | 5 | 7500 | 107500 | 7 |
| Keyboard | 3500 | 3 | 10500 | 118000 | 10 |
| Monitor | 20000 | 2 | 40000 | 158000 | 12 |

**Edge cases:** Empty input yields totals `0`, low-stock `[]`, byId `{}`, and both highest-product values `null`. Access `.name` only after checking that a product exists. Validate numeric inputs before using this in an application.

## 24. Common mistakes and debugging

### Mistake 1: Dot notation versus dynamic key

```javascript
const key = "name";
const person = { name: "Alice" };
console.log(person.key);  // undefined
console.log(person[key]); // Alice
```

**Fix:** Use brackets when a variable determines the property name.

### Mistake 2: Thinking `const` freezes objects

```javascript
const person = { name: "Alice" };
person.name = "Bob"; // Allowed
// person = {}; // Not allowed
```

**Fix:** Distinguish binding reassignment from object mutation.

### Mistake 3: Assuming spread deep-copies

```javascript
const a = { address: { city: "Nairobi" } };
const b = { ...a };
b.address.city = "Mombasa";
console.log(a.address.city); // Mombasa
```

**Fix:** Recognize shared nested references; use a suitable deep-cloning strategy if necessary.

### Mistake 4: Comparing object contents with `===`

```javascript
console.log({ a: 1 } === { a: 1 }); // false
```

**Fix:** Use a comparison function suited to your definition of equality.

### Mistake 5: Losing `this` when extracting methods

```javascript
const person = {
  name: "Alice",
  greet() { return this.name; }
};
const greet = person.greet.bind(person);
console.log(greet()); // Alice
```

**Fix:** Call as a method, bind the receiver, or design the function to receive the object explicitly.

### Mistake 6: Assuming `Object.keys` includes all keys

`Object.keys` omits symbol keys, inherited properties, and non-enumerable properties. `Reflect.ownKeys` includes all own string and symbol keys.

### Mistake 7: Confusing `??` and `||`

```javascript
console.log(0 ?? 10); // 0
console.log(0 || 10); // 10
```

**Fix:** Use `??` for nullish fallbacks, `||` for truthy/falsy logic.

## 25. Lesson summary

| Concept | Remember |
| --- | --- |
| Object literal | `{ key: value }` creates an object |
| Dot notation | `object.name` accesses a literal key |
| Bracket notation | `object[key]` evaluates the key expression |
| Methods | Functions stored on objects |
| `this` | Depends on function type and invocation |
| Optional chaining | `?.` safely handles nullish intermediates |
| Nullish coalescing | `??` supplies a nullish fallback |
| Destructuring | `const {name} = object` extracts a binding |
| Spread | `{...object}` makes a shallow copy |
| Object.keys | Own enumerable string keys |
| Object.values | Corresponding values |
| Object.entries | Key-value pairs |
| Object.hasOwn | Checks own-property existence |
| Object.freeze | Shallowly prevents own-property modifications |
| Getter/setter | Accessor functions for property reads/writes |
| Property descriptor | Controls writability, enumerability, configurability |

### Master these patterns

```javascript
const { name, price } = product;
const copy = { ...original };
for (const [key, value] of Object.entries(product)) console.log(key, value);
const city = student.address?.city ?? "Unknown";
const total = products.reduce((sum, p) => sum + p.price * p.quantity, 0);
```

Practice expanding each shorthand into a longer equivalent and tracing the values by hand.

## 26. Navigation

- **Previous:** [Lesson 11 - JavaScript Arrays and Array Methods](11-javascript-arrays-and-array-methods.md)
- **Current:** Lesson 12 - JavaScript Objects and Object Methods
- **Next:** [Lesson 13 - Object-Oriented Programming in JavaScript](13-object-oriented-programming-in-javascript.md)

---

*End of Lesson 12. Save this file as `12-javascript-objects-and-object-methods.md` in your GitHub course repository.*
