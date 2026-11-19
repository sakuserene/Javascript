# Unit 1 - Lesson 5: JavaScript Numbers and Math Methods

**Level:** Beginner to intermediate  
**Previous:** [Lesson 4 - Strings and String Methods](04-strings-and-string-methods.md)  
**Next:** [Lesson 6 - Booleans, Truthy and Falsy Values, and Logical Expressions](06-booleans-truthy-falsy-and-logical-expressions.md)

## Table of Contents

1. [Lesson overview and objectives](#lesson-overview-and-objectives)
2. [Understanding numbers](#1-understanding-numbers)
3. [Creating numbers](#2-creating-numbers)
4. [Number systems](#3-number-systems)
5. [Arithmetic operations](#4-arithmetic-operations)
6. [Number methods and validation](#5-number-methods-and-validation)
7. [BigInt](#6-bigint)
8. [toFixed](#7-tofixed)
9. [toPrecision](#8-toprecision)
10. [The Math object](#9-the-math-object)
11. [Math.round](#10-mathround)
12. [Math.floor](#11-mathfloor)
13. [Math.ceil](#12-mathceil)
14. [Math.trunc](#13-mathtrunc)
15. [Math.abs](#14-mathabs)
16. [Math.min and Math.max](#15-mathmin-and-mathmax)
17. [Math.pow and exponentiation](#16-mathpow-and-exponentiation)
18. [Math.sqrt and Math.cbrt](#17-mathsqrt-and-mathcbrt)
19. [Math.sign](#18-mathsign)
20. [Math.random](#19-mathrandom)
21. [Math constants](#20-math-constants)
22. [Floating-point precision](#21-floating-point-precision)
23. [Number conversion and parsing](#22-number-conversion-and-parsing)
24. [Intl.NumberFormat](#23-intlnumberformat)
25. [Practical examples](#24-practical-examples)
26. [Common mistakes](#25-common-mistakes)
27. [Knowledge assessment](#26-knowledge-assessment)
28. [Assessment answer key](#assessment-answer-key)
29. [Coding exercises](#27-coding-exercises)
30. [Model solutions](#model-solutions)
31. [Submitting your solutions](#28-submitting-your-solutions)
32. [Lesson summary](#29-lesson-summary)
33. [Lesson navigation](#lesson-navigation)

---

## Lesson overview and objectives

This lesson explains JavaScript's `number` primitive, numeric formats, arithmetic, rounding, mathematical methods, random values, and the important limits of floating-point precision.

```javascript
let price = 125.75;
let quantity = 4;
let total = price * quantity;
console.log(total);           // 503
console.log(Math.round(total)); // 503
console.log(total.toFixed(2)); // "503.00" (a string)
```

By the end you should be able to explain the Number and BigInt types; understand IEEE 754; use decimal, binary, octal, hexadecimal and scientific notation; validate numbers; round and format values; calculate powers, roots and random integers; recognize floating-point precision issues; and apply numeric methods in realistic calculations.

## 1. Understanding numbers

JavaScript uses the `number` primitive type for ordinary integers, fractions, negative numbers, zero, `Infinity`, `-Infinity`, and `NaN`. Unlike languages with separate integer and floating-point primitive types, both `25` and `25.5` have `typeof` equal to `"number"`.

```javascript
let age = 25;
let price = 99.99;
let temperature = -5;
let percentage = 0.75;
console.log(typeof age);         // number
console.log(typeof price);       // number
console.log(typeof temperature); // number
console.log(typeof percentage);  // number
```

| Category | Example | Meaning |
|---|---|---|
| Positive integer | `25` | Whole number above zero |
| Negative integer | `-15` | Whole number below zero |
| Zero | `0` | Neither positive nor negative |
| Floating-point | `12.75` | Fractional numeric value |
| Exponential | `1.5e3` | Scientific notation |
| Positive infinity | `Infinity` | Positive infinite value |
| Negative infinity | `-Infinity` | Negative infinite value |
| Invalid numeric result | `NaN` | Not-a-Number sentinel |

### IEEE 754 double precision

Ordinary numbers are stored in a 64-bit binary floating-point format:

```text
64-bit IEEE 754 representation
|-- Sign:      1 bit
|-- Exponent: 11 bits
`-- Fraction: 52 bits

Normal numbers have 53 bits of significand precision
because the leading bit is implicit.
```

This representation supports enormous ranges but cannot represent every decimal fraction exactly.

## 2. Creating numbers

### Integers and decimals

```javascript
let students = 50;
let score = 100;
let balance = -250;
console.log(students, score, balance); // 50 100 -250

let price = 49.99;
let weight = 2.5;
let temperature = -3.75;
console.log(price, weight, temperature); // 49.99 2.5 -3.75
```

### Scientific notation

```javascript
let largeNumber = 1e6;
let smallNumber = 2.5e-3;
console.log(largeNumber); // 1000000
console.log(smallNumber); // 0.0025
```

```text
1e6    = 1 x 10^6  = 1000000
2.5e-3 = 2.5 x 10^-3 = 0.0025
```

### Numeric separators

```javascript
let population = 1_000_000;
let budget = 25_500_750;
console.log(population); // 1000000
console.log(budget);     // 25500750
```

Underscores improve source-code readability and are not part of the numeric value.

## 3. Number systems

| System | Prefix | Example | Decimal result |
|---|---|---|---|
| Decimal | None | `255` | 255 |
| Binary | `0b` | `0b1010` | 10 |
| Octal | `0o` | `0o17` | 15 |
| Hexadecimal | `0x` | `0xFF` | 255 |

```javascript
console.log(255);    // 255
console.log(0b1010); // 10
console.log(0o17);   // 15
console.log(0xFF);   // 255
```

Binary `1010` is `1*8 + 0*4 + 1*2 + 0*1 = 10`. Octal `17` is `1*8 + 7 = 15`. Hexadecimal `FF` is `15*16 + 15 = 255`.

## 4. Arithmetic operations

| Operator | Name | Example | Result |
|---|---|---|---|
| `+` | Addition | `10 + 5` | 15 |
| `-` | Subtraction | `10 - 5` | 5 |
| `*` | Multiplication | `10 * 5` | 50 |
| `/` | Division | `10 / 5` | 2 |
| `%` | Remainder | `10 % 3` | 1 |
| `**` | Exponentiation | `2 ** 3` | 8 |

### Total cost

```javascript
let price = 250;
let quantity = 4;
let total = price * quantity;
console.log(total); // 1000
```

Execution: assign 250 to `price`; assign 4 to `quantity`; multiply them; store 1000 in `total`; print 1000.

### Percentage

Formula: `percentage = (obtainedMarks / totalMarks) * 100`.

```javascript
let totalMarks = 500;
let obtainedMarks = 425;
let percentage = (obtainedMarks / totalMarks) * 100;
console.log(percentage); // 85
```

### Discount

```javascript
let originalPrice = 1000;
let discountPercent = 15;
let discount = originalPrice * discountPercent / 100;
let finalPrice = originalPrice - discount;
console.log(discount);   // 150
console.log(finalPrice); // 850
```

## 5. Number methods and validation

### Number()

```javascript
let value = "250";
let result = Number(value);
console.log(result);        // 250
console.log(typeof result); // number
```

### Number.isInteger()

```javascript
console.log(Number.isInteger(10));   // true
console.log(Number.isInteger(10.5)); // false
console.log(Number.isInteger(-5));   // true
console.log(Number.isInteger("10")); // false
```

`Number.isInteger()` does not coerce strings into numbers.

### Number.isFinite()

```javascript
console.log(Number.isFinite(100));      // true
console.log(Number.isFinite(12.5));     // true
console.log(Number.isFinite(Infinity)); // false
console.log(Number.isFinite(NaN));      // false
```

### Number.isNaN()

```javascript
console.log(Number.isNaN(NaN));     // true
console.log(Number.isNaN(100));     // false
console.log(Number.isNaN("Hello")); // false
```

### Number.isSafeInteger()

```javascript
console.log(Number.isSafeInteger(100));              // true
console.log(Number.isSafeInteger(9007199254740991)); // true
console.log(Number.isSafeInteger(9007199254740992)); // false
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991
console.log(Number.MIN_SAFE_INTEGER); // -9007199254740991
console.log(9007199254740992 === 9007199254740993); // true
```

Beyond the safe integer range, different mathematical integers may round to the same representable `number`.

## 6. BigInt

`BigInt` is a distinct primitive for exact integer arithmetic beyond the safe `number` range. Append `n` to an integer literal.

```javascript
let largeNumber = 9007199254740993n;
console.log(largeNumber);        // 9007199254740993n
console.log(typeof largeNumber); // bigint

let a = 100n;
let b = 25n;
console.log(a + b); // 125n
console.log(a - b); // 75n
console.log(a * b); // 2500n
```

Ordinary arithmetic cannot mix BigInt and Number:

```javascript
let a = 10n;
let b = 5;
// console.log(a + b); // TypeError
console.log(a + BigInt(b)); // 15n
console.log(7n / 2n);       // 3n (truncates toward zero)
```

BigInt does not represent fractional numbers.

## 7. toFixed()

`toFixed(digits)` formats a number with a fixed number of decimal places and returns a **string**.

```javascript
let price = 12.3456;
console.log(price.toFixed(2)); // "12.35"
let number = 12.3456;
console.log(number.toFixed(0)); // "12"
console.log(number.toFixed(1)); // "12.3"
console.log(number.toFixed(2)); // "12.35"
console.log(number.toFixed(3)); // "12.346"
```

```javascript
let amount = 12.5;
let formatted = amount.toFixed(2);
console.log(formatted);        // "12.50"
console.log(typeof formatted); // string
let numericValue = Number(formatted);
console.log(numericValue);        // 12.5
console.log(typeof numericValue); // number
```

Because the underlying number is binary floating-point, decimal rounding may be surprising:

```javascript
console.log((1.005).toFixed(2)); // "1.00"
```

Do not treat `toFixed()` as a complete financial arithmetic solution.

## 8. toPrecision()

`toPrecision(digits)` formats a number to a given total number of **significant digits** and returns a string.

```javascript
let number = 123.456;
console.log(number.toPrecision(3)); // "123"
console.log(number.toPrecision(4)); // "123.5"
console.log(number.toPrecision(5)); // "123.46"
console.log(number.toFixed(2));     // "123.46"
console.log(number.toPrecision(2)); // "1.2e+2"
```

`toFixed(2)` means two digits after the decimal point; `toPrecision(2)` means two significant digits overall.

## 9. The Math object

`Math` is a built-in object of mathematical methods and constants. It is not instantiated with `new Math()`.

```javascript
console.log(Math.sqrt(25)); // 5
```

| Method | Purpose |
|---|---|
| `Math.round()` | Nearest integer; ties toward positive infinity |
| `Math.floor()` | Integer toward negative infinity |
| `Math.ceil()` | Integer toward positive infinity |
| `Math.trunc()` | Discard fractional part |
| `Math.abs()` | Absolute value |
| `Math.min()` | Minimum argument |
| `Math.max()` | Maximum argument |
| `Math.pow()` | Power |
| `Math.sqrt()` | Square root |
| `Math.cbrt()` | Cube root |
| `Math.random()` | Pseudorandom number in [0, 1) |
| `Math.sign()` | Sign of value |
| `Math.hypot()` | Euclidean norm |

## 10. Math.round()

```javascript
console.log(Math.round(4.2));  // 4
console.log(Math.round(4.5));  // 5
console.log(Math.round(4.8));  // 5
console.log(Math.round(-4.2)); // -4
console.log(Math.round(-4.5)); // -4
console.log(Math.round(-4.8)); // -5
```

At an exact halfway point, `Math.round()` chooses the integer toward positive infinity. It does not always round away from zero.

## 11. Math.floor()

`Math.floor()` returns the greatest integer less than or equal to its argument.

```javascript
console.log(Math.floor(4.9));  // 4
console.log(Math.floor(4.1));  // 4
console.log(Math.floor(4.0));  // 4
console.log(Math.floor(-4.2)); // -5
```

For negative values, moving down means moving farther from zero.

## 12. Math.ceil()

`Math.ceil()` returns the smallest integer greater than or equal to its argument.

```javascript
console.log(Math.ceil(4.1));  // 5
console.log(Math.ceil(4.9));  // 5
console.log(Math.ceil(4.0));  // 4
console.log(Math.ceil(-4.2)); // -4
```

## 13. Math.trunc()

`Math.trunc()` removes the fractional portion, moving toward zero.

```javascript
console.log(Math.trunc(4.9));  // 4
console.log(Math.trunc(4.1));  // 4
console.log(Math.trunc(-4.9)); // -4
```

### Rounding comparison

| Input | `round` | `floor` | `ceil` | `trunc` |
|---|---:|---:|---:|---:|
| `4.7` | 5 | 4 | 5 | 4 |
| `4.2` | 4 | 4 | 5 | 4 |
| `-4.7` | -5 | -5 | -4 | -4 |
| `-4.2` | -4 | -5 | -4 | -4 |

To recreate the interactive comparison in JavaScript:

```javascript
let value = -4.7;
console.log("round:", Math.round(value)); // -5
console.log("floor:", Math.floor(value)); // -5
console.log("ceil:", Math.ceil(value));   // -4
console.log("trunc:", Math.trunc(value)); // -4
```

## 14. Math.abs()

Absolute value is the distance from zero.

```javascript
console.log(Math.abs(10));    // 10
console.log(Math.abs(-10));   // 10
console.log(Math.abs(-5.75)); // 5.75
let temperature1 = 25;
let temperature2 = 18;
let difference = Math.abs(temperature1 - temperature2);
console.log(difference); // 7
```

## 15. Math.min() and Math.max()

```javascript
console.log(Math.min(10, 5, 20, 3)); // 3
console.log(Math.max(10, 5, 20, 3)); // 20
```

With an array, use spread syntax to pass individual arguments:

```javascript
let scores = [75, 90, 60, 85];
console.log(Math.max(...scores)); // 90
console.log(Math.min(...scores)); // 60
```

## 16. Math.pow() and exponentiation

```javascript
console.log(Math.pow(2, 3));  // 8
console.log(Math.pow(5, 2));  // 25
console.log(Math.pow(10, 3)); // 1000
console.log(2 ** 3);          // 8
console.log(5 ** 2);          // 25
```

```text
2^3 = 2 x 2 x 2 = 8
5^2 = 5 x 5 = 25
10^3 = 10 x 10 x 10 = 1000
```

## 17. Math.sqrt() and Math.cbrt()

```javascript
console.log(Math.sqrt(25));  // 5
console.log(Math.sqrt(81));  // 9
console.log(Math.sqrt(100)); // 10
console.log(Math.sqrt(-25)); // NaN
console.log(Math.cbrt(27));  // 3
console.log(Math.cbrt(64));  // 4
console.log(Math.cbrt(-8));  // -2
```

The ordinary `number` type does not directly represent complex square roots.

## 18. Math.sign()

```javascript
console.log(Math.sign(10));  // 1
console.log(Math.sign(-10)); // -1
console.log(Math.sign(0));   // 0
```

`Math.sign()` returns `1`, `-1`, `0`, `-0`, or `NaN` according to its argument.

```javascript
let balance = -250;
if (Math.sign(balance) === -1) {
  console.log("Negative balance"); // Negative balance
}
```

## 19. Math.random()

`Math.random()` returns a pseudorandom `number` satisfying `0 <= value < 1`.

```javascript
console.log(Math.random()); // Varies each run, e.g. 0.583729164
```

### Integers 0 through 9

```javascript
let randomNumber = Math.floor(Math.random() * 10);
console.log(randomNumber); // Any integer 0 through 9
```

Steps: generate `[0, 1)`, multiply to `[0, 10)`, floor to an integer `0..9`.

### Integers 1 through 10

```javascript
let randomNumber = Math.floor(Math.random() * 10) + 1;
console.log(randomNumber); // Any integer 1 through 10
```

### Inclusive integer range

```javascript
let min = 5;
let max = 15;
let randomNumber = Math.floor(Math.random() * (max - min + 1)) + min;
console.log(randomNumber); // Any integer 5 through 15
```

General formula: `Math.floor(Math.random() * (max - min + 1)) + min`. Suitable for ordinary small integer ranges with safe bounds, not arbitrary numeric ranges.

### Interactive example recreated as source code

```javascript
function randomInteger(min, max) {
  if (!Number.isSafeInteger(min) || !Number.isSafeInteger(max)) {
    throw new TypeError("Both bounds must be safe integers");
  }
  if (min > max || max - min >= 1_000_000) {
    throw new RangeError("Invalid or excessively large range");
  }
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
console.log(randomInteger(1, 10)); // Varies
```

**Security warning:** `Math.random()` is not cryptographically secure. Do not use it for passwords, encryption keys, authentication tokens, or other secrets. In browsers, use `crypto.getRandomValues()` for cryptographic randomness.

## 20. Math constants

```javascript
console.log(Math.PI); // 3.141592653589793
console.log(Math.E);  // 2.718281828459045
```

| Constant | Approximate value |
|---|---|
| `Math.PI` | 3.141592653589793 |
| `Math.E` | 2.718281828459045 |
| `Math.SQRT2` | 1.4142135623730951 |
| `Math.SQRT1_2` | 0.7071067811865476 |
| `Math.LN2` | 0.6931471805599453 |
| `Math.LN10` | 2.302585092994046 |
| `Math.LOG2E` | 1.4426950408889634 |
| `Math.LOG10E` | 0.4342944819032518 |

### Area of a circle

Formula: `area = PI * radius^2`.

```javascript
let radius = 5;
let area = Math.PI * radius ** 2;
console.log(area); // 78.53981633974483
```

## 21. Floating-point precision

```javascript
console.log(0.1 + 0.2);         // 0.30000000000000004
console.log(0.1 + 0.2 === 0.3); // false
```

Binary floating-point cannot represent some decimal fractions exactly. The tiny representation errors become visible in certain calculations.

### Approximate comparisons

```javascript
let a = 0.1 + 0.2;
let b = 0.3;
let approximatelyEqual = Math.abs(a - b) < Number.EPSILON;
console.log(approximatelyEqual); // true
console.log(Number.EPSILON); // 2.220446049250313e-16
```

`Number.EPSILON` is the gap between 1 and the next larger representable number. It is **not** a universal tolerance. Scale-aware tolerances may be required for values far from 1.

### Integer minor units

For fixed-decimal amounts, storing integer minor units can reduce common floating-point problems:

```javascript
let amount1 = 10; // cents
let amount2 = 20; // cents
let totalCents = amount1 + amount2;
console.log(totalCents);       // 30
console.log(totalCents / 100); // 0.3
```

Production financial applications must also account for currency-specific minor units, rounding policies, safe integer limits, and possibly decimal arithmetic libraries.

## 22. Number conversion and parsing

```javascript
console.log(Number("25"));    // 25
console.log(Number("25.5"));  // 25.5
console.log(Number(""));      // 0
console.log(Number("Hello")); // NaN

console.log(parseInt("25.75", 10)); // 25
console.log(parseInt("100px", 10)); // 100
console.log(parseFloat("25.75"));  // 25.75
console.log(parseFloat("12.5px")); // 12.5
```

| Expression | Result |
|---|---|
| `Number("25.75")` | `25.75` |
| `parseInt("25.75", 10)` | `25` |
| `parseFloat("25.75")` | `25.75` |
| `Number("25px")` | `NaN` |
| `parseInt("25px", 10)` | `25` |
| `parseFloat("25.5px")` | `25.5` |

`Number()` expects a complete numeric representation. Parsing functions accept a valid numeric prefix.

## 23. Intl.NumberFormat

`Intl.NumberFormat` formats numbers for locale-aware display without changing the internal numeric representation.

```javascript
let number = 1234567.89;
let formatter = new Intl.NumberFormat("en-US");
console.log(formatter.format(number)); // 1,234,567.89
```

### Currency

```javascript
let amount = 1250.5;
let formatter = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});
console.log(formatter.format(amount)); // $1,250.50
```

### Percentage

```javascript
let percentage = 0.85;
let formatter = new Intl.NumberFormat("en-US", {
  style: "percent",
  minimumFractionDigits: 0
});
console.log(formatter.format(percentage)); // 85%
```

## 24. Practical examples

### Average score

```javascript
let score1 = 75;
let score2 = 85;
let score3 = 90;
let average = (score1 + score2 + score3) / 3;
console.log(average);            // 83.33333333333333
console.log(average.toFixed(2)); // "83.33"
```

### Compound growth

Formula: `A = P * (1 + r)^t`, where `P` is initial amount, `r` is rate per period, and `t` is number of periods.

```javascript
let initialAmount = 1000;
let growthRate = 0.05;
let years = 3;
let finalAmount = initialAmount * (1 + growthRate) ** years;
console.log(finalAmount.toFixed(2)); // "1157.63"
```

### Distance between two points

Formula: `d = sqrt((x2-x1)^2 + (y2-y1)^2)`.

```javascript
let x1 = 0;
let y1 = 0;
let x2 = 3;
let y2 = 4;
let distance = Math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2);
console.log(distance); // 5
console.log(Math.hypot(x2 - x1, y2 - y1)); // 5
```

## 25. Common mistakes

**Mistake 1: Assuming `toFixed()` returns a number.**

```javascript
let value = 12.345;
let result = value.toFixed(2);
console.log(typeof result); // string
```

**Mistake 2: Confusing `floor()` with `trunc()` for negatives.**

```javascript
console.log(Math.floor(-4.7)); // -5
console.log(Math.trunc(-4.7)); // -4
```

**Mistake 3: Assuming decimal floating-point calculations are exact.**

```javascript
console.log(0.1 + 0.2 === 0.3); // false
```

**Mistake 4: Using `Math.random()` for security.** Use cryptographically secure random APIs instead.

**Mistake 5: Mixing BigInt and Number in arithmetic.**

```javascript
// console.log(10n + 5); // TypeError
console.log(10n + BigInt(5)); // 15n
```

**Mistake 6: Assuming `parseInt()` validates the whole string.**

```javascript
console.log(parseInt("100abc", 10)); // 100
```

## 26. Knowledge assessment

Try these questions before opening the answer key. These are the 10 questions from the interactive lesson, reproduced for GitHub.

**Question 1.** What is the output?

```javascript
console.log(typeof 25.5);
```

A. `float`  
B. `decimal`  
C. `number`  
D. `object`

**Question 2.** What is printed?

```javascript
console.log(Math.floor(-4.2));
```

A. `-4`  
B. `-5`  
C. `4`  
D. `5`

**Question 3.** What is printed?

```javascript
console.log(Math.ceil(4.2));
```

A. `4`  
B. `5`  
C. `4.2`  
D. `undefined`

**Question 4.** What is the output?

```javascript
console.log(Math.trunc(-4.9));
```

A. `-5`  
B. `-4`  
C. `5`  
D. `4`

**Question 5.** What does this return?

```javascript
console.log(Number.isInteger(10.5));
```

A. `true`  
B. `false`  
C. `NaN`  
D. `undefined`

**Question 6.** What is the result?

```javascript
console.log(Math.pow(2, 4));
```

A. `6`  
B. `8`  
C. `16`  
D. `24`

**Question 7.** What is the output?

```javascript
console.log(Math.sqrt(81));
```

A. `8`  
B. `9`  
C. `10`  
D. `81`

**Question 8.** What type is returned?

```javascript
let result = (12.5).toFixed(2);
console.log(typeof result);
```

A. `number`  
B. `string`  
C. `bigint`  
D. `object`

**Question 9.** What is printed?

```javascript
console.log(0.1 + 0.2 === 0.3);
```

A. `true`  
B. `false`  
C. `NaN`  
D. `Error`

**Question 10.** What is the output?

```javascript
console.log(Math.max(10, 25, 5));
```

A. `5`  
B. `10`  
C. `25`  
D. `undefined`

### Assessment answer key

| Question | Answer | Explanation |
|---|---|---|
| 1 | C - `number` | Ordinary integers and fractions share JavaScript's `number` type. |
| 2 | B - `-5` | `Math.floor()` rounds toward negative infinity. |
| 3 | B - `5` | `Math.ceil()` rounds toward positive infinity. |
| 4 | B - `-4` | `Math.trunc()` removes the fractional portion, toward zero. |
| 5 | B - `false` | `10.5` is not an integer. |
| 6 | C - `16` | `2 * 2 * 2 * 2 = 16`. |
| 7 | B - `9` | `9 * 9 = 81`. |
| 8 | B - `string` | `toFixed()` returns formatted text. |
| 9 | B - `false` | Binary floating-point approximations cause a tiny difference. |
| 10 | C - `25` | `Math.max()` returns the largest supplied argument. |

## 27. Coding exercises

Complete these independently before reading the model solutions.

### Exercise 1 - Basic calculations

```javascript
let a = 25;
let b = 4;
```

Calculate and print sum, difference, product, quotient, remainder, and exponentiation.

### Exercise 2 - Rounding methods

```javascript
let number = -7.6;
```

Use `Math.round()`, `Math.floor()`, `Math.ceil()`, and `Math.trunc()`. Predict and explain the results.

### Exercise 3 - Number validation

```javascript
let values = [10, 10.5, NaN, Infinity, "20"];
```

Check each value with `Number.isInteger()` and `Number.isFinite()`. Explain the results.

### Exercise 4 - Minimum and maximum

```javascript
let scores = [75, 90, 60, 85, 95];
```

Find the smallest and largest values with `Math.min()` and `Math.max()`.

### Exercise 5 - Random number generator

Generate a random integer from 1 through 6, simulating a dice roll. Explain each part of the formula.

### Exercise 6 - Circle calculator

```javascript
let radius = 7;
```

Calculate area (`PI * r^2`) and circumference (`2 * PI * r`). Display both to two decimal places.

### Exercise 7 - Number formatting

```javascript
let number = 12345.6789;
```

Display with `toFixed(2)`, `toPrecision(5)`, and `Intl.NumberFormat("en-US")`. Explain differences.

### Exercise 8 - Floating-point precision

Demonstrate why `0.1 + 0.2 === 0.3` is false, then compare with a suitable tolerance for these values.

### Exercise 9 - BigInt

```javascript
let a = 9007199254740993n;
let b = 10n;
```

Calculate sum, difference, and product. Explain BigInt's usefulness.

### Exercise 10 - Mini project: Shopping total calculator

```javascript
let price = 250;
let quantity = 5;
```

Calculate original total, apply a 15% discount if total is at least 1000, calculate final total, and print all three values to two decimal places.

Expected:

```text
Original total: 1250.00
Discount: 187.50
Final total: 1062.50
```

## Model solutions

These are reference solutions, **not graded submissions**. Compare them with your own answers and explain any differences.

### Solution 1 - Basic calculations

```javascript
let a = 25;
let b = 4;
console.log("Sum:", a + b);            // Sum: 29
console.log("Difference:", a - b);     // Difference: 21
console.log("Product:", a * b);        // Product: 100
console.log("Quotient:", a / b);       // Quotient: 6.25
console.log("Remainder:", a % b);      // Remainder: 1
console.log("Exponent:", a ** b);     // Exponent: 390625
```

**Explanation:** `+`, `-`, `*`, `/`, `%`, and `**` implement the requested arithmetic. The remainder of 25 divided by 4 is 1. Exponentiation is `25 * 25 * 25 * 25 = 390625`.

### Solution 2 - Rounding methods

```javascript
let number = -7.6;
console.log(Math.round(number)); // -8
console.log(Math.floor(number)); // -8
console.log(Math.ceil(number));  // -7
console.log(Math.trunc(number)); // -7
```

**Explanation:** The nearest integer to -7.6 is -8. The greatest integer less than or equal to -7.6 is -8. The smallest integer greater than or equal to -7.6 is -7. Truncation discards `.6`, leaving -7.

### Solution 3 - Number validation

```javascript
let values = [10, 10.5, NaN, Infinity, "20"];
for (const value of values) {
  console.log(value, {
    isInteger: Number.isInteger(value),
    isFinite: Number.isFinite(value)
  });
}
```

| Value | isInteger | isFinite | Explanation |
|---|---|---|---|
| `10` | true | true | Finite whole number |
| `10.5` | false | true | Finite fraction |
| `NaN` | false | false | Invalid numeric value |
| `Infinity` | false | false | Infinite value |
| `"20"` | false | false | String; methods do not coerce |

The `for...of` loop visits each array element. The object literal groups the two test results for printing. You will study loops and objects in later lessons.

### Solution 4 - Minimum and maximum

```javascript
let scores = [75, 90, 60, 85, 95];
let minimum = Math.min(...scores);
let maximum = Math.max(...scores);
console.log(minimum); // 60
console.log(maximum); // 95
```

**Explanation:** `...scores` spreads the five array elements into arguments. `Math.min()` chooses 60; `Math.max()` chooses 95.

### Solution 5 - Dice roll

```javascript
let diceRoll = Math.floor(Math.random() * 6) + 1;
console.log(diceRoll); // Random integer 1 through 6
```

**Explanation:** `Math.random()` returns `[0, 1)`. Multiplying by 6 produces `[0, 6)`. `Math.floor()` produces 0 through 5. Adding 1 shifts the possible outcomes to 1 through 6. Each execution may produce a different value.

### Solution 6 - Circle calculator

```javascript
let radius = 7;
let area = Math.PI * radius ** 2;
let circumference = 2 * Math.PI * radius;
console.log("Area:", area.toFixed(2));
console.log("Circumference:", circumference.toFixed(2));
```

Output:

```text
Area: 153.94
Circumference: 43.98
```

**Explanation:** Squaring 7 gives 49. Multiplying by pi gives approximately 153.938, formatted as 153.94. Twice pi times 7 gives approximately 43.982, formatted as 43.98. Both `toFixed(2)` results are strings.

### Solution 7 - Number formatting

```javascript
let number = 12345.6789;
console.log(number.toFixed(2)); // "12345.68"
console.log(number.toPrecision(5)); // "12346"
console.log(new Intl.NumberFormat("en-US").format(number)); // "12,345.679"
```

**Explanation:** `toFixed(2)` keeps two fractional digits; `toPrecision(5)` keeps five significant digits; `Intl.NumberFormat` uses locale-aware grouping and its default decimal-digit settings. All displayed results are strings.

### Solution 8 - Floating-point precision

```javascript
let calculated = 0.1 + 0.2;
let expected = 0.3;
console.log(calculated);             // 0.30000000000000004
console.log(calculated === expected); // false
let tolerance = 1e-12;
console.log(Math.abs(calculated - expected) < tolerance); // true
```

**Explanation:** Decimal 0.1 and 0.2 are approximated in binary. Their sum differs from 0.3 by a very small amount. For this example, the absolute error is much smaller than `1e-12`. The tolerance must be chosen for the scale and precision requirements of the actual application.

### Solution 9 - BigInt

```javascript
let a = 9007199254740993n;
let b = 10n;
console.log(a + b); // 9007199254741003n
console.log(a - b); // 9007199254740983n
console.log(a * b); // 90071992547409930n
```

**Explanation:** `BigInt` preserves exact integer values beyond `Number.MAX_SAFE_INTEGER`. Both operands are BigInts, so arithmetic succeeds without mixing numeric types.

### Solution 10 - Shopping total calculator

```javascript
let price = 250;
let quantity = 5;
let originalTotal = price * quantity;
let discountRate = originalTotal >= 1000 ? 0.15 : 0;
let discount = originalTotal * discountRate;
let finalTotal = originalTotal - discount;
console.log(`Original total: ${originalTotal.toFixed(2)}`);
console.log(`Discount: ${discount.toFixed(2)}`);
console.log(`Final total: ${finalTotal.toFixed(2)}`);
```

Output:

```text
Original total: 1250.00
Discount: 187.50
Final total: 1062.50
```

**Step-by-step:**

1. `price * quantity` gives `250 * 5 = 1250`.
2. Since `1250 >= 1000` is true, the conditional expression chooses the rate `0.15`.
3. `1250 * 0.15` gives `187.5`.
4. `1250 - 187.5` gives `1062.5`.
5. `toFixed(2)` formats the three amounts with two decimal places.
6. Template literals insert the formatted values into the output lines.

**Extension:** Validate that price and quantity are finite, nonnegative values before calculating. For real financial applications, adopt appropriate exact decimal or integer-minor-unit calculations and defined rounding policies.

## 28. Submitting your solutions

Write your own code for all 10 exercises before comparing with the model solutions. Run the code in a browser developer console or Node.js, note any unexpected outputs, and explain how you resolved them. When requesting a review, provide the exercise number, your code, observed output, and any questions. The model solutions above are references, not a claim that your answers were assessed.

## 29. Lesson summary

| Concept | Key takeaway |
|---|---|
| `number` | Most JavaScript numeric values |
| IEEE 754 | Binary double-precision floating-point representation |
| `BigInt` | Exact large integer arithmetic |
| `Number.isInteger()` | Checks integer values |
| `Number.isFinite()` | Checks finite numbers |
| `Number.isNaN()` | Checks the NaN value |
| `Number.isSafeInteger()` | Checks the safe integer range |
| `toFixed()` | Fixed decimal places; returns string |
| `toPrecision()` | Significant digits; returns string |
| `Math.round()` | Nearest integer, ties toward positive infinity |
| `Math.floor()` | Toward negative infinity |
| `Math.ceil()` | Toward positive infinity |
| `Math.trunc()` | Removes fractional portion |
| `Math.abs()` | Absolute value |
| `Math.min()` / `Math.max()` | Smallest/largest argument |
| `Math.pow()` / `**` | Exponentiation |
| `Math.sqrt()` / `Math.cbrt()` | Square/cube root |
| `Math.random()` | Nonsecure pseudorandom values |
| `Math.PI` | Mathematical pi |
| `Number.EPSILON` | Gap between 1 and next greater number |
| `Intl.NumberFormat` | Locale-aware display formatting |

### Key principles

1. Integers and fractions usually share JavaScript's `number` type.
2. Binary floating-point is not exact for every decimal fraction.
3. `toFixed()` and `toPrecision()` return strings.
4. `floor()`, `ceil()`, `trunc()`, and `round()` differ, especially for negative inputs.
5. `BigInt` is a separate primitive and cannot be mixed directly with `number` in arithmetic.
6. `Math.random()` must not be used for security-sensitive randomness.
7. Validate user input before numeric calculations.

## Lesson navigation

- **Previous:** [Unit 1 - Lesson 4: Strings and String Methods](04-strings-and-string-methods.md)
- **Current:** Unit 1 - Lesson 5: Numbers and Math Methods
- **Next:** [Unit 1 - Lesson 6: Booleans, Truthy and Falsy Values, and Logical Expressions](06-booleans-truthy-falsy-and-logical-expressions.md)

[Back to Unit 1 index](README.md)
