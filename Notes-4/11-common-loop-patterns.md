# Lesson 11 - Common Loop Patterns

*JavaScript Plain Loops*

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: Managing State in Loops (planned)

**Filename:** `11-common-loop-patterns.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Understanding loop patterns](#3-understanding-loop-patterns)
4. [Repeating operations](#4-repeating-operations)
5. [Generating sequences](#5-generating-sequences)
6. [Counting](#6-counting)
7. [Accumulation](#7-accumulation)
8. [Calculating averages](#8-calculating-averages)
9. [Finding minimum and maximum values](#9-finding-minimum-and-maximum-values)
10. [Searching](#10-searching)
11. [Boolean flags](#11-boolean-flags)
12. [Filtering](#12-filtering)
13. [Transforming values](#13-transforming-values)
14. [Partitioning](#14-partitioning)
15. [Combining loop patterns](#15-combining-loop-patterns)
16. [Recognizing the appropriate pattern](#16-recognizing-the-appropriate-pattern)
17. [Common mistakes and edge cases](#17-common-mistakes-and-edge-cases)
18. [Best practices](#18-best-practices)
19. [Key takeaways](#19-key-takeaways)
20. [Practice exercises](#20-practice-exercises)

## 1. Introduction

So far, we have learned how to construct loops, control their execution, manage variables and make decisions inside their bodies.

The next step is learning how to use loops to solve programming problems.

Although programming problems can appear very different, many share the same underlying solution structure. Calculating the total price of products, finding the sum of examination scores and calculating the total distance travelled all involve repeatedly adding values to a running total.

This reusable solution structure is called the accumulation pattern.

Similarly, finding the cheapest product, the highest examination score and the lowest recorded temperature all involve comparing values and retaining the best candidate encountered so far.

These reusable structures are known as **loop patterns**.

Learning loop patterns helps you move beyond memorizing syntax and develop the ability to recognize, design and implement solutions independently.

Instead of approaching every problem as something entirely new, you learn to identify familiar structures and adapt them to the requirements.

This lesson covers the complete Common Loop Patterns curriculum branch using traditional JavaScript loops. Run each example independently.

## 2. Learning objectives

- Recognize common patterns from problem requirements.
- Initialize and update counters, accumulators and candidate values correctly.
- Generate, search, filter, transform and partition sequences.
- Combine patterns and trace their state across iterations.
- Handle empty inputs, missing matches and other boundary cases.

## 3. Understanding loop patterns

A loop pattern describes how repeated operations and state changes produce a result. It is independent of the choice of `for`, `while` or `do...while`.

For each problem, identify the input, desired output, traversal, persistent state, update rule and stopping condition. A useful question is: what must be remembered after processing each value?

## 4. Repeating operations

The simplest pattern performs an operation a specified number of times without collecting a result.

```javascript
for (let i = 0; i < 3; i++) {
  console.log("Hello");
}
```

Output:

```text
Hello
Hello
Hello
```

The counter controls repetition but does not contribute to the printed value. With a zero repetition count, a pre-test loop performs no work.

## 5. Generating sequences

Sequence generation produces values according to a rule. The counter may itself be the result, or it may be used to calculate each term.

```javascript
const squares = [];
for (let i = 1; i <= 5; i++) {
  squares.push(i * i);
}
console.log(squares);
```

Output (array formatting may vary):

```text
[1, 4, 9, 16, 25]
```

A sequence can also depend on previous state:

```javascript
let value = 1;
for (let i = 0; i < 5; i++) {
  console.log(value);
  value *= 2;
}
```

Output:

```text
1
2
4
8
16
```

Here the counter determines the number of terms, while `value` determines the next term.

## 6. Counting

Counting determines how many occurrences satisfy a requirement. Initialize a counter to zero and increment it once for each matching occurrence.

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

| Value | Even? | Count after |
|---:|---|---:|
| 4 | Yes | 1 |
| 7 | No | 1 |
| 2 | Yes | 2 |
| 9 | No | 2 |
| 6 | Yes | 3 |

The iteration counter `i` and the result counter `count` serve different purposes. Multiple counters can track different categories in one traversal.

## 7. Accumulation

Accumulation combines successive values into a running result. An accumulator must persist between iterations.

### 7.1 Initial accumulator values

Choose an initial value appropriate to the operation and requirements.

| Operation | Typical initial value |
|---|---|
| Addition | `0` |
| Multiplication | `1` |
| String concatenation | `""` |
| Collecting values | `[]` |

For addition and multiplication, these are identity values: combining them with another value leaves that value unchanged. A nonzero starting balance may instead be appropriate when the problem specifies one.

### 7.2 Summation

```javascript
const values = [5, 3, 8];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
}
console.log(total);
```

Output:

```text
16
```

| Value | Total before | Total after |
|---:|---:|---:|
| 5 | 0 | 5 |
| 3 | 5 | 8 |
| 8 | 8 | 16 |

After each iteration, `total` equals the sum of the values processed so far.

### 7.3 Multiplication

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

The successive products are 1, 2, 6, 24 and 120. This computes 5 factorial. Starting at zero would make every product zero. An empty product is conventionally 1, which also gives the correct value for 0 factorial.

### 7.4 String concatenation

```javascript
const words = ["Learn", "JavaScript", "loops"];
let sentence = "";
for (let i = 0; i < words.length; i++) {
  if (i > 0) sentence += " ";
  sentence += words[i];
}
console.log(sentence);
```

Output:

```text
Learn JavaScript loops
```

The separator is added only between words. The empty input produces an empty string.

### 7.5 Conditional accumulation

```javascript
const values = [4, -3, 6, 0];
let total = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) total += values[i];
}
console.log(total);
```

Output:

```text
10
```

Unlike counting, accumulation adds the value itself rather than one.

### 7.6 Cumulative results

Sometimes every intermediate result is needed, not just the final total.

```javascript
const values = [3, 5, 2];
const runningTotals = [];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
  runningTotals.push(total);
}
console.log(runningTotals);
```

Output:

```text
[3, 8, 10]
```

This produces inclusive prefix sums. Each result contains the sum through the corresponding input position.

### 7.7 Multiple accumulators

```javascript
const values = [4, -2, 7, -3];
let positiveTotal = 0;
let negativeTotal = 0;
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value > 0) positiveTotal += value;
  else if (value < 0) negativeTotal += value;
}
console.log(positiveTotal, negativeTotal);
```

Output:

```text
11 -5
```

Each accumulator has a separate responsibility, but both share the traversal.

## 8. Calculating averages

An arithmetic mean is the sum divided by the number of included values. For a conditional average, count only the values included in the sum.

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
const average = count === 0 ? null : total / count;
console.log(average);
```

