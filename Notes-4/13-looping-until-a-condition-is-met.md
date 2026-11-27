# Lesson 13 - Looping Until a Condition Is Met

*JavaScript Plain Loops*

Previous: [Managing State in Loops](12-managing-state-in-loops.md) | Curriculum: Plain Loops | Next: [Infinite Loops and Loop Termination](14-infinite-loops-and-loop-termination.md) (planned)

## Table of contents

1. [Understanding condition-controlled repetition](#1-understanding-condition-controlled-repetition)
2. [Sentinel-controlled loops](#2-sentinel-controlled-loops)
3. [Threshold-controlled loops](#3-threshold-controlled-loops)
4. [Iterative approximation](#4-iterative-approximation)
5. [Validation and retry loops](#5-validation-and-retry-loops)
6. [Multiple stopping conditions](#6-multiple-stopping-conditions)
7. [Combining stopping techniques](#7-combining-stopping-techniques)
8. [Summary](#8-summary)
9. [Integrated exercises](#9-integrated-exercises)

## 1. Understanding condition-controlled repetition

In previous chapters, many of our loops performed a predetermined number of iterations.

For example, the following loop performs five operations:

```javascript
for (let i = 0; i < 5; i++) {
  console.log("Processing");
}
```

Before execution begins, we already know how many times the body will execute.

However, many practical problems do not provide a predetermined iteration count.

Suppose we want to determine how many months it takes to save 1,000 when we deposit 150 each month.

Instead of specifying the number of months, we specify the situation that must eventually be reached.

```javascript
let savings = 0;
let months = 0;

while (savings < 1000) {
  savings += 150;
  months++;
}

console.log(savings, months);
```

Output:

```text
1050 7
```

The number of iterations is discovered during execution.

| Month | Savings | Target reached? |
|---:|---:|---|
| 0 | 0 | No |
| 1 | 150 | No |
| 2 | 300 | No |
| 3 | 450 | No |
| 4 | 600 | No |
| 5 | 750 | No |
| 6 | 900 | No |
| 7 | 1050 | Yes |

Notice the distinction between the condition we want to achieve and the condition that keeps the loop running.

The desired stopping condition is:

```javascript
savings >= 1000
```

The continuation condition is its opposite:

```javascript
savings < 1000
```

When using a `while` loop, we normally express the continuation condition.

When using `break`, we can express the stopping condition directly.

Both approaches are useful.

Throughout this chapter, we will develop loops that stop when special values appear, numerical targets are reached, approximations become sufficiently accurate, inputs become valid or several competing conditions determine whether execution should continue.

Run each complete code example independently unless otherwise stated.

## 2. Sentinel-controlled loops

Suppose a device supplies measurements, followed by -1 to indicate that no more measurements should be processed. A special value that signals the end of processing is called a sentinel. It is a control signal rather than an ordinary observation.

```javascript
const readings = [12, 8, 0, 15, -1, 20];
let total = 0;
let count = 0;

for (let i = 0; i < readings.length; i++) {
  const reading = readings[i];
  if (reading === -1) {
    break;
  }
  total += reading;
  count++;
}
console.log(total, count);
```

Output: `35 4`.

The sentinel must be checked before accumulation. Otherwise -1 would reduce the total and increase the count. Zero is a legitimate reading, so `if (!reading)` would incorrectly stop at zero.

| Reading | Total | Count | Action |
|---:|---:|---:|---|
| 12 | 12 | 1 | Process |
| 8 | 20 | 2 | Process |
| 0 | 20 | 3 | Process |
| 15 | 35 | 4 | Process |
| -1 | 35 | 4 | Stop |

The array boundary also protects us if the sentinel is missing. A sentinel at index zero produces zero processed observations. Values after the sentinel are deliberately ignored.

### Checking the sentinel in the loop condition

```javascript
const readings = [12, 8, 0, 15, -1, 20];
let i = 0;
let total = 0;
while (i < readings.length && readings[i] !== -1) {
  total += readings[i];
  i++;
}
console.log(total, i);
```

Output: `35 4`.

The boundary is checked first. Short-circuit evaluation prevents accessing an element beyond the intended range. The counter also represents the number processed because every visited nonsentinel value is accepted.

### Choosing an unambiguous sentinel

If negative temperatures are legitimate, -1 cannot reliably signal the end. A distinct string such as "stop" can be used when observations are numbers.

```javascript
const readings = [-1, 0, 4, "stop", 9];
let total = 0;
for (let i = 0; i < readings.length; i++) {
  if (readings[i] === "stop") break;
  total += readings[i];
}
console.log(total);
```

Output: `3`.

This example assumes every nonsentinel value is a number. The sentinel must be distinguishable from legitimate data. Unlike a separator, which ends one batch and allows another to begin, a sentinel here ends the entire traversal.

### Practice

1. Given `[4, 0, 6, "stop", 9]`, sum and count the numbers before the sentinel. Expected: total 10, count 3. Test `["stop"]` (0, 0) and `[2, 3]` (5, 2).
2. Find the maximum before -1 in `[4, 9, 2, -1, 20]`. Expected: 9. For `[-1, 4]`, report `No readings`. For `[-8, -3, -1]`, expect -3.
3. Predict the result if the sentinel check is moved after accumulation in the first worked example. Explain the error and correct it. Expected corrected total: 35, count: 4.

## 3. Threshold-controlled loops

A threshold is a boundary that determines when an action or stopping rule applies. Unlike a sentinel, it need not appear as an exact input value.

### Reaching or exceeding a target

Suppose we start with 20 units and add 30 each round until we have at least 100.

```javascript
let amount = 20;
let rounds = 0;
while (amount < 100) {
  amount += 30;
  rounds++;
}
console.log(amount, rounds);
```

Output: `110 3`.

The amounts are 20, 50, 80 and 110. The target is crossed without ever equaling 100. Using `amount !== 100` instead would not reliably terminate. If the initial amount already meets the target, zero rounds are needed.

### Reaching a lower boundary

Suppose a tank contains 95 units and releases up to 30 per round. It must never contain a negative quantity.

```javascript
let water = 95;
let rounds = 0;
while (water > 0) {
  let released = 30;
  if (water < released) {
    released = water;
  }
  water -= released;
  rounds++;
  console.log(water);
}
console.log(rounds);
```

Output:

```text
65
35
5
0
4
```

The final release is five, not 30. This is different from allowing a threshold to be crossed: the physical requirement limits the operation itself.

### Testing whether the next operation is permitted

Suppose we have a budget of 100 and items cost 30 each. We must not exceed the budget.

```javascript
let spent = 0;
let items = 0;
while (spent + 30 <= 100) {
  spent += 30;
  items++;
}
console.log(spent, items);
```

Output: `90 3`.

Testing `spent < 100` would permit a fourth purchase and produce 120. The condition must examine the proposed next state when crossing the boundary is forbidden.

### Progress toward a threshold

A positive deposit moves savings toward a higher target. A zero deposit does not. A negative deposit moves away from it. Validate the update amount before relying on it.

```javascript
const deposit = 0;
let savings = 100;
let months = 0;
if (deposit <= 0 && savings < 1000) {
  console.log("Target cannot be reached");
} else {
  while (savings < 1000) {
    savings += deposit;
    months++;
  }
  console.log(months);
}
```

Output: `Target cannot be reached`.

These examples assume small finite numbers. More general termination and numerical limitations are covered later.

### Practice

1. Start with 75 and deposit 40 per round until savings reach at least 200. Report rounds and final savings. Expected: 4, 235. Starting at 200 requires zero rounds.
2. Empty a tank containing 73 units, releasing at most 20 per round. Expected remaining amounts: 53, 33, 13, 0. Never produce a negative amount. Test an initially empty tank.
3. With a budget of 95 and a price of 25, find the maximum affordable quantity. Expected: 3 items, 75 spent. Reject a price of zero or less. Test budget 100 (4 items) and 10 (0 items).
4. Starting at 10, add 5 per round. Compare stopping at least at 20 with stopping strictly above 20. Expected: 2 rounds and 3 rounds respectively.

## 4. Iterative approximation

Some problems require a value close enough to a desired answer rather than an exact answer. An approximation is an estimate. Iterative approximation repeatedly improves an estimate until its error is sufficiently small.

### Measuring error

Suppose we want an estimate close to 10. An estimate of 9.8 is 0.2 away; an estimate of 10.2 is also 0.2 away. We need the magnitude of the difference, ignoring its sign.

```javascript
let difference = 9.8 - 10;
if (difference < 0) {
  difference = -difference;
}
console.log(difference);
```

JavaScript prints approximately 0.2. Decimal fractions are not always represented exactly by JavaScript numbers. The nonnegative magnitude is called absolute error. A tolerance is the maximum error we are willing to accept.

### Repeatedly reducing an error

Suppose a controller begins with an estimate of zero and moves halfway toward a target of 10 each time. We accept an error of at most 0.1.

```javascript
const target = 10;
const tolerance = 0.1;
let estimate = 0;
let error = target - estimate;
let steps = 0;
while (error > tolerance) {
  estimate += (target - estimate) / 2;
  error = target - estimate;
  steps++;
}
console.log(estimate, steps);
```

Output: `9.921875 7`.

| Step | Estimate | Error |
|---:|---:|---:|
| 0 | 0 | 10 |
| 1 | 5 | 5 |
| 2 | 7.5 | 2.5 |
| 3 | 8.75 | 1.25 |
| 4 | 9.375 | 0.625 |
| 5 | 9.6875 | 0.3125 |
| 6 | 9.84375 | 0.15625 |
| 7 | 9.921875 | 0.078125 |

The error halves each iteration. This is a simple demonstration of convergence: estimates approach a target. The target is known here so that we can study the stopping mechanism before solving an unknown value.

The signed error works only because the estimate starts below the target and approaches it from below. Starting above the target would incorrectly stop immediately. Use the magnitude of the difference when either direction is possible.

### Approximating a square root

The square root of 2 is a positive number whose square is 2. We can improve a positive estimate by averaging it with 2 divided by that estimate. This is a square-root approximation rule, a special case of Newton's method. We use the rule here without requiring calculus.

For a guess of 1, the next guess is (1 + 2 / 1) / 2 = 1.5. The next is (1.5 + 2 / 1.5) / 2, approximately 1.4167.

```javascript
const number = 2;
const tolerance = 0.000001;
let guess = 1;
let error = guess * guess - number;
if (error < 0) error = -error;
let steps = 0;
while (error > tolerance && steps < 100) {
  guess = (guess + number / guess) / 2;
  error = guess * guess - number;
  if (error < 0) error = -error;
  steps++;
}
if (error <= tolerance) {
  console.log(guess, steps);
} else {
  console.log("Tolerance not reached");
}
```

Output: `1.4142135623746899 4`.

The error measures how closely the square of the guess matches 2, not the exact distance from the unknown square root. The positive initial guess avoids division by zero. The iteration limit protects against excessive repetition. Reaching the limit does not establish success, so the final error is checked separately.

### Practice

1. Starting at zero, move halfway toward 20 until the error is at most 0.01. Expected: 11 steps, estimate 19.990234375.
2. Adapt the halfway rule to work from either side of the target. Start at 30, target 10, tolerance 0.1. Expected: 8 steps, estimate 10.078125. Starting at 10 requires zero steps.
3. Approximate the square root of 9 using the demonstrated rule, starting at 1, with squared error at most 0.000001 and at most 100 steps. Expected: approximately 3. Repeat with initial guess 3 (zero steps).
4. For the preceding problem, use a limit of one step and initial guess 1. Expected: guess 5, squared error 16, failure. Explain why reaching the limit does not establish accuracy.

## 5. Validation and retry loops

Validation determines whether a supplied value satisfies requirements. A retry is another attempt after an unsuccessful attempt.

Suppose a valid examination mark must be between zero and 100. We will simulate successive input attempts with an array, so no browser input methods are required.

```javascript
const attempts = [-5, 120, 75, 80];
let i = 0;
let accepted = false;
let mark;
while (i < attempts.length && !accepted) {
  const candidate = attempts[i];
  i++;
  if (candidate >= 0 && candidate <= 100) {
    mark = candidate;
    accepted = true;
  } else {
    console.log("Invalid:", candidate);
  }
}
if (accepted) {
  console.log("Accepted:", mark);
} else {
  console.log("No valid input");
}
```

Output:

```text
Invalid: -5
Invalid: 120
Accepted: 75
```

The counter advances on every attempt, including invalid ones. The accepted flag distinguishes a valid mark of zero from failure. The final 80 is not consumed because validation has already succeeded.

### Limiting retries

Suppose only three attempts are permitted. An attempt includes the first try; three attempts therefore permit at most two retries.

```javascript
const attempts = [-5, 120, -1, 75];
const limit = 3;
let used = 0;
let accepted = false;
while (used < attempts.length && used < limit && !accepted) {
  const candidate = attempts[used];
  used++;
  if (candidate >= 0 && candidate <= 100) {
    accepted = true;
    console.log("Accepted:", candidate);
  }
}
if (!accepted) {
  console.log("No valid mark within available attempts");
}
console.log("Attempts:", used);
```

Output:

```text
No valid mark within available attempts
Attempts: 3
```

The valid fourth value is never examined. Incrementing the counter only on success would repeatedly examine the same invalid value.

### Using do...while for an initial attempt

A do...while loop is appropriate when an attempt must occur before its result can be tested, provided input is available.

```javascript
const attempts = [-5, 75];
let used = 0;
let valid = false;
if (attempts.length > 0) {
  do {
    const mark = attempts[used];
    used++;
    valid = mark >= 0 && mark <= 100;
  } while (!valid && used < attempts.length);
}
console.log(valid, used);
```

Output: `true 2`.

The outer condition prevents an attempt on an empty array. Retrying a real external operation may require waiting or asynchronous code, which is outside this chapter. Do not use a tight synchronous loop to wait for an external service.

### Practice

1. Given `[-3, 0, 7, 9]`, accept the first strictly positive number. Expected: 7, three attempts. For `[-3, 0]`, report failure after two attempts.
2. Given `[-5, 0, 75]`, accept the first mark between 0 and 100 inclusive. Expected: 0, two attempts. Explain why testing the accepted mark for truthiness is incorrect.
3. Limit validation to two attempts. For `[-1, 100, 50]`, expect success with 100 after two attempts. For `[-1, 101, 50]`, expect failure after two attempts. For `[]`, expect failure after zero attempts.

## 6. Multiple stopping conditions

A loop may stop for several reasons. Suppose a program collects sales until the total reaches 100, a sentinel appears or the available data ends.

The loop should continue only while none of those stopping conditions has occurred. If A or B is sufficient to stop, continuation requires both not A and not B.

### Combining continuation conditions

```javascript
const sales = [30, 40, 50, 20];
let i = 0;
let total = 0;
while (i < sales.length && total < 100) {
  total += sales[i];
  i++;
}
console.log(total, i);
```

Output: `120 3`.

Using OR instead of AND would allow processing to continue after one requirement fails. It could even access values beyond the array boundary.

### Recording the stopping reason

When different stopping reasons require different responses, remember the reason explicitly.

```javascript
const sales = [30, 40, -1, 50];
let total = 0;
let reason = "Data exhausted";
for (let i = 0; i < sales.length; i++) {
  if (sales[i] === -1) {
    reason = "Sentinel encountered";
    break;
  }
  total += sales[i];
  if (total >= 100) {
    reason = "Target reached";
    break;
  }
}
console.log(total, reason);
```

Output: `70 Sentinel encountered`.

The default reason applies when traversal finishes normally. The sentinel is checked before processing; the target is checked after processing. If the final available value reaches the target, the target reason takes precedence over exhaustion because the target check executes first.

### Practice

1. Given `[30, 40, -1, 50]`, accumulate until total reaches 100, -1 appears, or data ends. Expected: 70, sentinel. Test `[60, 40]` (100, target) and `[20]` (20, exhausted).
2. Add a limit of two processed values, excluding the sentinel. For `[20, 30, 80]`, expect 50, limit reached. Assume a positive integer limit.
3. With the same limit and target, process `[60, 40, 80]`. Give target success priority over the limit. Expected: 100, target reached. Explain where the checks must occur.

## 7. Combining stopping techniques

Suppose a savings program receives proposed deposits. Negative amounts other than -1 are invalid; -1 cancels processing. Zero is invalid. Stop after reaching 100 or examining five proposals. Assume the initial savings are zero and all inputs are finite numbers.

We need a total, an attempt counter, a valid deposit counter and a stopping reason. Invalid attempts consume an attempt but do not change savings. Cancellation also consumes an attempt, but is not a deposit.

```javascript
const proposals = [30, -5, 40, 35, 20];
const limit = 5;
let savings = 0;
let used = 0;
let deposits = 0;
let reason = "Data exhausted";
while (used < proposals.length && used < limit) {
  const amount = proposals[used];
  used++;
  if (amount === -1) {
    reason = "Cancelled";
    break;
  }
  if (amount > 0) {
    savings += amount;
    deposits++;
  }
  if (savings >= 100) {
    reason = "Target reached";
    break;
  }
  if (used === limit) {
    reason = "Attempt limit reached";
  }
}
console.log(savings, used, deposits, reason);
```

Output: `105 4 3 Target reached`.

| Proposal | Attempts | Deposits | Savings |
|---:|---:|---:|---:|
| 30 | 1 | 1 | 30 |
| -5 | 2 | 1 | 30 |
| 40 | 3 | 2 | 70 |
| 35 | 4 | 3 | 105 |

The final proposal is not examined. The attempt limit takes precedence over exhaustion if both occur together, while cancellation and success take precedence over the limit. These are chosen requirements, not universal rules.

## 8. Summary

| Technique | Stopping rule |
|---|---|
| Sentinel | A designated control value appears |
| Threshold | A numerical boundary is reached or crossed |
| Approximation | Error falls within tolerance |
| Validation | A candidate satisfies requirements |
| Retry limit | The permitted attempts are exhausted |
| Multiple conditions | Any designated stopping event occurs |

Identify what must change during each iteration, whether the condition is checked before or after processing, and whether reaching a limit actually means success.

## 9. Integrated exercises

### Exercise 1: Sentinel-controlled sales

Given `const sales = [40, 60, 0, -1, 90];`, report the total, count and average before -1. Zero is a valid sale. Expected: total 100, count 3, average approximately 33.333333333333336. For `[-1]`, report total 0, count 0 and `No average`.

### Exercise 2: Savings target

Given deposits `[20, 35, 50, 10]`, starting savings 15 and target 100, report the first one-based day reaching the target and the balance on that day. Expected: day 3, balance 120. With deposits `[10, 20]`, report target not reached and balance 45. If the initial balance meets the target, report day 0.

### Exercise 3: Budget-controlled purchases

Given prices `[25, 40, 50, 10]` and budget 100, stop before the first unaffordable purchase, even if later items are affordable. Expected: 2 purchases, 65 spent, 35 remaining. Assume positive prices. Test `[100]` (one purchase) and `[101]` (zero purchases).

### Exercise 4: Approximation with limits

Start at 0 and move halfway toward 16, with tolerance 0.25 and a maximum of six iterations. Expected: estimate 15.75, six iterations, success. With a limit of five, expect 15.5, five iterations, failure. Success on the final permitted iteration takes priority over the limit.

### Exercise 5: Input validation

Given attempts `[-5, 120, 75, 80]`, accept the first mark in the inclusive range 0 to 100. The value -1 cancels validation. Allow at most three attempts. Expected: accepted 75, three attempts. For `[-5, -1, 75]`, expect cancellation after two attempts. For `[101, 102, 103, 50]`, expect limit reached after three attempts. Empty input reports exhaustion.

### Exercise 6: Batch processing with a sentinel

Given `[4, 6, 0, 0, 3, 7, -1, 8]`, collect totals of nonempty batches separated by zero, stopping entirely at -1. Save the current nonempty batch before stopping. Expected: `[10, 10]`. Test `[0, -1]` (empty result) and `[2, 3]` (`[5]`). Assume positive readings apart from the two control values.

### Exercise 7: Debugging a threshold loop

The following fragment is intended to reach at least 100. Do not execute it unchanged.

```javascript
let amount = 20;
while (amount !== 100) {
  amount += 30;
}
```

Explain why the condition is unsuitable and correct it. Expected final amount: 110, after three updates.

### Exercise 8: Combined stopping rules

Given proposals `[20, 0, -5, 40, -1, 80]`, target 100 and attempt limit 5, process positive deposits, reject zero and negative amounts other than -1, and cancel on -1. Count every examined proposal as an attempt. Expected: savings 60, attempts 5, deposits 2, reason `Cancelled`. Cancellation and target success take precedence over the limit. Test `[100]` (success on first attempt) and `[]` (data exhausted).

For each exercise, derive the maintained state, continuation condition, stopping conditions and update order. Test empty input, immediate success, failure and boundary cases where applicable. Solutions are intentionally omitted.

---

Previous: [Managing State in Loops](12-managing-state-in-loops.md) | Curriculum: Plain Loops | Next: [Infinite Loops and Loop Termination](14-infinite-loops-and-loop-termination.md) (planned)
