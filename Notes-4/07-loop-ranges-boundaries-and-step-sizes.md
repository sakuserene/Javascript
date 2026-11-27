# Lesson 07 - Loop Ranges, Boundaries and Step Sizes

*JavaScript Plain Loops*

Previous: [Choosing a Loop](06-choosing-a-loop.md) | Curriculum: Plain Loops | Next: Controlling Loops (planned)

**Filename:** `07-loop-ranges-boundaries-and-step-sizes.md`

## Table of contents

1. [Introduction](#1-introduction)
2. [Learning objectives](#2-learning-objectives)
3. [Loop ranges and boundaries](#3-loop-ranges-and-boundaries)
4. [Forward and backward iteration](#4-forward-and-backward-iteration)
5. [Step sizes](#5-step-sizes)
6. [Changing loop variables](#6-changing-loop-variables)
7. [Counting iterations](#7-counting-iterations)
8. [Dynamic loop boundaries](#8-dynamic-loop-boundaries)
9. [Practical applications](#9-practical-applications)
10. [Common mistakes and edge cases](#10-common-mistakes-and-edge-cases)
11. [Best practices](#11-best-practices)
12. [Key takeaways](#12-key-takeaways)
13. [Practice exercises](#13-practice-exercises)

## 1. Introduction

Writing a loop involves more than choosing between `for`, `while` and `do...while`. You must also determine which values the loop visits, where repetition begins, how the iteration state progresses and when repetition ends.

Consider these requirements:

- Print integers from 1 through 100.
- Process array elements from the last to the first.
- Generate every third integer within a range.
- Generate powers of two.
- Process values within dynamically calculated boundaries.
- Determine how many times a loop will execute without running it.

All these tasks depend on understanding ranges, boundaries and progression.

A small mistake in a comparison operator or update expression can cause a loop to omit values, process unnecessary values, access invalid array indices or fail to terminate.

This lesson covers the complete Loop Ranges, Boundaries and Step Sizes curriculum branch. Examples are independent programs.

## 2. Learning objectives

- Construct inclusive, exclusive and half-open ranges.
- Iterate forward and backward with fixed or variable steps.
- Generate arithmetic, geometric and nonlinear progressions.
- Calculate iteration counts and predict boundary overshoot.
- Work with changing boundaries and recognize termination risks.

## 3. Loop ranges and boundaries

A range describes the values eligible for iteration. The starting value, continuation condition and update together determine which values are actually visited.

### 3.1 Inclusive and exclusive boundaries

An inclusive boundary permits equality; an exclusive boundary does not.

```javascript
for (let i = 2; i <= 5; i++) {
  console.log(i);
}
```

Output:

```text
2
3
4
5
```

Changing `<=` to `<` excludes 5. A half-open range includes one endpoint and excludes the other. The notation `[2, 5)` means include 2 and exclude 5.

| Range | Integer values | Condition for ascending iteration |
|---|---|---|
| [2, 5] | 2, 3, 4, 5 | `i <= 5`, start at 2 |
| [2, 5) | 2, 3, 4 | `i < 5`, start at 2 |
| (2, 5] | 3, 4, 5 | `i <= 5`, start at 3 |
| (2, 5) | 3, 4 | `i < 5`, start at 3 |

These starting-value adjustments assume integer iteration with unit steps.

### 3.2 Empty, single-value and negative ranges

| Header | Visited values |
|---|---|
| `for (let i = 4; i < 4; i++)` | None |
| `for (let i = 4; i <= 4; i++)` | 4 |
| `for (let i = -3; i <= 1; i++)` | -3, -2, -1, 0, 1 |
| `for (let i = 5; i < 2; i++)` | None |

An initially false condition produces an empty iteration sequence in a pre-test loop. Negative boundaries follow the same comparison rules as positive boundaries.

## 4. Forward and backward iteration

Forward iteration visits increasing values; backward iteration visits decreasing values.

```javascript
for (let i = 5; i >= 1; i--) {
  console.log(i);
}
```

Output:

```text
5
4
3
2
1
```

| Direction | Start | Inclusive condition | Exclusive condition | Update |
|---|---|---|---|---|
| Forward | Lower endpoint | `i <= end` | `i < end` | `i++` |
| Backward | Upper endpoint | `i >= end` | `i > end` | `i--` |

Reversing a loop requires reconsidering its starting value, comparison and update together.

## 5. Step sizes

A step size is the amount by which a loop variable changes between successive iterations.

### 5.1 Unit and non-unit steps

```javascript
for (let i = 2; i <= 12; i += 3) {
  console.log(i);
}
```

Output:

```text
2
5
8
11
```

| Check | Current i | Condition | Next i |
|---:|---:|---|---:|
| 1 | 2 | true | 5 |
| 2 | 5 | true | 8 |
| 3 | 8 | true | 11 |
| 4 | 11 | true | 14 |
| 5 | 14 | false | None |

An inclusive boundary does not guarantee that the endpoint is visited. Here the progression skips 12 and overshoots it.

### 5.2 Negative and fractional steps

```javascript
for (let i = 10; i >= 0; i -= 3) {
  console.log(i);
}
```

Output:

```text
10
7
4
1
```

```javascript
for (let i = 0; i <= 1; i += 0.25) {
  console.log(i);
}
```

Output:

```text
0
0.25
0.5
0.75
1
```

The fraction 0.25 is exactly representable in binary floating-point. Many decimal fractions, including 0.1, are not. Prefer integer counters with scaled output when exact decimal iteration counts matter.

### 5.3 Variable and zero steps

```javascript
let step = 1;
for (let i = 0; i <= 10; i += step) {
  console.log(i);
  step++;
}
```

Output:

```text
0
2
5
9
```

| i before body | Step before body | Step after body | Next i |
|---:|---:|---:|---:|
| 0 | 1 | 2 | 2 |
| 2 | 2 | 3 | 5 |
| 5 | 3 | 4 | 9 |
| 9 | 4 | 5 | 14 |

The update uses the new step value. A zero step makes no progress; if the condition remains true and no other exit occurs, the loop will not terminate.

## 6. Changing loop variables

### 6.1 Arithmetic progressions

An arithmetic progression has a constant difference between successive terms. Addition and subtraction produce these progressions, assuming exact arithmetic.

```javascript
for (let i = 4; i <= 16; i += 4) {
  console.log(i);
}
```

Output:

```text
4
8
12
16
```

### 6.2 Geometric progressions

A geometric progression has a constant ratio between successive terms.

```javascript
for (let i = 1; i <= 32; i *= 2) {
  console.log(i);
}
```

Output:

```text
1
2
4
8
16
32
```

Division can produce a decreasing progression:

```javascript
for (let i = 64; i >= 4; i /= 2) {
  console.log(i);
}
```

Output:

```text
64
32
16
8
4
```

Starting a multiplicative progression at zero leaves it at zero. Dividing a positive value by two does not reach mathematical zero in finitely many steps, although JavaScript Number arithmetic can eventually underflow to zero. Use a meaningful positive threshold.

### 6.3 Nonlinear updates

```javascript
for (let i = 2; i <= 100; i = i * i) {
  console.log(i);
}
```

Output:

```text
2
4
16
```

The next value is 256, which fails the condition. Nonlinear updates need particular attention to fixed points, overflow and termination. Starting this example at 1 would never advance.

## 7. Counting iterations

### 7.1 Fixed-step formulas

For exact arithmetic, a positive step `s`, fixed endpoints `a` and `b`, and no early exits or other state changes:

| Progression | Iteration count |
|---|---|
| Ascending, `i < b` | `Math.max(0, Math.ceil((b - a) / s))` |
| Ascending, `i <= b` | `Math.max(0, Math.floor((b - a) / s) + 1)` |
| Descending, `i > b` | `Math.max(0, Math.ceil((a - b) / s))` |
| Descending, `i >= b` | `Math.max(0, Math.floor((a - b) / s) + 1)` |

For descending loops, `s` is the positive magnitude subtracted on each update. These formulas are mathematical rules; JavaScript floating-point rounding can invalidate their assumptions.

Example: start at 3, end at 17 inclusive, step by 4. The count is `Math.floor((17 - 3) / 4) + 1`, which is 4. The visited values are 3, 7, 11 and 15.

### 7.2 Geometric and variable progressions

For positive `a`, ratio `r > 1`, and inclusive upper bound `b >= a`, the mathematical count is `floor(log(b / a) / log(r)) + 1`. If `b < a`, the count is zero. Floating-point logarithms can round near exact powers, so do not assume a direct JavaScript implementation is exact.

For variable or nonlinear updates, trace the progression or derive a rule specific to the recurrence. A fixed-step formula does not apply.

## 8. Dynamic loop boundaries

### 8.1 Boundaries calculated before iteration

```javascript
const start = 3;
const count = 4;
const end = start + count;
for (let i = start; i < end; i++) {
  console.log(i);
}
```

Output:

```text
3
4
5
6
```

A half-open range makes the number of unit-step iterations equal to `end - start`.

### 8.2 Boundaries changing during iteration

```javascript
let end = 6;
for (let i = 0; i < end; i++) {
  console.log(i);
  end--;
}
```

Output:

```text
0
1
2
```

| Check | i | end | Condition |
|---:|---:|---:|---|
| 1 | 0 | 6 | true |
| 2 | 1 | 5 | true |
| 3 | 2 | 4 | true |
| 4 | 3 | 3 | false |

The condition is reevaluated each time. The original boundary alone cannot determine the count.

## 9. Practical applications

### 9.1 Processing a subrange of an array

```javascript
const values = [10, 20, 30, 40, 50];
for (let i = 1; i < 4; i++) {
  console.log(values[i]);
}
```

Output:

```text
20
30
40
```

The half-open index range selects indices 1, 2 and 3. For externally supplied boundaries, validate them against the array length.

### 9.2 Generating decimal values reliably

```javascript
for (let i = 0; i <= 5; i++) {
  console.log((i / 10).toFixed(1));
}
```

Output:

```text
0.0
0.1
0.2
0.3
0.4
0.5
```

The integer counter controls exactly six iterations. `toFixed(1)` returns a formatted string; it does not make floating-point arithmetic exact.

## 10. Common mistakes and edge cases

- Off-by-one errors: choose inclusive or exclusive comparisons according to the requirement.
- Wrong direction: an ascending condition paired with a decreasing counter may never become false.
- Unreachable equality: starting at 0 and adding 2 never reaches exactly 5; use a threshold when appropriate.
- Floating-point precision: repeated addition of 0.1 can skip an expected endpoint.
- Unsafe integers: at `2 ** 53`, adding 1 to a Number may leave it unchanged. Validate integer ranges or use BigInt when appropriate.
- Moving boundaries: appending to an array while looping against its live length can extend the loop indefinitely. Capture the initial length if only original positions should be visited.

## 11. Best practices

- Specify the intended visited values before writing the header.
- Match the comparison to the direction of progression.
- Prefer half-open ranges for ordinary array traversal.
- Validate externally supplied boundaries and step sizes.
- Check empty ranges, single-value ranges and overshoot.
- Use integer-controlled iteration when decimal rounding could affect termination.

## 12. Key takeaways

- A boundary determines eligibility, not whether the progression actually reaches an endpoint.
- Step size and direction determine how the iteration state changes.
- Fixed-step counts can be calculated using floor or ceiling formulas under exact-arithmetic assumptions.
- Geometric, nonlinear and dynamic progressions require different reasoning.
- Every continuing path must make appropriate progress toward termination.

## 13. Practice exercises

1. Print integers from -5 through 5 inclusive.
2. Print integers in the half-open range [3, 12).
3. Print every fourth integer starting at 2 and not exceeding 30.
4. Print values from 25 down to 0 using a step of 3. Predict the last visited value.
5. Construct an empty range and a single-value range.
6. Calculate the iteration count for start 7, exclusive end 50 and step 6. Verify by tracing.
7. Generate powers of three from 1 through 729.
8. Generate the sequence 128, 64, 32, 16, 8, 4, 2, 1 using division.
9. Trace `for (let i = 1; i < 30; i = i * 2 + 1)`.
10. Generate decimal values from 0.0 through 2.0 in increments of 0.2 using an integer counter.
11. Explain why starting at 0 and multiplying by 2 cannot reach a positive threshold.
12. Trace a loop starting at `i = 0`, with `end = 10`, incrementing `i` and decreasing `end` by 2 each iteration.
13. Write a loop that processes only indices 2 through 5 of an array, safely handling shorter arrays.
14. Design a function that validates an ascending integer range and rejects zero or negative step sizes before iterating.

---

Previous: [Choosing a Loop](06-choosing-a-loop.md) | Curriculum: Plain Loops | Next: Controlling Loops (planned)