Output:

```text
15
```

| Value | Total | Count |
|---:|---:|---:|
| 10 | 10 | 1 |
| -2 | 10 | 1 |
| 20 | 30 | 2 |
| 0 | 30 | 2 |

Dividing by the array length would incorrectly produce 7.5. If no values qualify, this implementation returns `null` to represent an unavailable average rather than dividing zero by zero and producing NaN.

## 9. Finding minimum and maximum values

Retain the best candidate encountered so far and replace it only when a better candidate appears.

```javascript
function findMaximum(values) {
  if (values.length === 0) return undefined;
  let maximum = values[0];
  for (let i = 1; i < values.length; i++) {
    if (values[i] > maximum) maximum = values[i];
  }
  return maximum;
}
console.log(findMaximum([-8, -3, -10, -1]));
console.log(findMaximum([]));
```

Output:

```text
-1
undefined
```

| Candidate | Maximum before | Maximum after |
|---:|---:|---:|
| -3 | -8 | -3 |
| -10 | -3 | -3 |
| -1 | -3 | -1 |

Initializing the maximum to zero would fail for an all-negative input. Initializing from the first element avoids that assumption. For a minimum, reverse the comparison. These examples assume dense arrays of finite numbers.

To retain the position as well as the value, track the best index. A strict comparison retains the first tied candidate; a non-strict comparison retains the last tied candidate.

## 10. Searching

Linear search examines values sequentially until a match is found or the input ends.

```javascript
const values = [8, 4, 7, 4];
const target = 4;
let foundIndex = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] === target) {
    foundIndex = i;
    break;
  }
}
console.log(foundIndex);
```

Output:

```text
1
```

The result is the first matching index. The sentinel -1 represents absence because valid array indices are nonnegative. To find the last match, continue updating the result without breaking, or search backward. To find every match, collect matching indices in an array instead.

## 11. Boolean flags

A Boolean flag records whether a condition has been established. For an existence test, start with false and switch to true when a match is found.

```javascript
const values = [3, 5, 8];
let hasEven = false;
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 === 0) {
    hasEven = true;
    break;
  }
}
console.log(hasEven);
```

Output:

```text
true
```

For a universal test, start with true and switch to false when a counterexample is found.

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

For an empty input, the existence test returns false and the universal test returns true: no counterexample exists. If the business requirement also demands a nonempty input, check that separately.

## 12. Filtering

Filtering selects original values that satisfy a condition, preserving their order when traversed forward and appended.

```javascript
const values = [5, -2, 0, 8, 5];
const positives = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) positives.push(values[i]);
}
console.log(positives);
```

Output:

```text
[5, 8, 5]
```

Filtering does not automatically remove duplicates or modify the original array. The result may contain zero through all input elements.

## 13. Transforming values

Transformation calculates a corresponding output value for each input value.

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

A one-to-one transformation of a dense array produces the same number of elements. Writing to `values[i]` instead of a new array performs an in-place transformation and changes the input. Choose deliberately based on whether the original data must be preserved.

## 14. Partitioning

Partitioning separates input values into disjoint groups according to a rule. Every value belongs to exactly one group in a complete partition.

```javascript
const values = [3, 8, 2, 5];
const evens = [];
const odds = [];
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value % 2 === 0) {
    evens.push(value);
  } else {
    odds.push(value);
  }
}
console.log(evens);
console.log(odds);
```

