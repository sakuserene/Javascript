# Lesson 10 - Conditions Inside Loops

*JavaScript Plain Loops*

Previous: [Variable Scope in Loops](09-variable-scope-in-loops.md) | Curriculum: Plain Loops | Next: Common Loop Patterns (planned)

**Filename:** `10-conditions-inside-loops.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Conditional statements inside loops](#3-conditional-statements-inside-loops)
4. [Classifying values](#4-classifying-values)
5. [Combining conditions](#5-combining-conditions)
6. [Conditional updates](#6-conditional-updates)
7. [Loop conditions versus body conditions](#7-loop-conditions-versus-body-conditions)
8. [Practical applications](#8-practical-applications)
9. [Common mistakes and edge cases](#9-common-mistakes-and-edge-cases)
10. [Best practices](#10-best-practices)
11. [Key takeaways](#11-key-takeaways)
12. [Practice exercises](#12-practice-exercises)

## 1. Introduction

Loops allow programs to perform operations repeatedly. However, real-world programming problems rarely require exactly the same operation to be performed on every value.

A program often needs to examine each value and decide what action to take based on its properties.

For example:

- Determine whether each number is even or odd.
- Count students who passed an examination.
- Classify products according to their prices.
- Apply discounts only to eligible customers.
- Ignore invalid records.
- Update different variables depending on the current value.
- Process values that satisfy multiple conditions.

Combining loops with conditional statements allows us to implement this decision-making behavior.

Consider the following example:

```javascript
for (let i = 1; i <= 5; i++) {
  if (i % 2 === 0) {
    console.log(i, "is even");
  } else {
    console.log(i, "is odd");
  }
}
```

Output:

```text
1 is odd
2 is even
3 is odd
4 is even
5 is odd
```

The loop determines which numbers are visited. The conditional statement determines what happens to each number.

This distinction forms the foundation of many important programming patterns, including searching, filtering, counting, classification and aggregation.

This lesson covers the complete Conditions Inside Loops curriculum branch. Run each example independently.

## 2. Learning objectives

- Use if, else if, else and switch inside loops.
- Distinguish independent tests from mutually exclusive branches.
- Classify values using comparisons and compound conditions.
- Update counters, accumulators and iteration state conditionally.
- Trace decisions and state changes across iterations.

## 3. Conditional statements inside loops

### 3.1 The if statement

An `if` statement executes its body only when its condition is truthy. A false condition skips that body, not the entire loop.

```javascript
for (let i = 1; i <= 6; i++) {
  if (i % 3 === 0) {
    console.log(i);
  }
}
```

Output:

```text
3
6
```

| i | i % 3 | Condition | Action |
|---:|---:|---|---|
| 1 | 1 | false | Skip |
| 2 | 2 | false | Skip |
| 3 | 0 | true | Print |
| 4 | 1 | false | Skip |
| 5 | 2 | false | Skip |
| 6 | 0 | true | Print |

The loop executes six times even though only two values are printed.

### 3.2 The if...else statement

Use `else` when exactly one of two alternatives should execute.

```javascript
const scores = [45, 80, 50];
for (let i = 0; i < scores.length; i++) {
  if (scores[i] >= 50) {
    console.log("Pass");
  } else {
    console.log("Fail");
  }
}
```

Output:

```text
Fail
Pass
Pass
```

### 3.3 The else if chain

An `else if` chain tests alternatives in order and executes only the first matching branch. The final `else` handles unmatched values.

```javascript
const scores = [85, 70, 50, 30];
for (let i = 0; i < scores.length; i++) {
  const score = scores[i];
  if (score >= 80) {
    console.log("A");
  } else if (score >= 60) {
    console.log("B");
  } else if (score >= 50) {
    console.log("C");
  } else {
    console.log("F");
  }
}
```

Output:

```text
A
B
C
F
```

Assuming valid scores from 0 to 100, the thresholds must be tested from highest to lowest. Testing `score >= 50` first would incorrectly classify 85 as C.

### 3.4 Independent if statements

Use separate `if` statements when a value can satisfy multiple rules.

```javascript
for (let i = 1; i <= 6; i++) {
  if (i % 2 === 0) console.log(i, "divisible by 2");
  if (i % 3 === 0) console.log(i, "divisible by 3");
}
```

Output:

```text
2 divisible by 2
3 divisible by 3
4 divisible by 2
6 divisible by 2
6 divisible by 3
```

An `else if` would suppress the second classification of 6.

### 3.5 The switch statement

Use `switch` for alternatives based on discrete matching values. Case matching uses strict equality. Without a break, execution falls through to subsequent case bodies.

```javascript
const commands = ["start", "pause", "stop", "other"];
for (let i = 0; i < commands.length; i++) {
  switch (commands[i]) {
    case "start":
      console.log("Starting");
      break;
    case "pause":
      console.log("Pausing");
      break;
    case "stop":
      console.log("Stopping");
      break;
    default:
      console.log("Unknown command");
  }
}
```

Output:

```text
Starting
Pausing
Stopping
Unknown command
```

Each break exits the switch, not the enclosing loop.

## 4. Classifying values

Classification assigns values to categories based on their properties. Categories can be mutually exclusive or overlapping.

```javascript
const numbers = [-4, 0, 7, -2];
for (let i = 0; i < numbers.length; i++) {
  const n = numbers[i];
  if (n < 0) {
    console.log(n, "negative");
  } else if (n > 0) {
    console.log(n, "positive");
  } else {
    console.log(n, "zero");
  }
}
```

Output:

```text
-4 negative
0 zero
7 positive
-2 negative
```

This assumes finite numeric inputs. NaN satisfies neither comparison, so it would be misclassified as zero without validation. For integer parity, use `n % 2 === 0` for even and `n % 2 !== 0` for odd; negative odd integers can have remainder -1.

## 5. Combining conditions

### 5.1 Logical AND, OR and NOT

AND requires both tests to be truthy; OR requires at least one; NOT negates truthiness. AND and OR short-circuit from left to right.

```javascript
for (let i = 1; i <= 15; i++) {
  if (i % 2 === 0 && i % 3 === 0) {
    console.log(i);
  }
}
```

Output:

```text
6
12
```

Replacing AND with OR would print 2, 3, 4, 6, 8, 9, 10, 12, 14 and 15. Negating the combined AND condition selects values that are not divisible by both.

### 5.2 Range checks and grouping

```javascript
const values = [5, 10, 15, 20, 25];
for (let i = 0; i < values.length; i++) {
  const n = values[i];
  if (n >= 10 && n <= 20) {
    console.log(n);
  }
}
```

Output:

```text
10
15
20
```

Do not write `10 <= n <= 20`: JavaScript evaluates the first comparison to a Boolean and then compares that Boolean numerically with 20. Use parentheses to clarify mixed AND and OR expressions, such as `(n < 0 || n > 100) && Number.isFinite(n)`.

### 5.3 Guarding unsafe operations

```javascript
const names = [null, "Ada", undefined, ""];
for (let i = 0; i < names.length; i++) {
  const name = names[i];
  if (typeof name === "string" && name.length > 0) {
    console.log(name);
  }
}
```

Output:

```text
Ada
```

The type test prevents property access on null and undefined. Reversing the tests would remove that protection.

## 6. Conditional updates

### 6.1 Updating counters and accumulators

```javascript
const values = [4, -2, 7, 0, 3];
let count = 0;
let total = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) {
    count++;
    total += values[i];
  }
}
console.log(count, total);
```

Output:

```text
3 14
```

| Value | Matches? | Count after | Total after |
|---:|---|---:|---:|
| 4 | Yes | 1 | 4 |
| -2 | No | 1 | 4 |
| 7 | Yes | 2 | 11 |
| 0 | No | 2 | 11 |
| 3 | Yes | 3 | 14 |

Only matching values affect the accumulated state. The loop counter advances regardless of the classification.

### 6.2 Updating iteration state conditionally

```javascript
let position = 0;
while (position < 8) {
  console.log(position);
  if (position < 3) {
    position++;
  } else {
    position += 2;
  }
}
```

Output:

```text
0
1
2
3
5
7
```

Both branches advance the position. If a continuing branch fails to update the controlling state, the loop may never terminate.

## 7. Loop conditions versus body conditions

The loop condition determines whether repetition continues. A body condition determines which work occurs during an iteration.

```javascript
const values = [4, -1, 6];
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) console.log(values[i]);
}
```

Output:

```text
4
6
```

Moving `values[i] > 0` into the loop condition with AND would stop traversal at -1 and never visit 6. Use a body condition for selective processing and a loop condition or break for termination.

## 8. Practical applications

### 8.1 Conditional discounts

```javascript
const prices = [50, 100, 200];
let total = 0;
for (let i = 0; i < prices.length; i++) {
  let price = prices[i];
  if (price >= 100) {
    price *= 0.9;
  }
  total += price;
}
console.log(total.toFixed(2));
```

Output:

```text
320.00
```

The adjusted prices are 50, 90 and 180. This is a simplified arithmetic example; production monetary calculations need explicit rounding rules and suitable numeric representation.

### 8.2 FizzBuzz

```javascript
for (let i = 1; i <= 15; i++) {
  if (i % 15 === 0) {
    console.log("FizzBuzz");
  } else if (i % 3 === 0) {
    console.log("Fizz");
  } else if (i % 5 === 0) {
    console.log("Buzz");
  } else {
    console.log(i);
  }
}
```

Output:

```text
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
```

The overlapping case must be tested first because the branches are mutually exclusive.

## 9. Common mistakes and edge cases

- Using assignment `=` instead of strict equality `===` in a test.
- Testing broad categories before narrower categories in an else if chain.
- Using independent tests when only one category should be selected.
- Treating zero as invalid merely because it is falsy.
- Forgetting switch fall-through or confusing a switch break with a loop break.
- Skipping necessary state updates on a continuing path.
- Resetting an accumulator inside the body rather than preserving it outside.

## 10. Best practices

- Separate traversal, classification and state updates conceptually.
- Use descriptive Boolean variables for complex rules.
- Test exact boundary values and overlapping conditions.
- Validate inputs before classifying them.
- Keep branches mutually exclusive when the problem requires one category.
- Ensure every continuing path makes progress.

## 11. Key takeaways

- Body conditions select work without necessarily terminating repetition.
- An else if chain selects the first matching branch; independent if statements can select several.
- Logical operators combine tests and can guard unsafe operations.
- Conditional updates allow selective counting, accumulation and progression.
- Correct branch ordering and boundary handling are essential to reliable classification.

## 12. Practice exercises

1. Print all multiples of 4 from 1 through 50.
2. Classify integers from -5 through 5 as negative, zero or positive.
3. Count even and odd integers in an array, including negative values.
4. Classify valid scores into grades using descending thresholds.
5. Print values divisible by 3 or 5, but not both.
6. Trace independent if statements and an else if chain for values 6, 10 and 15.
7. Sum only numbers within the inclusive range 10 through 50.
8. Count nonempty strings in an array containing null and undefined.
9. Use switch to classify a sequence of traffic-light strings, including unknown values.
10. Implement FizzBuzz from 1 through 100.
11. Calculate a total with different discount rates for different price ranges.
12. Design a while loop whose step depends on whether its current counter is even or odd. Prove that it terminates for a fixed upper bound.
13. Explain why moving a filtering condition into the loop header can omit later valid values.
14. Classify an array of numeric inputs while rejecting NaN, Infinity and nonnumeric values.

---

Previous: [Variable Scope in Loops](09-variable-scope-in-loops.md) | Curriculum: Plain Loops | Next: Common Loop Patterns (planned)
