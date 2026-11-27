# Lesson 11 - Common Loop Patterns

*JavaScript Plain Loops*

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: Managing State in Loops (planned)

## Table of contents

1. [Introduction](#1-introduction)
2. [Repeating operations](#2-repeating-operations)
3. [Generating sequences](#3-generating-sequences)
4. [Working with collections of values](#4-working-with-collections-of-values)
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
15. [Recognizing loop patterns](#15-recognizing-loop-patterns)
16. [Summary](#16-summary)
17. [Practice exercises](#17-practice-exercises)

## 1. Introduction

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

## 4. Working with collections of values

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

## 15. Recognizing loop patterns

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

## 16. Summary

A loop pattern is a reusable solution structure. The required output determines the pattern. Initialize maintained information to represent the situation before processing, update it consistently and stop early only when remaining values cannot change the answer. Combine established patterns to solve larger problems.

## 17. Practice exercises

1. Print N numbered tickets, including N = 0.
2. Generate six descending multiples of five and six powers of two.
3. Build an array of the integers from 1 through 10 using push. Predict the return value of the final call.
4. Count multiples of two and three independently.
5. Sum negative values and multiply nonzero values separately.
6. Join nonempty words with separators.
7. Store running balances before and after each transaction, starting at 100.
8. Calculate the mean of passing scores; handle no passing scores.
9. Find both extrema and the first and last maximum positions.
10. Find every index tied for the maximum.
11. Implement first, last and all matches for a target, including absent targets.
12. Check any failing, all passing and none zero, including empty input.
13. Filter valid marks and transform them into percentages out of a specified maximum.
14. Partition numbers into negative, zero and positive groups.
15. Produce a sales report with total, count, average, largest sale and sales above a threshold.
16. Find the first day cumulative sales reach 100. Extend the solution to calculate the final total too. Explain why breaking early would then be incorrect.

For each solution, explain the initial values, updates and stopping rule. Trace a representative input and test an edge case before moving on.

---

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: Managing State in Loops (planned)
