
# Lesson 11 - Common Loop Patterns

*JavaScript Plain Loops*

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: [Managing State in Loops](12-managing-state-in-loops.md)

## Table of contents

1. [Understanding loop patterns](#1-understanding-loop-patterns)
2. [Repeating operations](#2-repeating-operations)
3. [Generating sequences](#3-generating-sequences)
4. [Collecting values in arrays](#4-collecting-values-in-arrays)
5. [Counting](#5-counting)
6. [Accumulation](#6-accumulation)
7. [Calculating averages](#7-calculating-averages)
8. [Finding minimum and maximum values](#8-finding-minimum-and-maximum-values)
9. [Searching](#9-searching)
10. [Boolean flags](#10-boolean-flags)
11. [Filtering](#11-filtering)
12. [Transforming values](#12-transforming-values)
13. [Partitioning](#13-partitioning)
14. [Combining loop patterns](#14-combining-loop-patterns)
15. [Recognizing and selecting loop patterns](#15-recognizing-and-selecting-loop-patterns)
16. [Summary](#16-summary)
17. [Integrated exercises](#17-integrated-exercises)

## 1. Understanding loop patterns

In the previous lessons, we learned how to repeat instructions using loops, control their execution and make decisions inside their bodies.

Now we will look at how these concepts can be used to solve actual programming problems.

Suppose we want to process the examination marks of a group of students. We might want to determine how many students passed, calculate their average mark, find the highest mark or collect all the failing marks.

All these tasks involve repetition. However, what we do during each iteration depends on the result we want to obtain.

Many programming problems share similar solution structures. For instance, calculating total expenses, total examination marks and total distance all involve repeatedly adding values to a running total.

These reusable solution structures are called **loop patterns**.

Loop patterns are not additional JavaScript syntax. They are ways of organizing the variables, conditions and operations we have already learned.

Understanding them helps us solve unfamiliar problems without having to invent every solution from scratch.

We will begin with simple repetition and gradually develop patterns for processing, collecting and analyzing values.

Run each code example independently.

## 2. Repeating operations

Sometimes the operation itself is the desired result. To print three numbered tickets, we need three iterations, but the ticket numbers should begin at one.

```javascript
for (let i = 0; i < 3; i++) {
  console.log("Ticket", i + 1);
}
```

Output:

```text
Ticket 1
Ticket 2
Ticket 3
```

The expression `i + 1` changes the displayed number, not the counter. Using `i <= 3` would print four tickets. A `for` loop is convenient when the repetition count is known; a `while` loop can do the same job. A `do...while` loop is unsuitable if zero repetitions must be possible.

### Practice

1. Print "Welcome" five times. Expected: five identical lines.
2. Print numbered receipts for quantity 3. Expected: Receipt 1, Receipt 2, Receipt 3 (one per line). Test quantity 0: no lines.
3. Count down from 4 to 1, then print "Finished". Expected: 4, 3, 2, 1, Finished.
4. Rewrite receipt printing using while. Test quantities 0, 1 and 5; expect exactly that many receipts.

## 3. Generating sequences

A sequence is an ordered series of values, called terms. Instead of repeating an identical message, we can calculate a different term each time.

### Arithmetic sequences

Suppose we need 3, 7, 11, 15, 19. Each term increases by four. A sequence with a constant difference is called an arithmetic sequence.

```javascript
for (let i = 0; i < 5; i++) {
  console.log(3 + 4 * i);
}
```

Output:

```text
3
7
11
15
19
```

The index counts how many increments of four are needed. Alternatively, remember the current term and update it after printing.

```javascript
let term = 3;
for (let i = 0; i < 5; i++) {
  console.log(term);
  term += 4;
}
```

This produces the same output. The formula calculates each term independently; the second approach derives each term from the preceding one.

### Geometric sequences

If the next term is obtained by multiplying by a constant rather than adding, the sequence is geometric.

```javascript
let term = 2;
for (let i = 0; i < 4; i++) {
  console.log(term);
  term *= 3;
}
```

Output:

```text
2
6
18
54
```

| Iteration | Printed | After update |
|---|---|---|
| 1 | 2 | 6 |
| 2 | 6 | 18 |
| 3 | 18 | 54 |
| 4 | 54 | 162 |

Printing before updating includes the initial term. If we update first, the sequence starts at six. When using a threshold instead of a fixed count, ensure that the update moves toward termination: multiplying zero by three never increases it.

### Generating decreasing sequences

```javascript
let term = 20;
for (let i = 0; i < 5; i++) {
  console.log(term);
  term -= 3;
}
```

Output: 20, 17, 14, 11, 8 (on separate lines).

### Generating terms until a boundary is reached

```javascript
let term = 1;
while (term <= 100) {
  console.log(term);
  term *= 2;
}
```

Output: 1, 2, 4, 8, 16, 32, 64 (on separate lines). Starting at zero would prevent termination because zero multiplied by two remains zero.

### Practice

1. Generate ten positive multiples of 7. Expected: 7, 14, 21, 28, 35, 42, 49, 56, 63, 70.
2. Generate five terms starting at 20 with step -3. Expected: 20, 17, 14, 11, 8.
3. Generate eight powers of 3 starting at 1. Expected: 1, 3, 9, 27, 81, 243, 729, 2187.
4. Generate positive multiples of 4 not exceeding 18. Expected: 4, 8, 12, 16. Test boundary 16 and 3.
5. Generate 3, 7, 11, 15 using both a maintained term and direct calculation from the index.
6. Predict the output when a term starts at 2, doubles before printing, and the loop runs three times.

## 4. Collecting values in arrays

Printing generated terms is useful, but sometimes we need to keep them for later processing. An array stores an ordered collection of values.

```javascript
const marks = [40, 70, 90];
console.log(marks[0]);
console.log(marks.length);
```

Output:

```text
40
3
```

Each value is an element. Its position is called an index. Indices begin at zero. Square brackets select an element, and the `length` property gives the length of the array. For the ordinary arrays used here, the last index is `length - 1`.

| Index | 0 | 1 | 2 |
|---|---|---|---|
| Value | 40 | 70 | 90 |

Reading `marks[3]` produces `undefined`, a value representing absence. To visit every element (called traversal), use a counter that remains below the length.

```javascript
const marks = [40, 70, 90];
for (let i = 0; i < marks.length; i++) {
  console.log(marks[i]);
}
```

Output:

```text
40
70
90
```

### Adding elements to an array

Sometimes we do not know the values when creating an array. We can begin with `[]`, an empty array, and add values as they become available. JavaScript provides `push()` for adding elements to the end of an array. A method is an operation associated with a value; we call it using a dot, its name and parentheses. Values supplied inside the parentheses are called arguments.

```javascript
const numbers = [10, 20];
numbers.push(30);
console.log(numbers);
```

Output:

```text
[10, 20, 30]
```

The original elements stay in place. The new element occupies index 2 and the length increases to 3. Although the variable is declared with `const`, the array contents can change; the variable itself cannot be reassigned.

We can supply several arguments separated by commas. `numbers.push(40, 50)` would append both values in that order. The method also produces a value, called its return value: the new length, not the array.

```javascript
const numbers = [];
const length = numbers.push(10, 20);
console.log(length);
console.log(numbers);
```

Output:

```text
2
[10, 20]
```

Do not write `numbers = numbers.push(30)`: with a `let` variable this replaces the array with a number; with `const` it causes an error. Simply call the method to modify the array.

Now we can store generated terms instead of printing them individually.

```javascript
const numbers = [];
for (let i = 1; i <= 4; i++) {
  numbers.push(i);
}
console.log(numbers);
```

Output:

```text
[1, 2, 3, 4]
```

| Iteration | Array after push |
|---|---|
| Before loop | [] |
| 1 | [1] |
| 2 | [1, 2] |
| 3 | [1, 2, 3] |
| 4 | [1, 2, 3, 4] |

The array must be created outside the loop so that previously collected elements remain available. Array display spacing can vary by environment. We will use these basic array operations throughout this chapter and defer advanced array features.

### Adding multiple elements

```javascript
const numbers = [10, 20];
numbers.push(30, 40, 50);
console.log(numbers);
```

Output: `[10, 20, 30, 40, 50]`.

### Collecting selected generated values

```javascript
const numbers = [];
for (let i = 1; i <= 10; i++) {
  if (i % 3 === 0) {
    numbers.push(i);
  }
}
console.log(numbers);
```

Output: `[3, 6, 9]`. The loop visits ten integers but appends only three.

### Practice

1. Given `[8, 3, 9]`, print the first and last elements. Expected: 8 and 9. Handle an empty array without accessing an element.
2. Traverse `[8, 3, 9]` backward. Expected: 9, 3, 8.
3. Append 4, then 7 and 9 to an empty array. Expected: `[4, 7, 9]`.
4. Predict the return values of `push(4)` and `push(7, 9)` on an initially empty array.
5. Store ten positive multiples of 5. Expected: `[5, 10, 15, 20, 25, 30, 35, 40, 45, 50]`.
6. Store integers from 1 through 30 divisible by 4. Expected: `[4, 8, 12, 16, 20, 24, 28]`.

## 5. Counting

To determine how many even numbers occur, we need to remember the number of matches encountered so far. This information is called state. Initially no values have been visited, so the counter starts at zero.

```javascript
const values = [4, 7, 2, 9, 6];
let count = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 === 0) {
    count++;
  }
}
console.log(count);
```

Output:

```text
3
```

| Value | Count before | Count after |
|---|---|---|
| 4 | 0 | 1 |
| 7 | 1 | 1 |
| 2 | 1 | 2 |
| 9 | 2 | 2 |
| 6 | 2 | 3 |

The counter describes the processed values at every stage. Adding `values[i]` instead of one would calculate their sum, not their count. Declaring the counter inside the body would reset it every iteration. Empty input and no matches both leave the count at zero. Duplicates count as separate occurrences.

### Counting several categories

```javascript
const values = [-2, 0, 4, -1];
let negative = 0;
let zero = 0;
let positive = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] < 0) negative++;
  else if (values[i] > 0) positive++;
  else zero++;
}
console.log(negative, zero, positive);
```

Output:

```text
2 1 1
```

For ordinary finite numbers these categories do not overlap. But multiples of two and three can overlap: six belongs to both. Use independent `if` statements for such counts rather than an `else if` chain.

### Counting occurrences

```javascript
const colors = ["red", "blue", "red", "green", "red"];
let count = 0;
for (let i = 0; i < colors.length; i++) {
  if (colors[i] === "red") count++;
}
console.log(count);
```

Output: `3`. Each occurrence counts separately.

### Why the counter must persist

```javascript
const numbers = [2, 4, 6];
for (let i = 0; i < numbers.length; i++) {
  let count = 0;
  if (numbers[i] % 2 === 0) count++;
  console.log(count);
}
```

Output: 1, 1, 1 (on separate lines). Each iteration recreates the counter. Move its declaration outside the loop to retain the count.

### Practice

1. Count positive, negative and zero values in `[-2, 0, 4, -1, 0, 8]`. Expected: 2, 2, 2.
2. Count occurrences of 7 in `[7, 2, 7, 7]`. Expected: 3. Target 9: 0.
3. Count marks from 50 through 79 in `[49, 50, 79, 80, 60]`. Expected: 3.
4. Count multiples of 5 and 7 independently in integers 1 through 35. Expected: 7 and 5.
5. For `[-1, 0, 50, 100, 101]`, count valid marks (0 through 100), valid passing marks (at least 50), and invalid marks. Expected: 3, 2, 2.
6. Repeat question 1 with `[]` and `[0]`. Expected: (0, 0, 0) and (0, 0, 1).

## 6. Accumulation

Counting adds one per match. To calculate a total, we instead add the actual contribution. A variable that combines contributions into a running result is called an accumulator.

```javascript
const values = [5, -3, 8];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
}
console.log(total);
```

Output:

```text
10
```

The successive totals are 5, 2 and 10. Zero represents the sum before any values have been processed. Writing `total = values[i]` would discard the previous total and leave only 8. If we are calculating a balance starting at 100, initialize the accumulator to 100 instead.

### Conditional accumulation

To sum only positive values, combine accumulation with the condition used in counting.

```javascript
const values = [5, -3, 8, 0];
let total = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) total += values[i];
}
console.log(total);
```

Output:

```text
13
```

Nonmatching values leave the total unchanged. If none qualify, the sum is zero.

### Multiplication

Accumulation need not use addition. The factorial of a positive integer is the product of the integers from one through that integer.

```javascript
let product = 1;
for (let i = 1; i <= 5; i++) {
  product *= i;
}
console.log(product);
```

Output:

```text
120
```

The successive products are 1, 2, 6, 24 and 120. Starting at zero would make every product zero. One is the correct initial value, and also gives the defined result for zero factorial. Use small nonnegative integer inputs for this exercise.

### Joining strings

The `+` operator joins strings when its operands are strings. This is called concatenation. We can accumulate text just as we accumulate numbers.

```javascript
const words = ["red", "green", "blue"];
let text = "";
for (let i = 0; i < words.length; i++) {
  if (i > 0) text += ", ";
  text += words[i];
}
console.log(text);
```

Output:

```text
red, green, blue
```

An empty string is the initial result. Adding a separator before every word except the first avoids a trailing comma. But if we skip empty words, the original index no longer tells us whether a word has already been included. We can maintain a count of included words instead.

```javascript
const words = ["", "red", "", "blue"];
let text = "";
let included = 0;
for (let i = 0; i < words.length; i++) {
  if (words[i] === "") continue;
  if (included > 0) text += ", ";
  text += words[i];
  included++;
}
console.log(text);
```

Output:

```text
red, blue
```

### Keeping intermediate results

A final total does not tell us how it changed. To retain each intermediate result, append the updated total to an array.

```javascript
const changes = [3, -1, 5];
const totals = [];
let total = 0;
for (let i = 0; i < changes.length; i++) {
  total += changes[i];
  totals.push(total);
}
console.log(totals);
```

Output:

```text
[3, 2, 7]
```

Appending before updating instead gives [0, 3, 2], the totals before each change. Choose the order according to the requirement.

### Multiple accumulators

```javascript
const changes = [4, -2, 7, -3];
let income = 0;
let expenses = 0;
for (let i = 0; i < changes.length; i++) {
  if (changes[i] > 0) income += changes[i];
  else if (changes[i] < 0) expenses -= changes[i];
}
console.log(income, expenses, income - expenses);
```

Output:

```text
11 5 6
```

Expenses are stored as positive amounts, so subtract them to obtain the net change. If we stored expenses as negative amounts, we would add the two totals instead.

### Accumulating calculated contributions

```javascript
const prices = [100, 200, 300];
let total = 0;
for (let i = 0; i < prices.length; i++) {
  const discountedPrice = prices[i] * 0.9;
  total += discountedPrice;
}
console.log(total);
```

Output: `540`. Each contribution is calculated before being added.

### Practice

1. Sum integers 1 through 100. Expected: 5050.
2. Sum negative elements of `[-3, 5, -2, 0]`. Expected: -5.
3. Calculate 5 factorial (1 * 2 * 3 * 4 * 5). Expected: 120. Test 0 factorial: 1.
4. Sum squares of positive elements in `[-2, 3, 4, 0]`. Expected: 25.
5. Join `["red", "blue", "green"]` using " - ". Expected: "red - blue - green".
6. Join nonempty words in `["", "red", "", "blue", ""]` with spaces. Expected: "red blue".
7. Store cumulative expenses for `[10, 20, 5]`. Expected: `[10, 30, 35]`.
8. Starting at 100, record balances before and after changes `[20, -30, 5]`. Expected before: `[100, 120, 90]`; after: `[120, 90, 95]`.
9. For `[100, -30, 50, -20]`, report income, positive expense magnitude and net change. Expected: 150, 50, 100.

## 7. Calculating averages

The arithmetic mean is the sum divided by the number of observations. We already know how to obtain both pieces of information. When every element qualifies, the array length is the count. When only selected elements qualify, maintain a separate count.

```javascript
const values = [10, -2, 20, 0];
let total = 0;
let count = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) {
    total += values[i];
    count++;
  }
}
if (count > 0) console.log(total / count);
else console.log("No qualifying values");
```

Output:

```text
15
```

| Value | Total | Count |
|---|---|---|
| 10 | 10 | 1 |
| -2 | 10 | 1 |
| 20 | 30 | 2 |
| 0 | 30 | 2 |

Dividing by the original length gives 7.5, answering a different question. Both the count and sum must describe the same observations. If none qualify, there is no arithmetic mean, so we explicitly report that case.

To display a running average, calculate the total divided by the count after each update. Do not average the previous average and the new value directly: that gives earlier observations less influence than later ones.

### Calculating running averages

```javascript
const marks = [60, 80, 100];
let total = 0;
let count = 0;
const averages = [];
for (let i = 0; i < marks.length; i++) {
  total += marks[i];
  count++;
  averages.push(total / count);
}
console.log(averages);
```

Output: `[60, 70, 80]`. Averaging the previous average with the new value would give 85 instead of 80, because earlier observations would have less influence.

### Practice

1. Average `[20, 25, 30]`. Expected: 25.
2. Average positive values in `[-2, 4, 0, 8]`. Expected: 6.
3. Calculate passing and failing averages for `[40, 60, 80, 20]`, passing at 50. Expected: 70 and 30.
4. Store running averages for `[10, 20, 30]`. Expected: `[10, 15, 20]`.
5. For `[]` and `[-2, 0]`, report "No qualifying values" when averaging positive values.

## 8. Finding minimum and maximum values

A sum combines every value, but a maximum retains only the best value encountered. A possible answer that we keep until a better answer appears is called a candidate.

```javascript
const values = [-8, -3, -10, -1];
if (values.length > 0) {
  let best = values[0];
  for (let i = 1; i < values.length; i++) {
    if (values[i] > best) best = values[i];
  }
  console.log(best);
} else {
  console.log("No maximum");
}
```

Output:

```text
-1
```

| Candidate | Best before | Best after |
|---|---|---|
| -3 | -8 | -3 |
| -10 | -3 | -3 |
| -1 | -3 | -1 |

Starting at zero would fail because none of these values exceeds zero. Start with an actual element, and handle empty input separately. A minimum uses the same approach with `<` instead of `>`.

### Keeping the position

Sometimes we need the position of the best value rather than the value alone.

```javascript
const scores = [70, 90, 90, 60];
let bestIndex = 0;
for (let i = 1; i < scores.length; i++) {
  if (scores[i] > scores[bestIndex]) bestIndex = i;
}
console.log(bestIndex, scores[bestIndex]);
```

Output:

```text
1 90
```

This example assumes a nonempty array. The index identifies the candidate and allows us to retrieve its value. Strict comparison retains the first tied maximum. Using `>=` retains the last. To find every tied maximum, first find the maximum, then collect every position equal to it using the searching pattern below.

### Finding both extrema

```javascript
const numbers = [12, 7, 25, 9, 18];
let smallest = numbers[0];
let largest = numbers[0];
for (let i = 1; i < numbers.length; i++) {
  if (numbers[i] < smallest) smallest = numbers[i];
  if (numbers[i] > largest) largest = numbers[i];
}
console.log(smallest, largest, largest - smallest);
```

Output: `7 25 18`.

### Finding extrema among qualifying values

```javascript
const marks = [30, 40, 75, 60, 90];
let found = false;
let largest;
for (let i = 0; i < marks.length; i++) {
  const mark = marks[i];
  if (mark < 50) continue;
  if (!found || mark > largest) {
    largest = mark;
    found = true;
  }
}
if (found) console.log(largest);
else console.log("No passing marks");
```

Output: `90`. The first qualifying mark establishes the candidate. Short-circuiting avoids comparing it against an undefined candidate.

### Practice

1. Find minimum and maximum of `[4, -2, 9, 3]`, separately and together. Expected: -2 and 9.
2. Calculate the range (maximum minus minimum) of that array. Expected: 11.
3. Find first and last maximum indices in `[4, 9, 2, 9]`. Expected: 1 and 3.
4. Find the smallest positive value in `[-3, 0, 7, 2]`. Expected: 2.
5. Find the highest failing mark in `[40, 80, 49, 50]`. Expected: 49.
6. Test extrema on `[-8, -2]`, `[5, 5]`, `[7]`, and `[]`. Expected pairs: (-8, -2), (5, 5), (7, 7), and no extrema.

## 9. Searching

Searching examines values to locate a match. When we examine them sequentially, the technique is called linear search. Unlike counting, searching for the first match does not require visiting the remaining elements.

```javascript
const values = [8, 4, 7, 4];
let index = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] === 4) {
    index = i;
    break;
  }
}
console.log(index);
```

Output:

```text
1
```

Negative one represents absence because it is not a valid array index. Test `index !== -1` for success, not `if (index)`: zero is falsy and negative one is truthy.

To find the last match, traverse backward and stop at the first match encountered in that direction.

```javascript
const values = [8, 4, 7, 4];
let index = -1;
for (let i = values.length - 1; i >= 0; i--) {
  if (values[i] === 4) {
    index = i;
    break;
  }
}
console.log(index);
```

Output:

```text
3
```

Alternatively, traverse forward and overwrite the index on every match. That approach must visit every element. If all matching positions are required, collect them rather than replacing a single index.

```javascript
const values = [8, 4, 7, 4];
const indices = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] === 4) indices.push(i);
}
console.log(indices);
```

Output:

```text
[1, 3]
```

No matches produce an empty array. Breaking early would make this result incomplete.

### Practice

1. Find first and last indices of 7 in `[7, 2, 7, 3]`. Expected: 0 and 2.
2. Collect all indices of 7 in that array. Expected: `[0, 2]`.
3. Find the first negative index in `[4, 0, -2, -3]`. Expected: 2.
4. Find the last passing mark in `[60, 20, 80, 40]`. Expected: 80 at index 2.
5. Find every index containing the maximum of `[9, 3, 9, 2]`. Expected: `[0, 2]`.
6. Search `[4, 5]` for 4, 5 and 9. Expected first indices: 0, 1, -1.

## 10. Boolean flags

Sometimes we only need to know whether a match exists, not where it occurs. A variable containing a Boolean value that remembers whether something has happened is called a flag.

```javascript
const values = [2, 4, 5];
let anyOdd = false;
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 !== 0) {
    anyOdd = true;
    break;
  }
}
console.log(anyOdd);
```

Output:

```text
true
```

Initially no odd number has been discovered. One match establishes the answer. For an all-values question, reverse the reasoning: assume the requirement holds until a counterexample is discovered.

```javascript
const values = [2, 4, 5];
let allEven = true;
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 !== 0) {
    allEven = false;
    break;
  }
}
console.log(allEven);
```

Output:

```text
false
```

Assigning the current condition to a flag on every iteration without breaking would only report the last element's result. For empty input, any is false and all is true because there is no counterexample. If the requirement demands at least one element, check length separately. A none test starts true and becomes false when a match appears.

### Accidentally overwriting the flag

```javascript
const marks = [40, 80, 90];
let allPassed = true;
for (let i = 0; i < marks.length; i++) {
  allPassed = marks[i] >= 50;
}
console.log(allPassed);
```

Output: `true` (incorrect). The flag describes only the final mark. Preserve the first failure by setting the flag to false and breaking.

### Practice

1. Check whether `[1, 3, 4]` contains an even number. Expected: true.
2. Check whether all values in `[2, 0, 5]` are positive. Expected: false.
3. Check whether no element in `[1, 2, 3]` equals 4. Expected: true.
4. Check whether every mark in `[0, 100, 101]` is valid (0 through 100). Expected: false.
5. Check whether at least one valid passing mark exists in `[120, 30, 50]`. Expected: true.
6. Explain why any-even is false and all-positive is true for an empty array under the usual logical definitions.

## 11. Filtering

Counting tells us how many values qualify. Sometimes we need the qualifying values themselves. Selecting original elements according to a condition is called filtering.

```javascript
const values = [5, -2, 0, 8, 5];
const selected = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) selected.push(values[i]);
}
console.log(selected);
```

Output:

```text
[5, 8, 5]
```

| Value | Selected after processing |
|---|---|
| 5 | [5] |
| -2 | [5] |
| 0 | [5] |
| 8 | [5, 8] |
| 5 | [5, 8, 5] |

The result preserves original order and duplicate occurrences. Creating a separate array preserves the input. If no values qualify, the result remains empty. The condition can be compound: `values[i] >= 0 && values[i] <= 100` selects valid marks.

### Filtering using compound conditions

```javascript
const marks = [45, -10, 80, 120, 50, 0, 100];
const valid = [];
for (let i = 0; i < marks.length; i++) {
  const mark = marks[i];
  if (mark >= 0 && mark <= 100) valid.push(mark);
}
console.log(valid);
```

Output: `[45, 80, 50, 0, 100]`.

### Practice

1. From `[-3, 0, 4, 6, -2]`, separately collect negatives and multiples of 3. Expected: `[-3, -2]` and `[-3, 0, 6]`.
2. Collect valid marks from `[-1, 0, 100, 101]` and nonempty strings from `["", "a", "b", ""]`. Expected: `[0, 100]` and `["a", "b"]`.
3. Collect values outside the inclusive range 10 through 20 from `[9, 10, 15, 20, 21]`. Expected: `[9, 21]`.
4. Filter passing marks from `[40, 50, 80, 20]` and count excluded marks. Expected: `[50, 80]`, 2 excluded.
5. Filter positive values from `[3, -1, 3, 2]`. Expected: `[3, 3, 2]`; explain why order and duplicates remain.

## 12. Transforming values

Filtering selects existing values. Transformation instead calculates a new value from each input. For instance, converting Celsius to Fahrenheit requires multiplying by nine, dividing by five and adding thirty-two.

```javascript
const celsius = [0, 20, 100];
const fahrenheit = [];
for (let i = 0; i < celsius.length; i++) {
  fahrenheit.push(celsius[i] * 9 / 5 + 32);
}
console.log(fahrenheit);
```

Output:

```text
[32, 68, 212]
```

Each input produces exactly one output. We can also replace an element using assignment to its indexed position. For example, `celsius[0] = 32` replaces the first element. Performing such assignments inside a loop is called in-place transformation. It destroys the original values, so use a separate result when those values are still needed.

Filtering and transformation can be combined: test whether a value is positive before appending its square. The condition determines whether to store anything; the calculation determines what to store.

### Combining filtering and transformation

```javascript
const numbers = [-3, 2, 0, 4, -1];
const squares = [];
for (let i = 0; i < numbers.length; i++) {
  const number = numbers[i];
  if (number > 0) squares.push(number * number);
}
console.log(squares);
```

Output: `[4, 16]`. The condition determines whether to produce an output; the calculation determines its value.

### Practice

1. Double `[2, -3, 0]`. Expected: `[4, -6, 0]`.
2. Apply a 10 percent discount to `[100, 200, 300]`. Expected: `[90, 180, 270]`.
3. Convert `[40, 50, 80]` into pass/fail strings. Expected: `["fail", "pass", "pass"]`.
4. Square `[-2, 3, 0]` in place. Expected modified array: `[4, 9, 0]`.
5. Convert positive Celsius readings in `[-5, 0, 10, 20]` to Fahrenheit using C * 9 / 5 + 32. Expected: `[50, 68]`.
6. Collect squares of even integers in `[1, 2, 3, 4]`. Expected: `[4, 16]`.
7. Explain when to preserve the original array rather than modify it.

## 13. Partitioning

Filtering keeps selected values and discards the others from its result. If both groups are needed, we can collect each group separately. Assigning every occurrence to exactly one group is called partitioning.

```javascript
const values = [3, 8, 2, 5];
const even = [];
const odd = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 === 0) even.push(values[i]);
  else odd.push(values[i]);
}
console.log(even);
console.log(odd);
```

Output:

```text
[8, 2]
[3, 5]
```

For integer inputs, every value belongs to one group and cannot belong to both. The group lengths must add up to the original length. Forward traversal preserves order within each group. More than two groups can use an `else if` chain, for example negative, zero and positive groups. Independent overlapping conditions would not form a partition.

### Designing nonoverlapping categories

```javascript
const marks = [40, 85, -5, 50, 120, 90, 79];
const invalid = [];
const distinctions = [];
const passing = [];
const failing = [];
for (let i = 0; i < marks.length; i++) {
  const mark = marks[i];
  if (mark < 0 || mark > 100) invalid.push(mark);
  else if (mark >= 80) distinctions.push(mark);
  else if (mark >= 50) passing.push(mark);
  else failing.push(mark);
}
console.log(invalid, distinctions, passing, failing);
```

Output: `[-5, 120] [85, 90] [50, 79] [40]`. Check validity first so that 120 is not classified as a distinction.

### Practice

1. Partition `[1, 2, 3, 4]` into even and odd groups. Expected: `[2, 4]` and `[1, 3]`.
2. Partition `[-2, 0, 3, -1]` into negative, zero and positive groups. Expected: `[-2, -1]`, `[0]`, `[3]`.
3. Partition `[-1, 40, 50, 79, 80, 100, 101]` into invalid (outside 0-100), distinction (80-100), pass (50-79), fail (0-49). Expected: `[-1, 101]`, `[80, 100]`, `[50, 79]`, `[40]`.
4. Partition `[49, 50, 99, 100]` into prices below 50, 50 to below 100, and at least 100. Expected: `[49]`, `[50, 99]`, `[100]`.
5. Verify that group lengths in question 3 sum to 7. Test empty input: all groups empty.
6. Explain why putting 6 in both multiples-of-2 and multiples-of-3 groups does not form a partition.

## 14. Combining loop patterns

Suppose an examination report requires passing scores, their count, sum, average and maximum. For [40, 80, 50, 30, 90], with a passing threshold of 50, the expected passing scores are [80, 50, 90], count 3, total 220 and maximum 90.

We need filtering, accumulation and candidate tracking. The passing array's length already provides the count. The first passing score establishes the maximum, and later scores replace it only if larger.

```javascript
const scores = [40, 80, 50, 30, 90];
const passing = [];
let total = 0;
let maximum;
for (let i = 0; i < scores.length; i++) {
  const score = scores[i];
  if (score < 50) continue;
  if (passing.length === 0 || score > maximum) {
    maximum = score;
  }
  passing.push(score);
  total += score;
}
console.log(passing);
console.log(passing.length, total);
if (passing.length > 0) {
  console.log(total / passing.length, maximum);
} else {
  console.log("No passing scores");
}
```

Output:

```text
[80, 50, 90]
3 220
73.33333333333333 90
```

| Score | Count | Total | Maximum |
|---|---|---|---|
| 40 | 0 | 0 | undefined |
| 80 | 1 | 80 | 80 |
| 50 | 2 | 130 | 80 |
| 30 | 2 | 130 | 80 |
| 90 | 3 | 220 | 90 |

The maximum test must occur before appending the current score, so that an empty passing array identifies the first match. Logical OR short-circuiting avoids comparing against the uninitialized maximum on that first match. Alternatively, filter first and calculate statistics in a second loop. This separates responsibilities but revisits the passing scores. Choose according to clarity and requirements.

## 15. Recognizing and selecting loop patterns

| Requirement | Pattern |
|---|---|
| Perform N actions | Repetition |
| Create ordered terms | Generation |
| How many? | Counting |
| How much altogether? | Accumulation |
| What is the mean? | Average |
| Largest or smallest? | Candidate tracking |
| Where is a match? | Searching |
| Does any or all qualify? | Boolean flag |
| Which values qualify? | Filtering |
| Calculate a new value for each input | Transformation |
| Assign every value to a group | Partitioning |

Work through a small input manually before writing code. Identify what must be remembered, what its initial value means, how each iteration changes it and when enough work has been done. Test empty inputs, single elements, duplicates, ties, no matches, all matches and exact thresholds where relevant.

### Finding a threshold while retaining the final total

```javascript
const sales = [100, 150, 200, 100, 300];
let total = 0;
let day = -1;
for (let i = 0; i < sales.length; i++) {
  total += sales[i];
  if (day === -1 && total >= 500) day = i + 1;
}
console.log(day, total);
```

Output: `4 850`. Breaking on day four would prevent calculating the final total. The additional condition preserves the first matching day.

## 16. Summary

A loop pattern is a reusable solution structure. The required output determines the pattern. Initialize maintained information to represent the situation before processing, update it consistently and stop early only when remaining values cannot change the answer. Combine established patterns to solve larger problems.

## 17. Integrated exercises

1. **Ticket printer:** Given quantity 4, print Ticket 1 through Ticket 4. Test quantity 0: no output.
2. **Sequence generator:** Generate six descending multiples of 5 starting at 30 and six powers of 2 starting at 1. Expected: `[30, 25, 20, 15, 10, 5]` and `[1, 2, 4, 8, 16, 32]`.
3. **Array builder:** Build `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]` using push. Predict the final return value.
4. **Divisibility report:** For integers 1 through 12, independently count multiples of 2 and 3. Expected: 6 and 4.
5. **Arithmetic report:** For `[-2, 3, 0, -4]`, sum negatives and multiply nonzero values. Expected: -6 and 24.
6. **Text cleanup:** Join `["", "JavaScript", "", "loops"]` with spaces, skipping empty words. Expected: "JavaScript loops".
7. **Account history:** Starting at 100, process `[50, -20, -10]`. Record balances before and after. Expected: `[100, 150, 130]` and `[150, 130, 120]`.
8. **Passing average:** Given `[40, 50, 80, 90]`, average marks at least 50. Expected: 220 / 3. Test `[20, 30]`: no passing scores.
9. **Extrema report:** Given `[9, 2, 9, 4]`, find minimum, maximum and first and last maximum indices. Expected: 2, 9, 0, 2.
10. **Tied maxima:** Given `[3, 8, 8, 1, 8]`, collect all maximum indices. Expected: `[1, 2, 4]`.
11. **Search report:** Search `[5, 2, 5]` for 5. Expected first 0, last 2, all `[0, 2]`. Target 9: -1, -1, `[]`.
12. **Flags:** For `[50, 80, 0]`, report any failing, all passing, none zero. Expected: true, false, false. Empty input: false, true, true under logical definitions.
13. **Percentage conversion:** Scores out of 20 are `[-1, 0, 10, 20, 21]`. Filter valid scores and convert to percentages. Expected: `[0, 50, 100]`.
14. **Partition report:** Group `[-3, 0, 2, -1, 0]` by sign. Expected: `[-3, -1]`, `[0, 0]`, `[2]`.
15. **Sales report:** Given `[40, 100, 60, 200]`, report total, count, average, largest sale and sales strictly above 100. Expected: 400, 4, 100, 200, `[200]`.
16. **Threshold report:** Given `[30, 40, 50, 20]`, find the first day cumulative sales reach 100 and the final total. Expected: day 3, total 140. Explain why stopping early loses the final total. Test `[20, 30]`: no qualifying day, total 50.

For each solution, explain the initial values, updates and stopping rule. Trace a representative input and test an edge case before moving on.

---

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: [Managing State in Loops](12-managing-state-in-loops.md)
