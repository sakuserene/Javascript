# Unit 1 - Lesson 8: JavaScript Loops and Iteration

**Level:** Beginner to intermediate  
**Previous:** [Lesson 7 - Conditional Statements and Decision Making](07-conditional-statements-and-decision-making.md)  
**Next:** [Lesson 9 - Functions and Function Expressions](09-functions-and-function-expressions.md)

## Table of Contents

- [Overview and objectives](#overview-and-objectives)
- [1. Loops and iterations](#1-loops-and-iterations)
- [2. The for loop](#2-the-for-loop)
- [3. For loop execution simulator](#3-for-loop-execution-simulator)
- [4. Counting backward and by increments](#4-counting-backward-and-by-increments)
- [5. The while loop](#5-the-while-loop)
- [6. The do...while loop](#6-the-dowhile-loop)
- [7. Infinite loops](#7-infinite-loops)
- [8. Break](#8-break)
- [9. Continue](#9-continue)
- [10. Break versus continue](#10-break-versus-continue)
- [11. Nested loops](#11-nested-loops)
- [12. Arrays and index boundaries](#12-arrays-and-index-boundaries)
- [13. For...of](#13-forof)
- [14. For...in](#14-forin)
- [15. Iterating through strings](#15-iterating-through-strings)
- [16. Loop variable scope](#16-loop-variable-scope)
- [17. Labeled statements](#17-labeled-statements)
- [18. Common mistakes](#18-common-mistakes)
- [19. Practical examples](#19-practical-examples)
- [20. Performance and readability](#20-performance-and-readability)
- [21. Knowledge assessment](#21-knowledge-assessment)
- [22. Assessment answers](#22-assessment-answers)
- [23. Ten coding exercises](#23-ten-coding-exercises)
- [24. Complete model solutions](#24-complete-model-solutions)
- [25. Lesson summary](#25-lesson-summary)
- [Lesson navigation](#lesson-navigation)

## Overview and objectives

A **loop** repeats a block of JavaScript statements. An **iteration** is one execution cycle of a loop. Loops let us count, search, process arrays and strings, accumulate totals, and build patterns without duplicating code.

By the end of this lesson, you should be able to explain initialization, conditions and updates; write `for`, `while` and `do...while`; use `break` and `continue`; write nested loops; iterate over arrays, strings and object properties; recognize scope, infinite loops and off-by-one errors; and solve practical problems.

```javascript
for (let number = 1; number <= 5; number++) {
  console.log(number);
}
```

Output:

```text
1
2
3
4
5
```

## 1. Loops and iterations

Without a loop, repeated output requires repeated statements:

```javascript
console.log("Hello");
console.log("Hello");
console.log("Hello");
```

A loop avoids duplication:

```javascript
for (let i = 1; i <= 3; i++) {
  console.log("Hello");
}
```

Both print `Hello` three times. A typical loop has **initialization** (starting state), **condition** (whether to continue), and **update** (progress toward termination).

```text
START
  |
  v
Initialize
  |
  v
Check condition ---- false ----> END
  |
 true
  v
Execute body
  |
  v
Update
  |
  +----> Check condition
```

| Iteration | `i` | Output |
|---|---:|---|
| 1 | 1 | 1 |
| 2 | 2 | 2 |
| 3 | 3 | 3 |

## 2. The for loop

Syntax:

```javascript
for (initialization; condition; update) {
  // Statements to repeat
}
```

| Part | Purpose |
|---|---|
| Initialization | Runs once before the first condition check |
| Condition | Checked before each iteration; a falsy result ends the loop |
| Body | Executes when the condition is truthy |
| Update | Runs after a normally completed iteration |

```javascript
for (let i = 1; i <= 5; i++) {
  console.log(i);
}
```

**Line by line:** `let i = 1` creates the counter; `i <= 5` allows values 1 through 5; `console.log(i)` prints the counter; `i++` increases it after each iteration. At `i = 6`, the condition becomes false.

| Step | `i` | `i <= 5` | Action |
|---|---:|---|---|
| 1 | 1 | true | Print 1 |
| 2 | 2 | true | Print 2 |
| 3 | 3 | true | Print 3 |
| 4 | 4 | true | Print 4 |
| 5 | 5 | true | Print 5 |
| 6 | 6 | false | Stop |

## 3. For loop execution simulator

The interactive simulator in the chat can be represented in GitHub Markdown with this self-contained program. Change `start`, `end`, and `step` to experiment:

```javascript
const start = 1;
const end = 5;
const step = 1;
let iteration = 0;

for (let i = start; i <= end; i += step) {
  iteration++;
  console.log(`Iteration ${iteration}: i = ${i}`);
}
console.log(`Completed ${iteration} iterations`);
```

Output:

```text
Iteration 1: i = 1
Iteration 2: i = 2
Iteration 3: i = 3
Iteration 4: i = 4
Iteration 5: i = 5
Completed 5 iterations
```

If `start > end` with a positive step, no iterations execute. Always use a step that moves toward termination.

## 4. Counting backward and by increments

Count backward with `i--`:

```javascript
for (let i = 5; i >= 1; i--) {
  console.log(i);
}
```

Output: `5`, `4`, `3`, `2`, `1` (one per line).

Count by two with `i += 2`:

```javascript
for (let i = 0; i <= 10; i += 2) {
  console.log(i);
}
```

Output: `0`, `2`, `4`, `6`, `8`, `10`.

Count down by two:

```javascript
for (let i = 10; i >= 0; i -= 2) {
  console.log(i);
}
```

Output: `10`, `8`, `6`, `4`, `2`, `0`.

## 5. The while loop

A `while` loop checks its condition before each iteration. Initialization and update usually appear outside its header.

```javascript
let count = 1;
while (count <= 5) {
  console.log(count);
  count++;
}
```

Output:

```text
1
2
3
4
5
```

**Explanation:** `count` starts at 1; `count <= 5` is checked; the value is printed; `count++` changes the value. The loop stops when `count` becomes 6.

Use `while` when repetition is controlled by a condition rather than a convenient predetermined counter:

```javascript
let balance = 100;
while (balance > 0) {
  console.log(balance);
  balance -= 25;
}
```

Output: `100`, `75`, `50`, `25`.

## 6. The do...while loop

A `do...while` loop checks its condition **after** the body. Its body executes at least once if reached normally.

```javascript
let count = 1;
do {
  console.log(count);
  count++;
} while (count <= 5);
```

Output: `1`, `2`, `3`, `4`, `5`.

Compare initially false conditions:

```javascript
let a = 10;
while (a <= 5) {
  console.log(a); // Never runs
  a++;
}

let b = 10;
do {
  console.log(b); // Runs once
  b++;
} while (b <= 5);
```

Output: `10`.

| Feature | `for` | `while` | `do...while` |
|---|---|---|---|
| Checks condition before body | Yes | Yes | No |
| Can run zero times | Yes | Yes | Not if reached normally |
| Typical use | Counter | Condition | Execute then test |

## 7. Infinite loops

An infinite loop fails to reach its exit condition. **Do not execute** this incorrect example:

```javascript
let count = 1;
while (count <= 5) {
  console.log(count);
  // Missing count++: count never changes.
}
```

Correct:

```javascript
let count = 1;
while (count <= 5) {
  console.log(count);
  count++;
}
```

An intentional `while (true)` loop needs a reachable exit:

```javascript
let count = 0;
while (true) {
  count++;
  if (count === 3) break;
  console.log(count);
}
```

Output:

```text
1
2
```

## 8. Break

`break` immediately terminates the nearest enclosing loop or `switch`, unless a label identifies another enclosing target.

```javascript
for (let i = 1; i <= 10; i++) {
  if (i === 5) break;
  console.log(i);
}
```

Output: `1`, `2`, `3`, `4`.

Searching an array:

```javascript
const numbers = [4, 8, 12, 16, 20];
for (let i = 0; i < numbers.length; i++) {
  if (numbers[i] === 12) {
    console.log("Found at index:", i);
    break;
  }
}
```

Output: `Found at index: 2`.

## 9. Continue

`continue` skips the rest of the current iteration, then proceeds to the next iteration according to the loop structure.

```javascript
for (let i = 1; i <= 5; i++) {
  if (i === 3) continue;
  console.log(i);
}
```

Output: `1`, `2`, `4`, `5`.

Print odd numbers only:

```javascript
for (let i = 1; i <= 10; i++) {
  if (i % 2 === 0) continue;
  console.log(i);
}
```

Output: `1`, `3`, `5`, `7`, `9`.

## 10. Break versus continue

| Statement | Behavior |
|---|---|
| `break` | Ends the loop entirely |
| `continue` | Skips the rest of the current iteration |
| Neither | Normal loop progression |

For a loop from 1 through 8 with a trigger at 4:

```javascript
for (let i = 1; i <= 8; i++) {
  if (i === 4) break;
  console.log(i);
}
// 1, 2, 3

for (let i = 1; i <= 8; i++) {
  if (i === 4) continue;
  console.log(i);
}
// 1, 2, 3, 5, 6, 7, 8
```

This reproduces the two outcomes demonstrated in the lesson's interactive control.

## 11. Nested loops

A nested loop runs inside another loop. The inner loop usually completes for each outer iteration.

```javascript
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 2; j++) {
    console.log(i, j);
  }
}
```

Output:

```text
1 1
1 2
2 1
2 2
3 1
3 2
```

```text
Outer i=1 --> inner j=1, j=2
Outer i=2 --> inner j=1, j=2
Outer i=3 --> inner j=1, j=2
```

Multiplication table:

```javascript
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    console.log(`${i} x ${j} = ${i * j}`);
  }
}
```

Output:

```text
1 x 1 = 1
1 x 2 = 2
1 x 3 = 3
2 x 1 = 2
2 x 2 = 4
2 x 3 = 6
3 x 1 = 3
3 x 2 = 6
3 x 3 = 9
```

For `n` outer iterations and `m` inner iterations per outer iteration, the inner body executes `n * m` times. For 100 by 100, that is 10,000 executions.

## 12. Arrays and index boundaries

Array indices begin at zero:

```text
Index:    0         1         2
          |         |         |
Value:  Apple     Banana    Orange
Length: 3
Last valid index: 2
```

```javascript
const fruits = ["Apple", "Banana", "Orange"];
for (let i = 0; i < fruits.length; i++) {
  console.log(fruits[i]);
}
```

Output:

```text
Apple
Banana
Orange
```

### 12.2 Why use `<` rather than `<=`?

`fruits.length` is `3`, but the last valid index is `2`. `i < fruits.length` visits indices 0, 1, and 2, then stops. Using `i <= fruits.length` visits index 3 too:

```javascript
const fruits = ["Apple", "Banana", "Orange"];
for (let i = 0; i <= fruits.length; i++) {
  console.log(fruits[i]);
}
```

Output:

```text
Apple
Banana
Orange
undefined
```

| `i` | `i < 3` | `i <= 3` | `fruits[i]` |
|---:|---|---|---|
| 0 | true | true | Apple |
| 1 | true | true | Banana |
| 2 | true | true | Orange |
| 3 | false | true | undefined |

**Rule:** Use `i < array.length` for ordinary zero-based array traversal.

## 13. For...of

`for...of` iterates values from iterable objects such as arrays and strings.

```javascript
for (const value of iterable) {
  // Process value
}
```

```javascript
const fruits = ["Apple", "Banana", "Orange"];
for (const fruit of fruits) {
  console.log(fruit);
}
```

Output: `Apple`, `Banana`, `Orange`.

**Line by line:** The array holds three strings; `for...of` supplies the next value to `fruit`; `console.log` prints that value. No index management is necessary.

Sum array values:

```javascript
const numbers = [10, 20, 30, 40];
let total = 0;
for (const number of numbers) {
  total += number;
}
console.log(total);
```

Output: `100`.

| Iteration | Value | Total |
|---|---:|---:|
| Start | - | 0 |
| 1 | 10 | 10 |
| 2 | 20 | 30 |
| 3 | 30 | 60 |
| 4 | 40 | 100 |

Strings are iterable:

```javascript
for (const character of "Hello") {
  console.log(character);
}
```

Output: `H`, `e`, `l`, `l`, `o`.

String `for...of` iterates Unicode code points; a visible character may still contain multiple code points.

## 14. For...in

`for...in` enumerates enumerable string property keys, including inherited enumerable keys.

```javascript
const person = { name: "Alice", age: 25, city: "London" };
for (const key in person) {
  console.log(key, person[key]);
}
```

Output:

```text
name Alice
age 25
city London
```

```text
person
  |-- name --> Alice
  |-- age  --> 25
  \-- city --> London
```

With arrays, `for...in` gives keys, not values:

```javascript
const colors = ["Red", "Green", "Blue"];
for (const index in colors) console.log(index);
// "0", "1", "2"

for (const color of colors) console.log(color);
// Red, Green, Blue
```

| Feature | `for...in` | `for...of` |
|---|---|---|
| Iterates | Enumerable string property keys | Iterable values |
| Typical use | Object property enumeration | Arrays and strings |
| Plain objects directly | Yes | No, unless iterable |
| Inherited keys | May include them | Depends on iterator |

For only an object's own enumerable string keys:

```javascript
const person = { name: "Alice", age: 25 };
for (const key of Object.keys(person)) {
  console.log(key, person[key]);
}
// name Alice
// age 25
```

## 15. Iterating through strings

Traditional indexed iteration:

```javascript
const word = "JavaScript";
for (let i = 0; i < word.length; i++) {
  console.log(word[i]);
}
```

Output, one per line: `J`, `a`, `v`, `a`, `S`, `c`, `r`, `i`, `p`, `t`.

Count vowels:

```javascript
const word = "education";
const vowels = "aeiou";
let count = 0;
for (const character of word) {
  if (vowels.includes(character)) count++;
}
console.log(count);
```

Output: `5`. The vowels are `e`, `u`, `a`, `i`, `o`.

Reverse a simple ASCII string:

```javascript
const word = "Hello";
let reversed = "";
for (let i = word.length - 1; i >= 0; i--) {
  reversed += word[i];
}
console.log(reversed);
```

Output: `olleH`.

```text
Original: H e l l o
Index:    0 1 2 3 4
Backward: o l l e H
```

Reversing arbitrary Unicode text correctly may require grapheme-aware handling.

## 16. Loop variable scope

`let` and `const` are block-scoped. A counter declared with `let` in a `for` header is not available afterward:

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i);
}
// console.log(i); // ReferenceError: i is not defined
```

Output: `0`, `1`, `2`.

`var` is not block-scoped:

```javascript
for (var i = 0; i < 3; i++) {
  console.log(i);
}
console.log("After loop:", i);
```

Output:

```text
0
1
2
After loop: 3
```

Prefer `let` for counters and `const` for per-iteration values that are not reassigned.

## 17. Labeled statements

A label can identify an enclosing loop for `break` or `continue`.

Labeled break:

```javascript
outerLoop:
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    if (i === 2 && j === 2) break outerLoop;
    console.log(i, j);
  }
}
```

Output:

```text
1 1
1 2
1 3
2 1
```

Labeled continue:

```javascript
outerLoop:
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    if (j === 2) continue outerLoop;
    console.log(i, j);
  }
}
```

Output:

```text
1 1
2 1
3 1
```

Use labels sparingly; excessive labels can obscure control flow.

## 18. Common mistakes

1. **Off-by-one error:** `i < 5` visits 1 through 4 when starting at 1. Use `i <= 5` to include 5.
2. **Missing update:** A `while` counter that never changes can create an infinite loop.
3. **Wrong direction:** `for (let i=5; i>=1; i++)` increments away from the stopping point and does not terminate normally.
4. **Confusing `break` and `continue`:** `break` exits; `continue` skips one iteration's remainder.
5. **Using `for...in` for array values:** It returns enumerable keys, often `"0"`, `"1"`, and so on; use `for...of` for values.
6. **Accidental semicolon:**

```javascript
for (let i = 1; i <= 3; i++); {
  console.log("Hello");
}
```

This prints `Hello` once because the semicolon is the empty loop body. Correct:

```javascript
for (let i = 1; i <= 3; i++) {
  console.log("Hello");
}
```

This prints `Hello` three times.

## 19. Practical examples

### Sum from 1 to 10

```javascript
let total = 0;
for (let i = 1; i <= 10; i++) total += i;
console.log(total); // 55
```

### Factorial

```javascript
let number = 5;
let factorial = 1;
for (let i = 1; i <= number; i++) factorial *= i;
console.log(factorial); // 120
```

The factorial of 5 is `5 * 4 * 3 * 2 * 1 = 120`; `0!` is `1`.

### Largest number

```javascript
const numbers = [15, 42, 8, 67, 23];
let largest = numbers[0];
for (const number of numbers) {
  if (number > largest) largest = number;
}
console.log(largest); // 67
```

Assumes a nonempty array.

### Count even numbers

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
let count = 0;
for (const number of numbers) {
  if (number % 2 === 0) count++;
}
console.log(count); // 4
```

### Star pattern

```javascript
for (let row = 1; row <= 5; row++) {
  let pattern = "";
  for (let column = 1; column <= row; column++) {
    pattern += "*";
  }
  console.log(pattern);
}
```

Output:

```text
*
**
***
****
*****
```

### FizzBuzz

```javascript
for (let i = 1; i <= 15; i++) {
  if (i % 3 === 0 && i % 5 === 0) {
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

## 20. Performance and readability

| Loop structure | Typical complexity |
|---|---|
| Fixed number of iterations | O(1) |
| One loop over `n` elements | O(n) |
| Two full nested loops over `n` elements | O(n^2) |
| Repeatedly halving a value | O(log n) |

These are simplified patterns; actual cost depends on operations inside each loop.

```javascript
for (let i = 0; i < 100; i++) {
  for (let j = 0; j < 100; j++) {
    // Executes 10,000 times
  }
}
```

Good practices: use meaningful names, avoid unnecessary nesting, check termination, use `break` when finished, and test empty collections and boundary values.

## 21. Knowledge assessment

Try answering before reading the key.

1. How many times does `for (let i = 1; i <= 5; i++)` execute? (A) 4 (B) 5 (C) 6 (D) infinite.
2. What does `for (let i = 0; i < 5; i += 2)` print? (A) 0,1,2,3,4 (B) 0,2,4 (C) 2,4,6 (D) 0,2,4,6.
3. Which loop checks its condition after its body? (A) for (B) while (C) do...while (D) for...of.
4. What does `break` do? (A) Skip one iteration (B) Exit the loop (C) Restart loop (D) Increment counter.
5. What does `continue` do? (A) Exit loop (B) Skip remaining statements in current iteration (C) Stop program (D) Reset counter.
6. What does `for...of` provide when iterating an array? (A) Indices (B) Values (C) undefined (D) Error.
7. What does `for...in` enumerate? (A) Array values only (B) Enumerable string property keys (C) Booleans (D) Nothing.
8. How many inner-body executions occur in a 3-by-4 nested loop? (A) 3 (B) 4 (C) 7 (D) 12.
9. What happens when a `while` condition is initially false? (A) Runs once (B) Runs five times (C) Does not execute (D) Runs forever.
10. What is the result of summing integers 1 through 4? (A) 4 (B) 6 (C) 10 (D) 15.

## 22. Assessment answers

1. **B - 5.** Counter values are 1, 2, 3, 4, and 5.
2. **B - 0, 2, 4.** Start at 0, increment by 2, and stop before 5.
3. **C - do...while.** The condition is checked after the body.
4. **B - Exit the loop.** An unlabeled `break` exits the nearest enclosing loop.
5. **B - Skip remaining statements in the current iteration.** The loop then progresses normally.
6. **B - Values.** `for...of` yields the values produced by the array iterator.
7. **B - Enumerable string property keys.** This can include inherited enumerable properties.
8. **D - 12.** Three outer iterations times four inner iterations.
9. **C - Does not execute.** `while` checks the condition first.
10. **C - 10.** `1 + 2 + 3 + 4 = 10`.

## 23. Ten coding exercises

Complete independently before viewing solutions.

1. **Count from 1 to 20:** Print every integer from 1 through 20 using `for`.
2. **Even numbers:** Print all even integers from 2 through 50.
3. **Sum of numbers:** Sum integers 1 through 100. Expected `5050`.
4. **Multiplication table:** Given `let number = 7`, print `7 x 1 = 7` through `7 x 12 = 84`.
5. **Factorial:** Given `let number = 6`, compute `6!`. Expected `720`.
6. **Total and average:** Given `[75, 80, 90, 85, 70]`, compute total `400` and average `80`.
7. **Largest and smallest:** Given `[12, 45, 7, 89, 23, 4]`, find largest `89` and smallest `4` without `Math.max()` or `Math.min()`.
8. **Reverse a string:** Reverse `"JavaScript"` to `"tpircSavaJ"` using a loop.
9. **Star pattern:** Use nested loops to print six rows containing 1 through 6 stars.
10. **Number analysis:** Given `[12, 7, 25, 18, 4, 33, 10]`, calculate total, average, largest, smallest, even count, odd count, and count greater than 15.

### Exercise submission template

```javascript
// Exercise 1
// Your solution:

// Exercise 2
// Your solution:

// Continue through Exercise 10.
```

## 24. Complete model solutions

### Solution 1 - Count from 1 to 20

```javascript
for (let i = 1; i <= 20; i++) {
  console.log(i);
}
```

Output:

```text
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
```

**Explanation:** Initialize at 1; continue while `i <= 20`; print `i`; increment by one. The final check at 21 is false.

### Solution 2 - Even numbers from 2 to 50

```javascript
for (let i = 2; i <= 50; i += 2) {
  console.log(i);
}
```

Output:

```text
2
4
6
8
10
12
14
16
18
20
22
24
26
28
30
32
34
36
38
40
42
44
46
48
50
```

**Explanation:** Starting at 2 and adding 2 ensures every printed number is even. Alternative: test `i % 2 === 0` inside a loop from 1 to 50.

### Solution 3 - Sum from 1 to 100

```javascript
let total = 0;
for (let i = 1; i <= 100; i++) {
  total += i;
}
console.log(total);
```

Output:

```text
5050
```

**Explanation:** `total` is an accumulator; `total += i` adds each integer. Check with the formula `100 * 101 / 2 = 5050`.

### Solution 4 - Multiplication table of 7

```javascript
let number = 7;
for (let i = 1; i <= 12; i++) {
  console.log(`${number} x ${i} = ${number * i}`);
}
```

Output:

```text
7 x 1 = 7
7 x 2 = 14
7 x 3 = 21
7 x 4 = 28
7 x 5 = 35
7 x 6 = 42
7 x 7 = 49
7 x 8 = 56
7 x 9 = 63
7 x 10 = 70
7 x 11 = 77
7 x 12 = 84
```

**Explanation:** `number` stays 7, `i` increases from 1 to 12, and template literals display each multiplication result.

### Solution 5 - Factorial of 6

```javascript
let number = 6;
let factorial = 1;
for (let i = 1; i <= number; i++) {
  factorial *= i;
}
console.log(factorial);
```

Output:

```text
720
```

**Explanation:** The accumulator takes the values 1, 2, 6, 24, 120, 720 as the counter increases. `6! = 1 * 2 * 3 * 4 * 5 * 6`.

### Solution 6 - Array total and average

```javascript
let scores = [75, 80, 90, 85, 70];
let total = 0;
for (const score of scores) {
  total += score;
}
let average = total / scores.length;
console.log("Total:", total);
console.log("Average:", average);
```

Output:

```text
Total: 400
Average: 80
```

**Explanation:** `for...of` visits each score. The total is 400; dividing by the five entries gives 80. For an empty array, guard against dividing by zero.

### Solution 7 - Largest and smallest values

```javascript
let numbers = [12, 45, 7, 89, 23, 4];
let largest = numbers[0];
let smallest = numbers[0];
for (const number of numbers) {
  if (number > largest) largest = number;
  if (number < smallest) smallest = number;
}
console.log("Largest:", largest);
console.log("Smallest:", smallest);
```

Output:

```text
Largest: 89
Smallest: 4
```

**Explanation:** Start both trackers at the first value, then update each whenever a larger or smaller number is encountered. Assumes a nonempty numeric array.

### Solution 8 - Reverse a string

```javascript
let word = "JavaScript";
let reversed = "";
for (let i = word.length - 1; i >= 0; i--) {
  reversed += word[i];
}
console.log(reversed);
```

Output:

```text
tpircSavaJ
```

**Explanation:** Start at the last valid index, append each character to `reversed`, and move backward to index 0. This is suitable for the ASCII string shown.

### Solution 9 - Six-row star pattern

```javascript
for (let row = 1; row <= 6; row++) {
  let pattern = "";
  for (let column = 1; column <= row; column++) {
    pattern += "*";
  }
  console.log(pattern);
}
```

Output:

```text
*
**
***
****
*****
******
```

**Explanation:** The outer loop controls the row number; the inner loop appends that many stars; the completed row is printed.

### Solution 10 - Number analysis mini-project

```javascript
let numbers = [12, 7, 25, 18, 4, 33, 10];
let total = 0;
let largest = numbers[0];
let smallest = numbers[0];
let evenCount = 0;
let oddCount = 0;
let greaterThan15 = 0;

for (const number of numbers) {
  total += number;

  if (number > largest) largest = number;
  if (number < smallest) smallest = number;

  if (number % 2 === 0) {
    evenCount++;
  } else {
    oddCount++;
  }

  if (number > 15) greaterThan15++;
}

let average = total / numbers.length;
console.log("Total:", total);
console.log("Average:", average);
console.log("Largest:", largest);
console.log("Smallest:", smallest);
console.log("Even numbers:", evenCount);
console.log("Odd numbers:", oddCount);
console.log("Numbers greater than 15:", greaterThan15);
```

Output:

```text
Total: 109
Average: 15.571428571428571
Largest: 33
Smallest: 4
Even numbers: 4
Odd numbers: 3
Numbers greater than 15: 3
```

**Line-by-line reasoning:** `total` accumulates all seven values; `largest` and `smallest` track the extremes; `% 2` classifies each integer as even or odd; `number > 15` counts 25, 18, and 33; finally, average is `109 / 7`. The program assumes a nonempty array of integers.

## 25. Lesson summary

| Concept | Key takeaway |
|---|---|
| Loop | Repeats statements |
| Iteration | One cycle |
| `for` | Counter-based iteration |
| `while` | Repeats while condition is truthy |
| `do...while` | Executes body before checking condition |
| `break` | Terminates loop |
| `continue` | Skips remaining work in current iteration |
| Nested loops | Loop inside a loop |
| `for...of` | Iterates values |
| `for...in` | Enumerates string property keys |
| Infinite loop | Does not terminate normally |
| Off-by-one error | Incorrect boundary |
| Loop scope | Visibility of declared variables |
| Labeled statements | Control an enclosing loop |

**Remember:** Choose the appropriate loop; make sure it terminates; understand `<` versus `<=`; use `break` and `continue` intentionally; prefer `for...of` for array values; beware nested-loop cost; and test edge cases.

## Lesson navigation

- **Previous:** [Unit 1 - Lesson 7: Conditional Statements and Decision Making](07-conditional-statements-and-decision-making.md)
- **Current:** Unit 1 - Lesson 8: JavaScript Loops and Iteration
- **Next:** [Unit 1 - Lesson 9: JavaScript Functions and Function Expressions](09-functions-and-function-expressions.md)

[Back to Table of Contents](#table-of-contents)
