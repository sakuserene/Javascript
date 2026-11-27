# Lesson 23 - Loop Correctness and Edge Cases

[Previous: Lesson 22](22-converting-and-refactoring-loops.md) | [Next: Lesson 24 (planned)](24-debugging-and-testing-loops.md)

## Learning objectives

By the end of this chapter, you should be able to derive safe loop boundaries, identify invalid state transitions, specify input contracts, reason about numerical limitations, safely mutate collections and establish correctness using invariants and termination arguments. We build on traditional loops, arrays, state tracking, binary search and refactoring from earlier chapters.

## Table of contents

- [1. Correctness and contracts](#1-correctness-and-contracts)
- [2. Boundary errors](#2-boundary-errors)
- [3. State management errors](#3-state-management-errors)
- [4. Control-flow errors](#4-control-flow-errors)
- [5. Input validation and edge cases](#5-input-validation-and-edge-cases)
- [6. Floating-point precision](#6-floating-point-precision)
- [7. Looping with BigInt](#7-looping-with-bigint)
- [8. Mutating collections during iteration](#8-mutating-collections-during-iteration)
- [9. Loop invariants and termination](#9-loop-invariants-and-termination)
- [10. Integrated exercises](#10-integrated-exercises)
- [11. Summary](#11-summary)

## 1. Correctness and contracts

Suppose a loop must count positive numbers. Does zero count? Are numeric strings permitted? What happens for an empty array? These questions describe the contract: the permitted inputs and required behavior. Without a contract, we cannot decide whether an unusual result is an error.

A precondition is a requirement that holds before execution, such as receiving a dense array of finite numbers. A postcondition describes what must hold afterward, such as returning the number of elements strictly greater than zero without changing the input. An edge case is an input or state near a meaningful boundary, such as an empty array, a single element or a value equal to a threshold. Invalid inputs are not necessarily edge cases within the contract; their handling must be specified separately.

Consider this count, with a contract accepting dense arrays of finite numbers, including empty arrays.

```javascript
const values = [-2, 0, 4, 7];
let count = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) count++;
}
console.log(count);
```

Expected output:

```text
2
```

The initial count is zero because no elements have been examined. Each positive element contributes exactly one. The condition is strict because zero is excluded. Empty input produces zero without any special branch. Correctness includes termination: the loop must finish as well as produce the required answer. A test demonstrates behavior for a particular input; reasoning about all permitted inputs requires more than a successful test.

## 2. Boundary errors

### Deriving boundaries from accessed indexes

An array of length n has indexes 0 through n - 1. Instead of memorizing loop syntax, write down which indexes the body accesses. If the body accesses a[i], the condition i < n is sufficient for an increasing index starting at zero. If it accesses a[i + 1], we additionally need i + 1 < n, or i < n - 1.

Problem: count adjacent increases in [3, 5, 2, 8]. The pairs are (3,5), (5,2), (2,8), so the answer is 2.

```javascript
const a = [3, 5, 2, 8];
let count = 0;
for (let i = 0; i < a.length - 1; i++) {
  if (a[i + 1] > a[i]) count++;
}
console.log(count);
```

Expected output:

```text
2
```

| i | i + 1 | comparison | count afterward |
|---|---|---|---|
| 0 | 1 | 5 > 3 | 1 |
| 1 | 2 | 2 > 5 | 1 |
| 2 | 3 | 8 > 2 | 2 |

Using i < a.length would also examine an invalid pair with undefined. The comparison would be false here, hiding the error. If we calculated a difference instead, the invalid pair would produce NaN. An incorrect access is still incorrect even if the final count happens to match.

### Inclusive and exclusive boundaries

A half-open interval [start, end) includes start and excludes end. Its length is end - start. This makes empty ranges natural: start === end. To sum indexes 1 through 3 inclusive, convert the last index into an exclusive end of 4.

```javascript
const a = [10, 20, 30, 40, 50];
let sum = 0;
for (let i = 1; i < 4; i++) sum += a[i];
console.log(sum);
let reverse = '';
for (let i = a.length - 1; i >= 0; i--) {
  reverse += a[i] + ' ';
}
console.log(reverse.trim());
```

Expected output:

```text
90
50 40 30 20 10
```

The backward loop includes index zero by using >= rather than >. The string method trim() returns a new string with leading and trailing whitespace removed; here it removes the final separator for display only.

### Windows and nested boundaries

A window of k elements beginning at i ends at i + k - 1. Requiring this last index to be less than n gives i <= n - k. For n = 5 and k = 3, starts 0, 1 and 2 are valid. Using i < n - k loses the last window, which may contain the answer.

```javascript
const a = [1, 2, 3, 8, 9];
const k = 3;
let best = -Infinity;
for (let i = 0; i <= a.length - k; i++) {
  let sum = 0;
  for (let j = i; j < i + k; j++) sum += a[j];
  if (sum > best) best = sum;
}
console.log(best);
```

Expected output:

```text
20
```

Negative infinity is smaller than every finite number. It is a useful initial maximum when at least one finite candidate is guaranteed. Here the contract requires 1 <= k <= n. Without that requirement the sentinel might escape as an unintended result. For ragged arrays, use the current row's length, not the first row's length, to derive the inner boundary.

### Practice: boundary reasoning

Given [4, 1, 7, 2, 9], calculate all differences between elements two positions apart, later minus earlier. Expected [3, 1, 2]. What happens for length zero or one?

#### Worked solution

The accessed indexes are i and i + 2. The second must satisfy i + 2 < n, so i < n - 2. The result array starts empty because no differences exist before traversal.

```javascript
const a = [4, 1, 7, 2, 9];
const result = [];
for (let i = 0; i < a.length - 2; i++) {
  result.push(a[i + 2] - a[i]);
}
console.log(result.join(', '));
```

Expected output:

```text
3, 1, 2
```

For length zero or one the initial condition is false. Length two also produces no differences; length three produces exactly one. No special handling is needed. The join method combines array elements into a string separated by its argument; it does not modify the array.

## 3. State management errors

### Initialization must match the meaning of state

A maximum initialized to zero is incorrect for a list containing only negative numbers. For [-8, -3, -5], it never changes. If the contract guarantees a nonempty array, initialize from the first element and begin comparisons at index one. For empty arrays, explicitly choose a result such as undefined, which is not a permitted numeric element.

```javascript
function maximum(a) {
  if (a.length === 0) return undefined;
  let best = a[0];
  for (let i = 1; i < a.length; i++) {
    if (a[i] > best) best = a[i];
  }
  return best;
}
console.log(maximum([-8, -3, -5]));
console.log(maximum([]));
```

Expected output:

```text
-3
undefined
```

The initial state already represents one processed element. Starting at zero would be redundant, not incorrect. Returning undefined for empty input is a design choice, not a universal maximum convention.

### Lifetime and resetting

A row total must reset at the beginning of each row; a grand total must survive across rows. Declaring both outside the outer loop accidentally makes each row total cumulative. Declaring the grand total inside the loop loses previous rows.

```javascript
const rows = [[2, 3], [], [4, 1]];
const totals = [];
let grand = 0;
for (let i = 0; i < rows.length; i++) {
  let rowTotal = 0;
  for (let j = 0; j < rows[i].length; j++) {
    rowTotal += rows[i][j];
  }
  totals.push(rowTotal);
  grand += rowTotal;
}
console.log(totals.join(', '));
console.log(grand);
```

Expected output:

```text
5, 0, 5
10
```

The empty row contributes zero. The placement of each declaration follows the lifetime of the quantity it represents.

### Update order and old state

In a recurrence, the next state may depend on multiple old values. Suppose a and b represent consecutive Fibonacci numbers. Updating a = b before computing b = a + b destroys the old a. Preserve the next value first.

```javascript
let a = 0;
let b = 1;
const result = [];
for (let i = 0; i < 7; i++) {
  result.push(a);
  const next = a + b;
  a = b;
  b = next;
}
console.log(result.join(', '));
```

Expected output:

```text
0, 1, 1, 2, 3, 5, 8
```

At state (1,2), next is 3, then a becomes 2 and b becomes 3. The incorrect update would yield (2,4). Temporary variables preserve dependencies rather than merely improve readability.

### Missing finalization

When grouping consecutive equal values, a change signals the end of a run. The final run has no following change. It must be saved after traversal. For an empty array, do not create a fictitious run.

```javascript
function runs(a) {
  const result = [];
  if (a.length === 0) return result;
  let value = a[0];
  let count = 1;
  for (let i = 1; i < a.length; i++) {
    if (a[i] === value) count++;
    else {
      result.push(value + ':' + count);
      value = a[i];
      count = 1;
    }
  }
  result.push(value + ':' + count);
  return result;
}
console.log(runs([2, 2, 5, 5, 5, 1]).join(', '));
console.log(runs([]).length);
```

Expected output:

```text
2:2, 5:3, 1:1
0
```

### Practice: longest streak

Find the longest consecutive sequence of positive numbers in [1, 2, 0, 3, 4, 5, -1, 6]. Expected 3. Empty input should return zero. Explain why resetting the current streak must not reset the best streak.

#### Worked solution

The current streak describes the suffix of the processed elements that is positive. The best streak describes all processed elements. A nonpositive number ends the current suffix but does not erase historical information.

```javascript
const a = [1, 2, 0, 3, 4, 5, -1, 6];
let current = 0;
let best = 0;
for (let i = 0; i < a.length; i++) {
  if (a[i] > 0) current++;
  else current = 0;
  if (current > best) best = current;
}
console.log(best);
```

Expected output:

```text
3
```

Current progresses through 1, 2, 0, 1, 2, 3, 0, 1. Best progresses through 1, 2, 2, 2, 2, 3, 3, 3. Updating best on every iteration also handles a streak ending at the last element without a separate finalization step.

## 4. Control-flow errors

### Skipped progress

In a while loop, continue transfers control to the condition. It does not automatically execute a manual update at the bottom of the body.

```js
// Incorrect: hangs when i reaches an odd number.
let i = 0;
while (i < 5) {
  if (i % 2 !== 0) continue;
  console.log(i);
  i++;
}
```

At i = 1 the continue statement skips i++, so every subsequent iteration repeats the same state. One correction is to use a for loop, whose update executes after continue. Another is to avoid continue and make the operation conditional.

```javascript
let i = 0;
while (i < 5) {
  if (i % 2 === 0) console.log(i);
  i++;
}
```

Expected output:

```text
0
2
4
```

The progress operation now occurs on every path that returns to the condition. A break or return path does not require an update because it exits the loop.

### Premature termination

Searching for a match requires rejecting individual candidates without rejecting the entire search. Returning false inside the first nonmatching iteration incorrectly treats one failure as proof of absence.

```javascript
function contains(a, target) {
  for (let i = 0; i < a.length; i++) {
    if (a[i] === target) return true;
  }
  return false;
}
console.log(contains([2, 7, 9], 9));
console.log(contains([2, 7, 9], 4));
console.log(contains([], 4));
```

Expected output:

```text
true
false
false
```

A successful candidate is sufficient evidence of existence. Absence requires exhausting every candidate. This asymmetry also applies to checking whether all elements satisfy a condition: one failure is sufficient to return false, but success requires checking all elements. An empty array satisfies an all-elements condition because there is no counterexample.

### Nested exits and condition order

An unlabelled break exits only the innermost loop. To find the first matching cell in row-major order, returning from a function avoids extra flags. The result is a pair of indexes, or undefined if absent.

```javascript
function locate(rows, target) {
  for (let r = 0; r < rows.length; r++) {
    for (let c = 0; c < rows[r].length; c++) {
      if (rows[r][c] === target) return [r, c];
    }
  }
  return undefined;
}
console.log(locate([[1, 8], [8, 2]], 8).join(', '));
console.log(locate([[], [2]], 8));
```

Expected output:

```text
0, 1
undefined
```

When accessing a potentially missing row, check its existence before accessing its length. The && operator evaluates left to right and stops when an operand is falsy. For example, r < rows.length && c < rows[r].length avoids accessing rows[r] when r is beyond the end, assuming r and c are nonnegative and rows are dense.

### Practice: sentinel and continue

Sum positive values until the first zero in [-2, 3, -4, 5, 0, 9]. Negative values must be ignored, not terminate the loop. Expected 8.

#### Worked solution

The sentinel check must happen before adding. The negative-value check only decides whether to accumulate. A for loop supplies progress even if we use continue.

```javascript
const a = [-2, 3, -4, 5, 0, 9];
let sum = 0;
for (let i = 0; i < a.length; i++) {
  if (a[i] === 0) break;
  if (a[i] < 0) continue;
  sum += a[i];
}
console.log(sum);
```

Expected output:

```text
8
```

The sentinel is not processed, and 9 is never visited. If no sentinel exists, the array boundary still terminates the loop. If zero is first, the result is zero.

## 5. Input validation and edge cases

### Validate before relying on assumptions

A chunk size must be a positive integer. A zero step prevents progress; a negative step moves away from the boundary; a fractional step produces fractional indexes. Number.isInteger(value) returns true only for Number values representing integers, including negative integers and zero. It rejects strings, NaN and Infinity. Combine it with a range check.

A function can signal invalid input by throwing an error. The expression new RangeError('message') creates an error object describing an unacceptable value range. throw stops normal execution. A caller may handle it using try and catch; the catch variable receives the thrown value. Here we use this mechanism to demonstrate invalid input without stopping subsequent examples.

```javascript
function chunkTotals(a, size) {
  if (!Number.isInteger(size) || size <= 0) {
    throw new RangeError('Invalid chunk size');
  }
  const result = [];
  for (let start = 0; start < a.length; start += size) {
    let sum = 0;
    for (let i = start; i < a.length && i < start + size; i++) {
      sum += a[i];
    }
    result.push(sum);
  }
  return result;
}
console.log(chunkTotals([1, 2, 3, 4, 5], 2).join(', '));
console.log(chunkTotals([], 2).length);
try {
  chunkTotals([1], 0);
} catch (error) {
  console.log(error.message);
}
```

Expected output:

```text
3, 7, 5
0
Invalid chunk size
```

The inner condition checks both the collection boundary and the chunk boundary. The final incomplete chunk is retained by contract. This function assumes a dense array of finite numbers; it validates the size but not the array itself. A validation policy should be explicit rather than pretending that one check validates everything.

### Missing, invalid and zero are different

Array.isArray(value) checks whether a value is an array. Number.isFinite(value) checks whether it is a finite Number without coercing strings or null. Object.hasOwn(a, i) checks whether the index exists directly on the array. A hole differs from an explicitly stored undefined even though reading either normally produces undefined.

Problem: average only present finite numbers in an array, ignoring holes and invalid values. Return undefined when no valid values exist.

```javascript
function average(a) {
  if (!Array.isArray(a)) throw new TypeError('Expected array');
  let sum = 0;
  let count = 0;
  for (let i = 0; i < a.length; i++) {
    if (!Object.hasOwn(a, i)) continue;
    if (!Number.isFinite(a[i])) continue;
    sum += a[i];
    count++;
  }
  if (count === 0) return undefined;
  return sum / count;
}
console.log(average([0, , 6, '9', NaN, undefined]));
console.log(average([, NaN]));
```

Expected output:

```text
3
undefined
```

The accepted elements are 0 and 6. Dividing by a.length would count rejected entries. Checking if (!a[i]) would reject valid zero and accept truthy invalid strings. TypeError describes an unacceptable input type. This example assumes sums remain finite; large finite values can still overflow when added.

### Practice: choose a test matrix

Design cases for a function returning the first index of a finite number strictly above a finite threshold, or -1 when absent. Assume a dense array of finite numbers.

#### Worked solution

```javascript
function firstAbove(a, threshold) {
  for (let i = 0; i < a.length; i++) {
    if (a[i] > threshold) return i;
  }
  return -1;
}
console.log(firstAbove([], 5));
console.log(firstAbove([5], 5));
console.log(firstAbove([6], 5));
console.log(firstAbove([1, 5, 6], 5));
console.log(firstAbove([7, 8], 5));
console.log(firstAbove([-4, -2], -3));
```

Expected output:

```text
-1
-1
0
2
0
1
```

These cases distinguish empty input, equality, first and last positions, multiple matches and negative thresholds. Returning zero for absence would be ambiguous because zero is a valid index. Returning -1 avoids that ambiguity. Invalid inputs are outside this stated contract; a public-facing implementation may need additional validation.

## 6. Floating-point precision

### Why repeated addition can miss a boundary

JavaScript Number uses binary floating-point representation. Many decimal fractions, including 0.1, cannot be represented exactly in binary with a finite number of digits. Arithmetic rounds to representable values. This is not a loop-specific bug, but repetition can accumulate rounding errors.

```javascript
let x = 0;
for (let i = 0; i < 3; i++) x += 0.1;
console.log(x);
console.log(x === 0.3);
```

Expected output:

```text
0.30000000000000004
false
```

A loop that continues until x === 0.3 may miss the target and never finish. Even x <= 0.3 can exclude an intended final value. If the requirement is to generate four decimal tenths from zero to three tenths, use integer indexes to control iteration and derive the displayed value from the index.

```javascript
for (let i = 0; i <= 3; i++) {
  console.log(i / 10);
}
```

Expected output:

```text
0
0.1
0.2
0.3
```

This prevents cumulative addition from determining the iteration count. The resulting Number values are still approximations. For exact decimal accounting with two decimal places, represent amounts as integer cents from the beginning, provided totals remain safe integers. Converting an arbitrary decimal Number into cents is itself subject to rounding and requires a defined conversion policy.

### Tolerances and convergence

When a computation is approximate, the contract can specify an acceptable absolute error. Math.abs(x) returns the nonnegative magnitude of x. Thus Math.abs(actual - target) measures the distance between two values. A tolerance of 0.000001 permits a difference of at most one millionth.

Problem: approximate the square root of 2 using the repeated update x = (x + 2 / x) / 2, starting at 1. Stop when the squared estimate differs from 2 by at most 0.000001, or report failure after 100 updates.

```javascript
let x = 1;
let steps = 0;
const tolerance = 0.000001;
while (Math.abs(x * x - 2) > tolerance && steps < 100) {
  x = (x + 2 / x) / 2;
  steps++;
}
console.log(Math.abs(x * x - 2) <= tolerance);
console.log(steps);
```

Expected output:

```text
true
4
```

The first update averages 1 and 2 to obtain 1.5. The next averages 1.5 and approximately 1.3333 to obtain approximately 1.4167. The residual x*x - 2 becomes smaller. The limit is a safeguard, not evidence of convergence: we check the residual again afterward. The residual tolerance is not the same as a tolerance on the root itself. This example has fixed, well-behaved input; generalizing it requires validation and a policy for nonfinite intermediate results.

Absolute tolerance is useful near zero. For values of very different magnitudes, relative tolerance may be more appropriate. One combined comparison uses Math.abs(a - b) <= Math.max(absTol, relTol * Math.max(Math.abs(a), Math.abs(b))). Math.max returns the largest of its arguments. The inner maximum sets a scale, multiplication derives the relative allowance, and the outer maximum chooses the larger allowance. Both tolerances must be chosen from the problem requirements, not blindly set to Number.EPSILON. Number.EPSILON is the spacing between 1 and the next larger representable Number, not a universal error bound.

### Stagnation and safe integers

At large magnitudes, neighboring representable Numbers can be more than one unit apart. Number.MAX_SAFE_INTEGER is 9007199254740991. Integers beyond this limit are not all exactly representable. Number.isSafeInteger checks whether a Number is an integer within the safe range.

```javascript
let x = Number.MAX_SAFE_INTEGER + 1;
console.log(x);
console.log(x + 1 === x);
console.log(Number.isSafeInteger(x));
```

Expected output:

```text
9007199254740992
true
false
```

An increment can therefore fail to change the state. A loop with i++ and an upper bound beyond this point may never terminate. Validate integer ranges and intermediate arithmetic, or use BigInt when exact large integer arithmetic is required.

### Practice: decimal sequence

Generate 0.2, 0.4, 0.6, 0.8 and 1 using an integer-controlled loop. Explain why comparing a repeatedly incremented decimal counter with 1 is less robust.

#### Worked solution

There are five terms, with term number i corresponding to i / 5. The integer counter determines exactly five iterations independently of decimal rounding.

```javascript
for (let i = 1; i <= 5; i++) console.log(i / 5);
```

Expected output:

```text
0.2
0.4
0.6
0.8
1
```

Repeated decimal addition can happen to reach 1 for a particular sequence but is not guaranteed to reach an arbitrary target exactly. Deriving each value independently also avoids carrying the previous rounding error into the next term.

## 7. Looping with BigInt

### Exact integer arithmetic

BigInt is a distinct primitive type representing integers of arbitrary magnitude, subject to available resources. An integer literal followed by n creates a BigInt. For example, 1n is not the Number 1. Arithmetic between BigInts preserves exact integer results. Ordinary arithmetic operators generally cannot mix Number and BigInt: 1n + 1 throws TypeError. Use consistent types for arithmetic operands.

```javascript
let x = 9007199254740992n;
for (let i = 0n; i < 3n; i++) {
  console.log(x.toString());
  x += 1n;
}
```

Expected output:

```text
9007199254740992
9007199254740993
9007199254740994
```

The toString() method converts the BigInt to a decimal string for display without the n notation. The increment i++ preserves the type of i. Comparisons such as < can compare Number and BigInt, but strict equality distinguishes them. Keeping counters and bounds of the same type avoids confusion.

### Products, division and conversion

Factorial multiplies the integers from 1 through n. Its initial product is 1n, the multiplicative identity. For zero, the loop does not execute and the result is 1n, consistent with the definition of 0 factorial.

```javascript
function factorial(n) {
  if (typeof n !== 'bigint' || n < 0n) {
    throw new RangeError('Expected nonnegative BigInt');
  }
  let product = 1n;
  for (let i = 2n; i <= n; i++) product *= i;
  return product;
}
console.log(factorial(0n).toString());
console.log(factorial(25n).toString());
console.log((7n / 2n).toString());
```

Expected output:

```text
1
15511210043330985984000000
3
```

BigInt division truncates toward zero; it cannot represent fractional results. The typeof operator returns a string identifying the operand type. BigInt('9007199254740993') constructs the exact integer from a decimal string, whereas BigInt(9007199254740993) converts a Number that has already rounded. Number(bigintValue) converts back to Number and may lose precision. Math functions such as Math.sqrt do not accept BigInt directly. BigInt solves integer precision problems, not all numerical problems or resource limits.

### Practice: sum a large integer range

Sum the five consecutive integers starting at 9007199254740992n. Expected 45035996273704970. Do not convert the values to Number.

#### Worked solution

Use a BigInt accumulator and a BigInt offset. The offset ranges from 0n to 4n. Adding it to the start produces each required integer exactly.

```javascript
const start = 9007199254740992n;
let sum = 0n;
for (let offset = 0n; offset < 5n; offset++) {
  sum += start + offset;
}
console.log(sum.toString());
```

Expected output:

```text
45035996273704970
```

The mathematical check is five times the start plus 0 + 1 + 2 + 3 + 4. Both terms can be computed exactly with BigInt. Initializing sum to 0 instead would fail on the first addition because the operand types differ.

## 8. Mutating collections during iteration

### Deletion changes the meaning of the next index

The array splice(start, count) method removes count elements starting at start, shifts subsequent elements left and returns an array of removed elements. The original array is modified. Removing an element and then incrementing the index can skip its successor.

```javascript
const a = [2, 4, 5, 6, 8];
for (let i = 0; i < a.length; i++) {
  if (a[i] % 2 === 0) a.splice(i, 1);
}
console.log(a.join(', '));
```

Expected output:

```text
4, 5, 8
```

At index 0, removing 2 shifts 4 into index 0. Incrementing i to 1 skips 4. The same happens to 8 after deleting 6. The required result was [5]. There are three useful corrections.

```javascript
const a = [2, 4, 5, 6, 8];
let i = 0;
while (i < a.length) {
  if (a[i] % 2 === 0) a.splice(i, 1);
  else i++;
}
console.log(a.join(', '));
```

Expected output:

```text
5
```

On deletion, keep i unchanged because a new unprocessed element occupies that index. Otherwise advance. Each iteration reduces the number of unprocessed elements, a.length - i, by one. This is progress even when i does not increase.

```javascript
const a = [2, 4, 5, 6, 8];
for (let i = a.length - 1; i >= 0; i--) {
  if (a[i] % 2 === 0) a.splice(i, 1);
}
console.log(a.join(', '));
```

Expected output:

```text
5
```

Backward deletion shifts only elements to the right of the removed position, which have already been examined. It is suitable when the predicate does not depend on forward processing order or changing indexes. Repeated splice can require many shifts. Read/write compaction avoids repeated shifts and preserves the order of retained values.

```javascript
const a = [2, 4, 5, 6, 8];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (a[read] % 2 !== 0) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
console.log(a.join(', '));
```

Expected output:

```text
5
```

Write never exceeds read, so an unread element is never overwritten. The length assignment removes the unused tail after all original elements have been examined. This works for ordinary dense mutable arrays and a predicate based on the current value. If preserving the original is required, collect retained elements into a separate array instead.

### Appending and changing boundaries

If the body appends an element on every iteration and the condition uses the live length, the boundary moves forward as quickly as the counter. To process only the original elements, capture the original length before appending.

```javascript
const a = [1, 2, 3];
const originalLength = a.length;
for (let i = 0; i < originalLength; i++) {
  a.push(a[i] * 10);
}
console.log(a.join(', '));
```

Expected output:

```text
1, 2, 3, 10, 20, 30
```

This snapshot is only a snapshot of the length, not the values. If the body also deletes or reorders elements, originalLength does not guarantee that original elements are visited exactly once. A separate copy may be necessary. For objects stored in arrays, copying the array does not copy the objects themselves; modifying a shared object affects both arrays.

### Practice: remove adjacent duplicates

Given a sorted dense array [1, 1, 1, 2, 2, 4], remove duplicates in place while preserving one copy of each value. Empty input must remain empty. Expected [1, 2, 4].

#### Worked solution

The first retained value is always accepted. Later values are accepted only if different from the last retained value. Because the array is sorted, equal values are consecutive. The write pointer represents the number retained, so the last retained index is write - 1.

```javascript
const a = [1, 1, 1, 2, 2, 4];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (write === 0 || a[read] !== a[write - 1]) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
console.log(a.join(', '));
```

Expected output:

```text
1, 2, 4
```

The || operator short-circuits when write is zero, avoiding the nonexistent previous retained index. After the first three reads, write is 1; after the two 2s, write is 2; after 4, write is 3. Empty input never enters the loop. Unsorted input would require a different approach because duplicates may be separated.

## 9. Loop invariants and termination

### Establishing a statement that stays true

A loop invariant is a statement that is true at a chosen point in every iteration, commonly immediately before the condition is evaluated. It connects the current program state to the problem requirements. To establish partial correctness, show initialization, preservation and the consequence of exiting. To establish total correctness, also prove termination.

Consider summing the first n elements of a dense array, with 0 <= n <= a.length and exact arithmetic assumed for the permitted values.

```javascript
const a = [4, 7, 2, 9];
const n = 3;
let sum = 0;
let i = 0;
while (i < n) {
  sum += a[i];
  i++;
}
console.log(sum);
```

Expected output:

```text
13
```

Invariant: at the condition check, 0 <= i <= n and sum equals the sum of elements at indexes 0 through i - 1.

Initialization: i = 0 and sum = 0 represent an empty processed prefix. Preservation: if i < n, a[i] is the next unprocessed element. Adding it extends the sum by one element, and incrementing i extends the processed prefix by one. Exit: i >= n and i <= n imply i = n, so the sum covers exactly the required prefix. Termination: n - i is a nonnegative integer that decreases by one per iteration. It cannot decrease forever.

| Condition check | i | sum | Processed indexes |
|---|---|---|---|
| Initial | 0 | 0 | none |
| After first | 1 | 4 | 0 |
| After second | 2 | 11 | 0, 1 |
| After third | 3 | 13 | 0, 1, 2 |

An invariant need not be true at every intermediate statement in the body. After sum += a[i] but before i++, the sum already covers one additional element. The invariant is restored at the next condition check.

### Binary search: preserving candidates

For a sorted ascending array, an exact search can maintain a closed interval [left, right]. The invariant is that if the target exists, at least one occurrence remains in that interval, unless the function has already returned. All discarded positions are known not to contain the target.

```javascript
function search(a, target) {
  let left = 0;
  let right = a.length - 1;
  while (left <= right) {
    const mid = left + Math.trunc((right - left) / 2);
    if (a[mid] === target) return mid;
    if (a[mid] < target) left = mid + 1;
    else right = mid - 1;
  }
  return -1;
}
console.log(search([2, 5, 8, 12, 16], 12));
console.log(search([2, 5, 8, 12, 16], 7));
console.log(search([], 7));
```

Expected output:

```text
3
-1
-1
```

Subtracting left from right gives the interval width. Dividing by two gives half the width; Math.trunc removes the fractional part, and adding left translates the offset into an actual index. When a[mid] is smaller than target, sortedness guarantees every index at or below mid is also too small. The new left is mid + 1, excluding the examined midpoint. The other branch is symmetric. The candidate count right - left + 1 decreases strictly on every unsuccessful iteration. Using left = mid instead can leave the interval unchanged when only one or two candidates remain. The proof depends on sortedness and comparable values; NaN or unsorted input invalidates the elimination argument.

### Nested loops and progress measures

For nested loops, prove that each inner traversal terminates for every outer state, then prove the outer loop terminates. For a loop with multiple pointers, a useful measure can be the number of remaining candidates rather than one pointer alone. In a merge of two arrays, (a.length - i) + (b.length - j) decreases whenever one element is consumed. In a deletion loop, the remaining length minus the current index decreases even if the index stays unchanged. A maximum iteration limit guarantees an exit only if its counter advances on every continuing path; it does not guarantee the desired answer was found.

### Practice: prove a counting loop

Count values equal to 7 in [7, 2, 7, 7]. State an invariant and a termination measure.

#### Worked solution

```javascript
const a = [7, 2, 7, 7];
let count = 0;
for (let i = 0; i < a.length; i++) {
  if (a[i] === 7) count++;
}
console.log(count);
```

Expected output:

```text
3
```

At each condition check, count equals the number of 7s in indexes 0 through i - 1. Initially the prefix is empty and count is zero. Each iteration adds exactly one if the next value matches, otherwise adds nothing. The update extends the prefix by one. At exit the prefix is the whole array. The measure a.length - i decreases by one and reaches zero, assuming the array length is unchanged. This establishes the result for every permitted input, not just the example.

## 10. Integrated exercises

Attempt the problems before reading their solutions.

### Exercise 1 - Repair a range report

For [-5, -2, -8, -1], report the maximum and sum of adjacent differences (later minus earlier). Expected maximum -1 and difference sum 4. Empty input should return undefined for maximum and zero for difference sum. Explain why initializing maximum to zero and iterating adjacent pairs through the last index are incorrect.

#### Solution

Separate the two state meanings. The maximum needs an actual candidate, whereas the difference sum has identity zero. Begin at index one after initializing the maximum from index zero. Each current element has a valid predecessor. Empty input is handled before accessing index zero.

```javascript
function report(a) {
  if (a.length === 0) return [undefined, 0];
  let best = a[0];
  let differenceSum = 0;
  for (let i = 1; i < a.length; i++) {
    if (a[i] > best) best = a[i];
    differenceSum += a[i] - a[i - 1];
  }
  return [best, differenceSum];
}
const result = report([-5, -2, -8, -1]);
console.log(result[0], result[1]);
const empty = report([]);
console.log(empty[0], empty[1]);
```

Expected output:

```text
-1 4
undefined 0
```

The differences are 3, -6 and 7, whose sum is 4. For a single element, the maximum is that element and the difference sum is zero. Under exact arithmetic the differences telescope to last minus first, providing an independent check. Floating-point arithmetic may not preserve that equality exactly.

### Exercise 2 - Validate and compact readings

Modify [0, NaN, 3, -1, '4', 5, Infinity] in place, retaining only finite nonnegative Numbers. Report the retained count and total. Expected array [0, 3, 5], count 3 and total 8. Assume an ordinary dense mutable array and finite totals.

#### Solution

Use read/write compaction to avoid skipped elements. Validate each value before arithmetic. The write index counts retained values, so no separate count is necessary. Sum starts at zero and changes only when a value is retained.

```javascript
const a = [0, NaN, 3, -1, '4', 5, Infinity];
let write = 0;
let sum = 0;
for (let read = 0; read < a.length; read++) {
  const value = a[read];
  if (!Number.isFinite(value) || value < 0) continue;
  a[write] = value;
  write++;
  sum += value;
}
a.length = write;
console.log(a.join(', '));
console.log(write, sum);
```

Expected output:

```text
0, 3, 5
3 8
```

The invariant is that the prefix of length write contains exactly the accepted values from the processed original prefix, in original order, and sum is their total. Since write <= read before each iteration, writing cannot damage unread elements. Zero is accepted; the string '4' is rejected without coercion. Empty input and all-invalid input both yield count and sum zero. If mutation were prohibited, a new result array would be preferable.

### Exercise 3 - Exact monetary totals

Three payments in integer cents are 9007199254740992n, 125n and 75n. Compute the running totals exactly. Expected 9007199254740992, 9007199254741117, 9007199254741192. Explain why using Number is unsafe.

#### Solution

The first payment already exceeds the safe integer range. Keep every operand as BigInt and start the accumulator at 0n. Print after each addition because the requirement is cumulative totals, not only the final total.

```javascript
const payments = [9007199254740992n, 125n, 75n];
let total = 0n;
for (let i = 0; i < payments.length; i++) {
  total += payments[i];
  console.log(total.toString());
}
```

Expected output:

```text
9007199254740992
9007199254741117
9007199254741192
```

The array index is a Number because it indexes a normal array; it is never added to a BigInt. Only the payment arithmetic needs BigInt. The middle total is odd and cannot be represented exactly as a Number at that magnitude. Empty input prints no running totals, while the final accumulator remains 0n.

### Exercise 4 - Find the first qualifying position

Given sorted [1, 3, 3, 3, 8, 10], return the first index whose value is at least 3. Expected 1. For target 11 return the array length (6); for empty input return zero. Use binary search and explain termination.

#### Solution

Unlike exact search, equality is not enough to return immediately: earlier equal values may exist. Use a half-open candidate interval [left, right), with right initially equal to the length. The answer is a boundary between values smaller than target and values at least target. The boundary may equal the length.

```javascript
function lowerBound(a, target) {
  let left = 0;
  let right = a.length;
  while (left < right) {
    const mid = left + Math.trunc((right - left) / 2);
    if (a[mid] < target) left = mid + 1;
    else right = mid;
  }
  return left;
}
console.log(lowerBound([1, 3, 3, 3, 8, 10], 3));
console.log(lowerBound([1, 3, 3, 3, 8, 10], 11));
console.log(lowerBound([], 3));
```

Expected output:

```text
1
6
0
```

For target 3, the initial midpoint is 3. Its value qualifies, so right becomes 3. Next midpoint 1 qualifies, so right becomes 1. Midpoint 0 does not qualify, so left becomes 1. The loop ends. The invariant is that indexes below left are too small and indexes at or above right qualify (within the array). The boundary lies between left and right inclusive. Since left <= mid < right whenever the loop runs, either update strictly decreases right - left. At exit the two boundaries coincide. This is why right = mid is correct here even though right = mid can be wrong in a closed-interval exact search.

### Exercise 5 - Process batches without losing the final batch

Process [2, 3, 0, 4, 0, 0, 5] as batches separated by zero. Ignore empty batches. Return the sum of each nonempty batch, including the last one. Expected [5, 4, 5]. Assume positive integer data and zero separators. Explain the state reset and finalization.

#### Solution

A batch has a running sum and a count. Count distinguishes an empty batch from a nonempty batch, and would still work if signed values were permitted and a batch could sum to zero. At a separator, save only if count is positive, then reset both state variables. At the end, apply the same save condition.

```javascript
function batchSums(a) {
  const result = [];
  let sum = 0;
  let count = 0;
  for (let i = 0; i < a.length; i++) {
    if (a[i] === 0) {
      if (count > 0) result.push(sum);
      sum = 0;
      count = 0;
    } else {
      sum += a[i];
      count++;
    }
  }
  if (count > 0) result.push(sum);
  return result;
}
console.log(batchSums([2, 3, 0, 4, 0, 0, 5]).join(', '));
console.log(batchSums([0, 0]).length);
console.log(batchSums([]).length);
```

Expected output:

```text
5, 4, 5
0
0
```

The first separator saves 5 and resets state. The next saves 4. The immediately following separator sees count zero and saves nothing. The final 5 remains in state until the post-loop finalization. Removing the final if loses that batch. Moving the reset inside the nonempty branch would also work under this contract, but unconditional reset at every separator makes the state transition explicit.

## 11. Summary

Correct loops follow from explicit contracts and meaningful state definitions. Derive boundaries from actual accessed indexes, preserve old values when updates depend on them, and ensure every continuing control-flow path makes progress. Validate assumptions before relying on them. Treat floating-point calculations as approximate and use BigInt for exact large integer arithmetic when appropriate. When mutating a collection, reason about which elements have been processed and how indexes change. Establish invariants at consistent points, prove preservation and use a decreasing nonnegative progress measure to establish termination. Test representative edge cases as a complement to reasoning, not a replacement for it.

[Back to table of contents](#table-of-contents) | [Previous: Lesson 22](22-converting-and-refactoring-loops.md) | [Next: Lesson 24 (planned)](24-debugging-and-testing-loops.md)
