# Unit 1 - Lesson 3: JavaScript Type Conversion and Type Coercion

**Level:** Beginner to intermediate fundamentals  
**Previous:** [Lesson 2 - Operators and Expressions](02-operators-and-expressions.md)  
**Next:** [Lesson 4 - JavaScript Strings and String Methods](04-strings-and-string-methods.md)

## Table of Contents

1. [Lesson overview and learning objectives](#lesson-overview-and-learning-objectives)
2. [1. Understanding JavaScript type conversion](#1-understanding-javascript-type-conversion)
3. [2. Explicit type conversion](#2-explicit-type-conversion)
4. [3. Converting values into numbers](#3-converting-values-into-numbers)
5. [4. Understanding parseInt and parseFloat](#4-understanding-parseint-and-parsefloat)
6. [5. Converting values into Booleans](#5-converting-values-into-booleans)
7. [6. Implicit type conversion](#6-implicit-type-conversion-type-coercion)
8. [7. Type coercion in comparisons](#7-type-coercion-in-comparisons)
9. [8. Understanding NaN](#8-understanding-nan)
10. [9. Understanding Infinity](#9-understanding-infinity)
11. [10. Type conversion with arrays](#10-type-conversion-with-arrays)
12. [11. Type conversion with objects](#11-type-conversion-with-objects)
13. [12. Type conversion in real-world programming](#12-type-conversion-in-real-world-programming)
14. [13. Common mistakes](#13-common-mistakes-and-how-to-avoid-them)
15. [14. Knowledge assessment](#14-knowledge-assessment)
16. [15. Practical coding exercises](#15-practical-coding-exercises)
17. [16. Submitting your solutions](#16-submitting-your-solutions)
18. [17. Lesson summary](#17-lesson-summary)
19. [Assessment answer key](#assessment-answer-key)
20. [Detailed exercise model solutions](#detailed-exercise-model-solutions)
21. [Lesson navigation](#lesson-navigation)

---

## Lesson overview and learning objectives

In Lesson 1, we learned about JavaScript data types. In Lesson 2, we explored operators and expressions. Now we will learn how JavaScript converts values from one data type to another, why these conversions sometimes happen automatically, and how to avoid unexpected results.

```javascript
console.log(10 + "5");
console.log(10 - "5");
console.log("10" == 10);
console.log("10" === 10);
```

Output:

```text
105
5
true
false
```

**Learning objectives:** Explain explicit and implicit conversion; use `String()`, `Number()`, and `Boolean()`; predict arithmetic coercion; recognize truthy and falsy values; understand equality rules; explain `NaN`, `undefined`, `null`, and `Infinity`; parse numbers with `parseInt()` and `parseFloat()`; explain array/object conversions; and debug coercion-related bugs.

---

## 1. Understanding JavaScript type conversion

**Type conversion** changes a value from one data type into another.

```javascript
let age = "25";
console.log(age);
console.log(typeof age);
```

Output:

```text
25
string
```

The quotation marks make `"25"` a string, even though it looks numeric.

```javascript
let age = "25";
let convertedAge = Number(age);
console.log(convertedAge);
console.log(typeof convertedAge);
```

Output:

```text
25
number
```

ASCII diagram:

```text
Original value
    |
    v
"25" (string)
    |
    | Number()
    v
25 (number)
    |
    v
Ready for numerical calculations
```

The conversion does not change the original variable:

```javascript
let age = "25";
let convertedAge = Number(age);
console.log(typeof age);
console.log(typeof convertedAge);
```

Output:

```text
string
number
```

There are two broad forms:

| Type | Meaning | Example |
|---|---|---|
| Explicit conversion | Programmer intentionally converts a value | `Number("25")` |
| Implicit conversion / coercion | JavaScript converts a value during an operation | `"25" - 5` |

---

## 2. Explicit type conversion

The most common functions are `String()`, `Number()`, and `Boolean()`.

### 2.1 Converting values into strings

```javascript
let age = 25;
let ageString = String(age);
console.log(ageString);
console.log(typeof ageString);
```

Output:

```text
25
string
```

**Line-by-line:** First, `age` holds the number `25`. Next, `String(age)` returns `"25"` and assigns it to `ageString`. The first `console.log()` displays its contents. The second confirms its type is `string`.

```javascript
console.log(String(true));
console.log(String(false));
console.log(typeof String(true));
```

Output:

```text
true
false
string
```

The displayed `true` and `false` are string contents, not Boolean values.

```javascript
console.log(String(null));
console.log(String(undefined));
```

Output:

```text
null
undefined
```

| Original | Expression | Result |
|---|---|---|
| `25` | `String(25)` | `"25"` |
| `true` | `String(true)` | `"true"` |
| `false` | `String(false)` | `"false"` |
| `null` | `String(null)` | `"null"` |
| `undefined` | `String(undefined)` | `"undefined"` |
| `0` | `String(0)` | `"0"` |

### 2.2 Converting with `.toString()`

```javascript
let number = 100;
let result = number.toString();
console.log(result);
console.log(typeof result);
```

Output:

```text
100
string
```

`String(null)` works and returns `"null"`. In contrast, `null.toString()` throws a `TypeError`, as does `undefined.toString()`. Prefer `String(value)` when values may be nullish.

---

## 3. Converting values into numbers

Use `Number()` for compatible numeric values.

```javascript
let price = "500";
let convertedPrice = Number(price);
console.log(convertedPrice);
console.log(typeof convertedPrice);
```

Output:

```text
500
number
```

Decimal and negative strings:

```javascript
console.log(Number("12.5"));
console.log(Number("0.75"));
console.log(Number("-20"));
```

Output:

```text
12.5
0.75
-20
```

Empty and whitespace-only strings convert to zero:

```javascript
console.log(Number(""));
console.log(Number("   "));
```

Output:

```text
0
0
```

Invalid numeric text converts to `NaN`:

```javascript
console.log(Number("Hello"));
console.log(typeof NaN);
```

Output:

```text
NaN
number
```

Boolean conversion to numbers:

```javascript
console.log(Number(true));
console.log(Number(false));
```

Output:

```text
1
0
```

Null and undefined differ:

```javascript
console.log(Number(null));
console.log(Number(undefined));
```

Output:

```text
0
NaN
```

| Input | `Number(input)` |
|---|---|
| `"25"` | `25` |
| `"12.5"` | `12.5` |
| `""` | `0` |
| `"   "` | `0` |
| `"Hello"` | `NaN` |
| `true` | `1` |
| `false` | `0` |
| `null` | `0` |
| `undefined` | `NaN` |

---

## 4. Understanding parseInt and parseFloat

### 4.1 `parseInt()`

`parseInt()` parses an integer from the beginning of a string.

```javascript
console.log(parseInt("25"));
console.log(parseInt("25.75"));
console.log(parseInt("100px"));
console.log(parseInt("px100"));
```

Output:

```text
25
25
100
NaN
```

It stops at the first character that is not part of the integer; if no valid integer prefix exists, the result is `NaN`.

Specify the radix explicitly:

```javascript
console.log(parseInt("101", 10));
console.log(parseInt("101", 2));
```

Output:

```text
101
5
```

For binary `101`, the calculation is `1 x 4 + 0 x 2 + 1 x 1 = 5`.

### 4.2 `parseFloat()`

```javascript
console.log(parseFloat("25.75"));
console.log(parseFloat("10.5px"));
console.log(parseFloat("3.14159"));
```

Output:

```text
25.75
10.5
3.14159
```

### 4.3 Comparing all three

```javascript
let value = "25.75px";
console.log(Number(value));
console.log(parseInt(value, 10));
console.log(parseFloat(value));
```

Output:

```text
NaN
25
25.75
```

| Function | Behavior |
|---|---|
| `Number()` | Converts the entire string according to numeric conversion rules |
| `parseInt()` | Parses an integer prefix |
| `parseFloat()` | Parses a floating-point prefix |

Use `Number()` when the *whole* input should be numeric. `parseInt()` and `parseFloat()` accept prefixes, so strings such as `"100invalid"` can still yield a number.

---

## 5. Converting values into Booleans

```javascript
console.log(Boolean(1));
console.log(Boolean(0));
```

Output:

```text
true
false
```

### 5.1 Truthy values

```javascript
console.log(Boolean(10));
console.log(Boolean(-5));
console.log(Boolean("Hello"));
console.log(Boolean("false"));
console.log(Boolean([]));
console.log(Boolean({}));
```

Output:

```text
true
true
true
true
true
true
```

The string `"false"` is truthy because it is nonempty. Empty arrays and ordinary objects are also truthy.

### 5.2 Falsy values

Common falsy values are `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, and `NaN`. The special legacy browser value `document.all` is also falsy.

```javascript
console.log(Boolean(""));
console.log(Boolean(0));
console.log(Boolean(null));
console.log(Boolean(undefined));
console.log(Boolean(NaN));
```

Output:

```text
false
false
false
false
false
```

### 5.3 Truthiness explorer - static GitHub reference

The original lesson includes an interactive truthiness explorer. This Markdown version preserves all its choices and explanations in a static table:

| Value | `Boolean(value)` | Explanation |
|---|---|---|
| `0` | `false` | Zero is falsy |
| `"0"` | `true` | Nonempty string |
| `""` | `false` | Empty string |
| `"false"` | `true` | Nonempty string, despite its text |
| `[]` | `true` | Arrays are objects |
| `{}` | `true` | Ordinary objects are truthy |
| `null` | `false` | Null is falsy |
| `undefined` | `false` | Undefined is falsy |
| `NaN` | `false` | NaN is falsy |

---

## 6. Implicit type conversion (type coercion)

JavaScript can convert values automatically during operations.

```javascript
console.log("10" - 5);
```

Output:

```text
5
```

Subtraction requires numeric operands, so `"10"` becomes `10`.

### 6.1 Coercion with addition

```javascript
console.log(10 + 5);
console.log("10" + 5);
console.log(10 + "5");
```

Output:

```text
15
105
105
```

When a primitive operand is a string, `+` concatenates strings.

```javascript
console.log(10 + 5 + "2");
```

Output:

```text
152
```

```text
10 + 5 + "2"
    |
    v
15 + "2"
    |
    v
"152"
```

But:

```javascript
console.log("10" + 5 + 2);
```

Output:

```text
1052
```

```text
"10" + 5 + 2
     |
     v
"105" + 2
     |
     v
"1052"
```

### 6.2 Subtraction

```javascript
console.log("20" - 5);
console.log("20" - "5");
```

Output:

```text
15
15
```

### 6.3 Multiplication

```javascript
console.log("10" * 3);
console.log("4" * "5");
```

Output:

```text
30
20
```

### 6.4 Division

```javascript
console.log("100" / 4);
console.log("20" / "5");
```

Output:

```text
25
4
```

### 6.5 Invalid numeric strings

```javascript
console.log("Hello" * 2);
console.log("Hello" - 5);
```

Output:

```text
NaN
NaN
```

| Expression | Output | Reason |
|---|---|---|
| `10 + "5"` | `"105"` | Concatenation |
| `"10" - 5` | `5` | Numeric coercion |
| `"10" * 2` | `20` | Numeric coercion |
| `"20" / 4` | `5` | Numeric coercion |
| `"10" + 2 + 3` | `"1023"` | Left-to-right concatenation |
| `10 + 2 + "3"` | `"123"` | Addition then concatenation |
| `"Hello" * 2` | `NaN` | Invalid numeric conversion |

---

## 7. Type coercion in comparisons

### 7.1 Loose equality (`==`)

```javascript
console.log(10 == "10");
console.log(false == 0);
```

Output:

```text
true
true
```

Abstract equality rules permit coercion for these comparisons.

### 7.2 Strict equality (`===`)

```javascript
console.log(10 === "10");
console.log(false === 0);
```

Output:

```text
false
false
```

### 7.3 Null and undefined

```javascript
console.log(null == undefined);
console.log(null === undefined);
console.log(null == 0);
console.log(undefined == 0);
```

Output:

```text
true
false
false
false
```

Loose equality has special nullish rules; it does not simply convert every value to a number.

### 7.4 Relational comparisons of strings

```javascript
console.log("20" > "3");
console.log("20" > 3);
```

Output:

```text
false
true
```

When both operands are strings, relational comparison uses lexicographic UTF-16 code-unit ordering; `"2"` sorts before `"3"`. With a number operand, `"20"` is converted to `20`.

| Expression | Result |
|---|---|
| `10 == "10"` | `true` |
| `10 === "10"` | `false` |
| `false == 0` | `true` |
| `false === 0` | `false` |
| `null == undefined` | `true` |
| `null === undefined` | `false` |
| `"20" > "3"` | `false` |
| `"20" > 3` | `true` |

**Best practice:** Prefer `===` and `!==`, and convert types explicitly when appropriate.

---

## 8. Understanding NaN

`NaN` represents an invalid numeric result.

```javascript
let result = Number("Hello");
console.log(result);
console.log(typeof NaN);
```

Output:

```text
NaN
number
```

`NaN` is not strictly equal to itself:

```javascript
console.log(NaN === NaN);
```

Output:

```text
false
```

### 8.1 `Number.isNaN()`

```javascript
console.log(Number.isNaN(NaN));
console.log(Number.isNaN(100));
console.log(Number.isNaN("Hello"));
console.log(Number.isNaN(Number("Hello")));
```

Output:

```text
true
false
false
true
```

`Number.isNaN()` checks the actual value without coercion.

### 8.2 `isNaN()` versus `Number.isNaN()`

```javascript
console.log(isNaN("Hello"));
console.log(Number.isNaN("Hello"));
```

Output:

```text
true
false
```

The global `isNaN()` performs numeric coercion; `Number.isNaN()` does not. Prefer `Number.isNaN()` after explicit conversion.

---

## 9. Understanding Infinity

```javascript
console.log(10 / 0);
console.log(-10 / 0);
console.log(typeof Infinity);
```

Output:

```text
Infinity
-Infinity
number
```

### 9.1 `Number.isFinite()`

```javascript
console.log(Number.isFinite(100));
console.log(Number.isFinite(Infinity));
console.log(Number.isFinite(NaN));
```

Output:

```text
true
false
false
```

Example validation:

```javascript
let result = 100 / 0;
if (Number.isFinite(result)) {
  console.log("Valid finite result");
} else {
  console.log("Invalid or infinite result");
}
```

Output:

```text
Invalid or infinite result
```

---

## 10. Type conversion with arrays

### 10.1 Arrays to strings

```javascript
console.log(String([1, 2, 3]));
console.log(String(["Alice", "Bob"]));
```

Output:

```text
1,2,3
Alice,Bob
```

### 10.2 Empty arrays

```javascript
console.log(String([]));
```

Output: an empty line, because the result is `""`.

```javascript
console.log(Number([]));
```

Output:

```text
0
```

An empty array converts to an empty string, which converts to zero.

### 10.3 Single-element arrays

```javascript
console.log(Number([5]));
```

Output:

```text
5
```

### 10.4 Multiple-element arrays

```javascript
console.log(Number([5, 10]));
```

Output:

```text
NaN
```

The string `"5,10"` is not a valid number.

| Expression | Result |
|---|---|
| `String([])` | `""` |
| `String([1, 2])` | `"1,2"` |
| `Number([])` | `0` |
| `Number([5])` | `5` |
| `Number([5, 10])` | `NaN` |
| `Boolean([])` | `true` |

**Important:** Empty arrays are truthy even though `Number([])` is zero.

---

## 11. Type conversion with objects

```javascript
let person = { name: "Alice" };
console.log(String(person));
```

Output:

```text
[object Object]
```

For an ordinary plain object, this is the default string representation.

```javascript
console.log(Number({}));
console.log(Boolean({}));
```

Output:

```text
NaN
true
```

Objects may convert to primitives using `Symbol.toPrimitive`, `valueOf()`, and `toString()`, according to the conversion context. We will study these mechanisms further in the objects unit.

```javascript
console.log([] == 0);
console.log([] === 0);
```

Output:

```text
true
false
```

Loose equality coerces the array; strict equality recognizes that an array object is not a number.

---

## 12. Type conversion in real-world programming

### 12.1 The problem: numeric inputs arrive as strings

```javascript
let firstNumber = "100";
let secondNumber = "50";
let total = firstNumber + secondNumber;
console.log(total);
```

Output:

```text
10050
```

### 12.2 The solution: explicit numeric conversion

```javascript
let firstNumber = "100";
let secondNumber = "50";
let total = Number(firstNumber) + Number(secondNumber);
console.log(total);
```

Output:

```text
150
```

**Line-by-line:** The first two lines store strings. `Number(firstNumber)` produces `100` and `Number(secondNumber)` produces `50`. The `+` operator now adds numbers to obtain `150`. The result is assigned to `total` and printed.

### 12.3 Validate converted values

```javascript
let firstInput = "100";
let secondInput = "50";
let firstNumber = Number(firstInput);
let secondNumber = Number(secondInput);

if (Number.isFinite(firstNumber) && Number.isFinite(secondNumber)) {
  let total = firstNumber + secondNumber;
  console.log(total);
} else {
  console.log("Please enter valid numbers");
}
```

Output:

```text
150
```

For real user input, also reject empty and whitespace-only strings if they are not acceptable, because `Number("")` is `0`.

---

## 13. Common mistakes and how to avoid them

### Mistake 1 - Assuming numeric strings are numbers

```javascript
let price = "500";
console.log(typeof price);
```

Output:

```text
string
```

Fix: `let price = Number("500");`

### Mistake 2 - Using `+` without checking types

```javascript
console.log("10" + 20);
```

Output:

```text
1020
```

Fix:

```javascript
console.log(Number("10") + 20);
```

Output:

```text
30
```

### Mistake 3 - Assuming `Boolean("false")` is false

```javascript
console.log(Boolean("false"));
```

Output:

```text
true
```

If a specific input protocol uses the strings `"true"` and `"false"`, compare explicitly:

```javascript
let input = "false";
let result = input === "true";
console.log(result);
```

Output:

```text
false
```

### Mistake 4 - Assuming an empty array is falsy

```javascript
console.log(Boolean([]));
```

Output:

```text
true
```

Check array length instead:

```javascript
let items = [];
console.log(items.length === 0);
```

Output:

```text
true
```

### Mistake 5 - Using `parseInt()` for full numeric validation

```javascript
console.log(parseInt("100abc", 10));
```

Output:

```text
100
```

If the whole string must be a number, use `Number()` plus validation, rather than accepting a numeric prefix.

---

## 14. Knowledge assessment

**Instructions:** Predict each result before running the code. The answer key and explanations are provided later.

### Question 1

```javascript
console.log(Number("25"));
```

A. `"25"` (string)  
B. `25` (number)  
C. `NaN`  
D. `undefined`

### Question 2

```javascript
console.log("10" + 5);
```

A. `15`  
B. `105`  
C. `NaN`  
D. Error

### Question 3

```javascript
console.log("10" - 5);
```

A. `105`  
B. `NaN`  
C. `5`  
D. Error

### Question 4

```javascript
console.log(Boolean("false"));
```

A. `false`  
B. `true`  
C. `undefined`  
D. `NaN`

### Question 5

```javascript
console.log(Number(null));
```

A. `NaN`  
B. `null`  
C. `0`  
D. `undefined`

### Question 6

```javascript
console.log(parseInt("25.75", 10));
```

A. `25.75`  
B. `25`  
C. `NaN`  
D. `26`

### Question 7

```javascript
console.log(Number([5]));
```

A. `NaN`  
B. `0`  
C. `5`  
D. `"5"`

### Question 8

```javascript
console.log(null == undefined);
```

A. `true`  
B. `false`  
C. `null`  
D. Error

### Question 9

```javascript
console.log(Number.isNaN("Hello"));
```

A. `true`  
B. `false`  
C. `NaN`  
D. Error

### Question 10

```javascript
console.log("20" > "3");
```

A. `true`  
B. `false`  
C. `NaN`  
D. Error

---

## 15. Practical coding exercises

**Try these independently before reading the model solutions.**

### Exercise 1 - String to number conversion

```javascript
let price = "250";
let quantity = "4";
```

Convert both to numbers, multiply them, and print the total.

Expected output:

```text
1000
```

### Exercise 2 - Predict the output

```javascript
console.log(10 + "5");
console.log("10" - 5);
console.log("10" * "2");
console.log("20" / 4);
console.log("Hello" * 3);
```

Explain each result.

### Exercise 3 - Boolean conversion

```javascript
console.log(Boolean(""));
console.log(Boolean("0"));
console.log(Boolean(0));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(null));
```

Explain why empty arrays and objects are truthy.

### Exercise 4 - Number parsing

```javascript
let value = "45.75px";
```

Use `Number()`, `parseInt()`, and `parseFloat()`; print and explain the results.

### Exercise 5 - Equality and coercion

```javascript
console.log(10 == "10");
console.log(10 === "10");
console.log(false == 0);
console.log(false === 0);
console.log(null == undefined);
console.log(null === undefined);
```

Explain why strict equality is generally recommended.

### Exercise 6 - Input validation

```javascript
let userInput = "150";
```

Convert to a number, check whether it is finite, and print `"Valid number"` or `"Invalid number"`. Extend the program to reject empty and whitespace-only strings.

### Exercise 7 - Array conversion

```javascript
console.log(Number([]));
console.log(Number([10]));
console.log(Number([10, 20]));
console.log(String([1, 2, 3]));
console.log(Boolean([]));
```

Predict and explain all outputs.

### Exercise 8 - Debugging

```javascript
let firstNumber = "20";
let secondNumber = "30";
let total = firstNumber + secondNumber;
console.log(total);
```

Explain why this prints `2030` and fix it to print `50`.

### Exercise 9 - Conversion challenge

```javascript
let a = "5";
let b = 2;
let result = a + b * 3;
console.log(result);
console.log(typeof result);
```

Predict the output and explain precedence and coercion.

### Exercise 10 - Mini project: Simple price calculator

Create a program that stores price and quantity as strings, converts and validates them as finite numbers greater than zero, calculates total, applies a 10% discount when total is at least `1000`, and prints original total, discount, and final total.

Starting values:

```javascript
let price = "250";
let quantity = "5";
```

Expected output:

```text
Original total: 1250
Discount: 125
Final total: 1125
```

---

## 16. Submitting your solutions

Write your code and predicted outputs for Exercises 1-10 and submit them for review. A reviewer should check correctness, explain mistakes, and suggest improvements before advancing to the next lesson. The original lesson provided a form with one answer field per exercise; GitHub Markdown cannot reproduce that interactive submission control without additional code, so the exercise prompts above are fully preserved for you to work through locally.

---

## 17. Lesson summary

| Concept | Key takeaway |
|---|---|
| Type conversion | Changing a value from one type to another |
| Explicit conversion | Programmer-requested conversion |
| Implicit coercion | JavaScript converts automatically during an operation |
| `String()` | Converts to a string |
| `Number()` | Converts compatible values to numbers |
| `Boolean()` | Converts to true or false |
| `parseInt()` | Parses an integer prefix |
| `parseFloat()` | Parses a floating-point prefix |
| `NaN` | Invalid numeric result |
| `Number.isNaN()` | Tests for actual numeric NaN |
| `Number.isFinite()` | Tests for finite numbers |
| `==` | Abstract equality with coercion rules |
| `===` | Strict equality without coercion |
| Arrays | Can convert to strings and sometimes numbers |
| Objects | Use primitive conversion rules when needed |

**Key principles:** Convert explicitly when practical; prefer strict equality; validate numeric input; remember `+` can concatenate; and distinguish empty arrays (truthy) from empty strings (falsy).

---

## Assessment answer key

These are **model answers**, not grades for any submitted work.

| Question | Correct option | Explanation |
|---|---|---|
| 1 | B | `Number("25")` returns the number `25`. |
| 2 | B | `+` concatenates because one operand is a string, producing `"105"`. |
| 3 | C | `-` converts `"10"` to numeric `10`, then subtracts `5`. |
| 4 | B | `"false"` is a nonempty string and therefore truthy. |
| 5 | C | `Number(null)` is `0`. |
| 6 | B | `parseInt()` reads the integer prefix `25`. |
| 7 | C | `[5]` converts to `"5"`, then to numeric `5`. |
| 8 | A | `null` and `undefined` are equal under abstract equality. |
| 9 | B | `Number.isNaN()` does not coerce strings; `"Hello"` is not NaN. |
| 10 | B | Two strings are compared lexicographically; `"2"` sorts before `"3"`. |

---

## Detailed exercise model solutions

These solutions are provided for independent study. Try the exercises first.

### Model solution 1 - String to number conversion

```javascript
let price = "250";
let quantity = "4";
let numericPrice = Number(price);
let numericQuantity = Number(quantity);
let total = numericPrice * numericQuantity;
console.log(total);
```

Output:

```text
1000
```

**Walkthrough:** `Number(price)` converts `"250"` into `250`; `Number(quantity)` converts `"4"` into `4`; multiplication produces `1000`; the final line prints the result. The original string variables remain unchanged.

### Model solution 2 - Predict the output

```text
105
5
20
5
NaN
```

1. `10 + "5"` concatenates the number and string, yielding `"105"`.
2. `"10" - 5` performs numeric subtraction, yielding `5`.
3. `"10" * "2"` converts both numeric strings, yielding `20`.
4. `"20" / 4` converts the numeric string, yielding `5`.
5. `"Hello" * 3` cannot convert `"Hello"` to a number, yielding `NaN`.

### Model solution 3 - Boolean conversion

```text
false
true
false
true
true
false
```

An empty string is falsy. `"0"` is nonempty and truthy. Numeric zero is falsy. Arrays and ordinary objects are truthy regardless of whether they contain properties or elements. Null is falsy.

### Model solution 4 - Number parsing

```javascript
let value = "45.75px";
console.log(Number(value));
console.log(parseInt(value, 10));
console.log(parseFloat(value));
```

Output:

```text
NaN
45
45.75
```

`Number()` rejects the nonnumeric suffix. `parseInt()` parses the integer prefix and stops at the decimal point. `parseFloat()` parses the decimal prefix and stops at `p`.

### Model solution 5 - Equality and coercion

```text
true
false
true
false
true
false
```

1. `10 == "10"` is true after abstract-equality coercion.
2. `10 === "10"` is false because number and string differ.
3. `false == 0` is true under abstract equality.
4. `false === 0` is false because Boolean and number differ.
5. `null == undefined` is true under a special abstract-equality rule.
6. `null === undefined` is false because their types differ.

**Why strict equality?** It avoids implicit type conversion and makes comparison intent clearer.

### Model solution 6 - Input validation

```javascript
let userInput = "150";
let trimmedInput = userInput.trim();

if (trimmedInput === "") {
  console.log("Invalid number");
} else {
  let value = Number(trimmedInput);

  if (Number.isFinite(value)) {
    console.log("Valid number");
  } else {
    console.log("Invalid number");
  }
}
```

Output:

```text
Valid number
```

**Walkthrough:** `.trim()` removes surrounding whitespace. An empty result is rejected before conversion (otherwise `Number("")` would be `0`). If the string is not empty, `Number()` attempts conversion. `Number.isFinite()` accepts finite numeric results and rejects `NaN` and infinities. For example, replacing `"150"` with `"   "` produces `"Invalid number"`.

### Model solution 7 - Array conversion

```text
0
10
NaN
1,2,3
true
```

- `Number([])` becomes `Number("")`, yielding `0`.
- `Number([10])` becomes `Number("10")`, yielding `10`.
- `Number([10, 20])` becomes `Number("10,20")`, yielding `NaN`.
- `String([1, 2, 3])` yields `"1,2,3"`.
- `Boolean([])` is true because arrays are objects and are truthy.

### Model solution 8 - Debugging

Original problem: both values are strings, so `+` concatenates them into `"2030"`.

Corrected code:

```javascript
let firstNumber = "20";
let secondNumber = "30";
let total = Number(firstNumber) + Number(secondNumber);
console.log(total);
```

Output:

```text
50
```

**Walkthrough:** `Number()` converts each string before `+` is applied, so JavaScript performs numeric addition instead of concatenation.

### Model solution 9 - Conversion challenge

```text
56
string
```

**Execution order:** Multiplication has higher precedence than addition, so `b * 3` evaluates first: `2 * 3 = 6`. Then `a + 6` is `"5" + 6`, which concatenates into `"56"`. `typeof "56"` is `"string"`.

### Model solution 10 - Mini project: Simple price calculator

```javascript
let price = "250";
let quantity = "5";

let numericPrice = Number(price);
let numericQuantity = Number(quantity);

let validPrice = Number.isFinite(numericPrice) && numericPrice > 0;
let validQuantity = Number.isFinite(numericQuantity) && numericQuantity > 0;

if (validPrice && validQuantity) {
  let originalTotal = numericPrice * numericQuantity;
  let discount = originalTotal >= 1000 ? originalTotal * 0.10 : 0;
  let finalTotal = originalTotal - discount;

  console.log("Original total: " + originalTotal);
  console.log("Discount: " + discount);
  console.log("Final total: " + finalTotal);
} else {
  console.log("Invalid price or quantity");
}
```

Output:

```text
Original total: 1250
Discount: 125
Final total: 1125
```

**Line-by-line reasoning:**

1. `price` and `quantity` begin as strings.
2. `Number(price)` returns `250` and `Number(quantity)` returns `5`.
3. `Number.isFinite()` verifies that neither result is `NaN` nor infinite.
4. `> 0` ensures both values are positive.
5. Both checks pass, so the `if` block runs.
6. `originalTotal = 250 * 5` produces `1250`.
7. Because `1250 >= 1000`, the ternary selects `1250 * 0.10`, producing `125`.
8. `finalTotal = 1250 - 125` produces `1125`.
9. The three `console.log()` calls display the original total, discount, and final total.

**Additional validation note:** This solution follows the exercise requirement to validate *converted numbers*. If the input must also reject blank strings, check `price.trim() !== ""` and `quantity.trim() !== ""` before converting, as demonstrated in Exercise 6.

---

## Lesson navigation

- **Previous:** [Unit 1 - Lesson 2: Operators and Expressions](02-operators-and-expressions.md)
- **Current:** Unit 1 - Lesson 3: Type Conversion and Type Coercion
- **Next:** [Unit 1 - Lesson 4: JavaScript Strings and String Methods](04-strings-and-string-methods.md)
- **Course index:** [README.md](../README.md)

**Suggested repository path:** `javascript-course/unit-01-fundamentals/03-type-conversion-and-type-coercion.md`