Output:

```text
[8, 2]
[3, 5]
```

This assumes integer inputs. Unlike filtering, partitioning retains both matching and nonmatching values. More than two groups are possible using mutually exclusive branches.

## 15. Combining loop patterns

Suppose a program must report the number, sum, average and maximum of passing scores, and retain the passing scores themselves. Assume valid numeric scores between 0 and 100.

```javascript
const scores = [40, 80, 50, 30, 90];
const passing = [];
let count = 0;
let total = 0;
let maximum;
for (let i = 0; i < scores.length; i++) {
  const score = scores[i];
  if (score < 50) continue;
  passing.push(score);
  count++;
  total += score;
  if (maximum === undefined || score > maximum) {
    maximum = score;
  }
}
const average = count === 0 ? null : total / count;
console.log(passing);
console.log(count, total, average, maximum);
```

Output:

```text
[80, 50, 90]
3 220 73.33333333333333 90
```

| Score | Action | Count | Total | Maximum |
|---:|---|---:|---:|---:|
| 40 | Skip | 0 | 0 | undefined |
| 80 | Include | 1 | 80 | 80 |
| 50 | Include | 2 | 130 | 80 |
| 30 | Skip | 2 | 130 | 80 |
| 90 | Include | 3 | 220 | 90 |

The solution combines filtering, counting, accumulation, candidate tracking and average calculation. If no score passes, the list is empty, count and total are zero, average is null and maximum is undefined. One traversal is convenient here, but separate loops can be clearer when responsibilities become complicated.

## 16. Recognizing the appropriate pattern

| Requirement | Pattern | Result state |
|---|---|---|
| Perform an action N times | Repetition | Optional counter |
| Produce a sequence | Generation | Terms or output array |
| How many match? | Counting | Counter |
| What is the combined result? | Accumulation | Running result |
| What is the mean? | Average | Sum and count |
| Which is largest or smallest? | Extremum | Best candidate |
| Where is the first match? | Search | Index or value |
| Does any or every value qualify? | Boolean flag | Boolean |
| Which values qualify? | Filtering | Selected values |
| Convert each value | Transformation | Converted values |
| Separate all values into groups | Partitioning | Group arrays |

Start with the required output rather than choosing a pattern solely from the input. The same array can be processed using many different patterns.

## 17. Common mistakes and edge cases

- Resetting persistent state inside the body instead of initializing it before traversal.
- Using zero as the initial product or as the maximum of potentially negative numbers.
- Confusing the number of matches with the sum of matching values.
- Dividing by the wrong count or failing to handle an empty average.
- Breaking after the first match when all matches are required.
- Resetting a Boolean flag after a match or counterexample has established the answer.
- Assuming filtering removes duplicates or transformation must mutate the input.
- Ignoring invalid numeric values, sparse arrays or Number precision limits. The examples assume dense arrays and valid values unless otherwise stated.

## 18. Best practices

- Define the desired result and empty-input behavior before coding.
- Give persistent state descriptive names such as `total`, `matchCount` and `maximum`.
- State what each variable represents after processing each element.
- Use early termination only when later values cannot change the required answer.
- Preserve original data unless mutation is intended.
- Test empty, single-element, all-matching, no-matching and tied-value cases.

## 19. Key takeaways

Loop patterns are reusable solution structures, not additional JavaScript syntax. Counting tracks occurrences; accumulation combines values; searching locates matches; Boolean flags answer existence or universal questions. Filtering selects values, transformation converts values and partitioning separates values into groups. More complex solutions combine these patterns with carefully initialized persistent state.

## 20. Practice exercises

### Foundation

1. Print a message exactly five times.
2. Generate the first ten positive multiples of seven.
3. Count negative integers in an array.
4. Sum the odd integers in an array.
5. Calculate the product of integers from 1 through N, including N = 0.
6. Concatenate words with commas between them but no trailing comma.

### Intermediate

7. Generate running totals for `[2, -1, 5, 3]`.
8. Calculate the average of nonnegative values, handling no matches.
9. Find the minimum and its first index without using Math.min.
10. Find the last occurrence of a target, returning -1 if absent.
11. Collect every index at which a target occurs.
12. Determine whether any value is negative and whether all values are finite numbers.
13. Filter strings to retain only nonempty strings.
14. Transform prices into discounted prices without modifying the input.
15. Partition integers into negative, zero and positive groups.

### Problem-solving

16. Find the maximum and minimum in a single traversal, handling empty input.
17. Count passing and failing scores and calculate the average passing score.
18. Filter positive integers and square only those selected values.
19. Given daily sales, produce cumulative totals and identify the first day the total reaches a specified target.
20. Design a report containing the count, sum, average, minimum and maximum of valid readings. Define invalid readings and empty-result behavior before writing code.
21. For each of the previous exercises, identify the pattern, persistent state, update rule and stopping condition. Trace at least one representative input and one edge case.

---

Previous: [Conditions Inside Loops](10-conditions-inside-loops.md) | Curriculum: Plain Loops | Next: Managing State in Loops (planned)
