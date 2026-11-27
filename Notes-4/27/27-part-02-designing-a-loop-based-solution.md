# Lesson 27 - Part 2: Designing a Loop-Based Solution

[Previous: Understanding the Problem](27-part-01-understanding-the-problem.md) | [Next: Recognizing and Combining Loop Patterns (planned)](27-part-03-recognizing-and-combining-loop-patterns.md)

## Table of contents

- [From requirements to a design](#from-requirements-to-a-design)
- [Choosing and initializing state](#choosing-and-initializing-state)
- [Selecting boundaries and update rules](#selecting-boundaries-and-update-rules)
- [Designing a search](#designing-a-search)
- [Tracking changing state](#tracking-changing-state)
- [Designing a terminating loop](#designing-a-terminating-loop)
- [Combining state variables](#combining-state-variables)
- [Independent practice](#independent-practice)
- [Worked solutions](#worked-solutions)
- [Summary](#summary)

## From requirements to a design

A problem statement describes a desired result. An algorithm describes the steps that produce that result. A loop is one way of implementing repeated steps of an algorithm.

Do not begin by asking whether to use `for` or `while`. First ask what a person would do to solve the problem manually. Then identify which actions repeat and which information must survive from one repetition to the next.

Consider a shop with sales of 12, 5, 18 and 7 units. Calculate the total number of units sold. Assume the input is a dense array of finite nonnegative integers and the total is within JavaScript's safe integer range. The empty array should produce zero.

Manually, start with nothing counted. Add 12, then 5, then 18, then 7. The intermediate totals are 12, 17, 35 and 42.

We do not need four separate variables for the intermediate totals. Once the next total is calculated, the previous total is no longer needed. One variable can hold the changing total.

```javascript
const sales = [12, 5, 18, 7];
let total = 0;
for (let i = 0; i < sales.length; i++) {
    total = total + sales[i];
}
console.log(total);
```

Expected output:

```text
42
```

Read the program in stages. `const sales` gives the input a descriptive name. `let total = 0` creates a variable whose value can change. Zero is the starting value because no sales have been counted yet. The loop header selects the indexes, while its body performs the repeated addition.

The expression `total + sales[i]` combines two operations: reading an array element and adding it to the previous total. At index 1, `sales[i]` becomes `sales[1]`, which is 5. The old total is 12. The right side becomes `12 + 5`, or 17. Assignment then stores 17 in `total`. The old value is replaced, not stored alongside the new value.

| Index | Element | Total before | Total after |
|---|---|---|---|
| 0 | 12 | 0 | 12 |
| 1 | 5 | 12 | 17 |
| 2 | 18 | 17 | 35 |
| 3 | 7 | 35 | 42 |

The shorthand `total += sales[i]` has the same effect here. Use the expanded form while learning if it helps you see the old state and new state.

The result is correct because after each iteration, total contains the sum of exactly the elements already visited. Before the loop this is the sum of zero elements. Each iteration adds one previously unvisited element. When the loop ends, all elements have been visited. This is an example of a loop invariant: a statement that remains true at a chosen point in every iteration.

## Choosing and initializing state

State is information retained by a program as it executes. A counter retains how many events occurred. An accumulator retains a combined value. A flag retains whether a condition has occurred. A pointer retains a position. Choosing state is about identifying the information needed for the next decision or the final result.

### Counting is not summation

Problem: count sales of at least 10 units in `[12, 5, 18, 7]`. The expected result is 2, not 30. We need a counter, not a sum of the qualifying values.

```javascript
const sales = [12, 5, 18, 7];
let count = 0;
for (let i = 0; i < sales.length; i++) {
    const sale = sales[i];
    if (sale >= 10) {
        count = count + 1;
    }
}
console.log(count);
```

Expected output:

```text
2
```

`sale` is a temporary variable created for the current iteration. At index 2 it holds 18. `sale >= 10` evaluates to true because 18 is greater than 10. The comparison includes equality: a sale of exactly 10 also qualifies. The assignment adds one to the old count because one additional qualifying event was found. If the comparison is false, the body of the `if` is skipped and count retains its previous value.

A common error is `count += sale`. That calculates the sum of qualifying values rather than their number. Another is declaring `let count = 0` inside the loop: the counter would restart on every iteration and would not be available outside its block.

### Choosing a starting value for an extremum

Problem: find the greatest number in `[-8, -3, -12]`. Return `undefined` for an empty array. Starting the maximum at zero is wrong: zero is not in the input and no negative value can replace it.

Instead, use the first element as the first candidate. The remaining elements compete with that candidate.

```javascript
function greatest(numbers) {
    if (numbers.length === 0) {
        return undefined;
    }
    let best = numbers[0];
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > best) {
            best = numbers[i];
        }
    }
    return best;
}
console.log(greatest([-8, -3, -12]));
console.log(greatest([]));
console.log(greatest([4]));
```

Expected output:

```text
-3
undefined
4
```

The function parameter `numbers` receives the input array. The initial condition `numbers.length === 0` compares its length with zero using strict equality. `return undefined` ends the function before any element is read. For nonempty input, `numbers[0]` is a valid candidate. The loop starts at 1 because index 0 has already been considered.

At index 1, `numbers[i] > best` becomes `-3 > -8`, which is true. Assignment replaces -8 with -3. At index 2 the comparison is `-12 > -3`, false, so the record is preserved. With a single element the loop condition is immediately false; the initial candidate is returned.

If the problem asks for the position rather than the value, store `bestIndex` instead. Compare `numbers[i]` with `numbers[bestIndex]`, and update the index when a better value appears. This avoids maintaining two variables that could become inconsistent. Strict `>` preserves the first tied maximum; `>=` selects the last. Choose according to the requirements.

## Selecting boundaries and update rules

A boundary determines which candidates are visited. An update determines how the loop advances. Both must follow the problem, not habit.

### Comparing adjacent elements

Problem: count how many times a temperature increases from one reading to the next. Input `[20, 22, 22, 19, 24]` should produce 2. Equal readings do not qualify. Empty and single-element inputs produce zero.

The pairs are (20,22), (22,22), (22,19), (19,24). There are four pairs, not five. Each pair has a current element and a previous element. Start the current index at 1.

```javascript
const readings = [20, 22, 22, 19, 24];
let increases = 0;
for (let i = 1; i < readings.length; i++) {
    const previous = readings[i - 1];
    const current = readings[i];
    if (current > previous) {
        increases++;
    }
}
console.log(increases);
```

Expected output:

```text
2
```

At `i = 3`, `i - 1` is 2, so `readings[i - 1]` reads 22. `readings[i]` reads 19. The condition becomes `19 > 22`, false. The count remains 1. The update `i++` advances the current index by one. It does not change any array element.

Starting at zero would read `readings[-1]`, normally undefined for an ordinary array. Using `i <= readings.length` would read one position beyond the end. Both violate the intended pair boundaries even if a particular comparison happens to produce the expected count.

An alternative design uses `i` for the left member and `i + 1` for the right member. The condition must then be `i < readings.length - 1`. For length 5, the rightmost valid left index is 3. Both designs visit the same pairs.

### Visiting disjoint pairs

Suppose instead that the readings are organized into nonoverlapping pairs. Calculate the sum of each complete pair in `[3, 8, 2, 4, 9]`, ignoring the unmatched final element. Expected result: `[11, 6]`.

```javascript
const values = [3, 8, 2, 4, 9];
const totals = [];
for (let i = 0; i + 1 < values.length; i += 2) {
    const pairTotal = values[i] + values[i + 1];
    totals.push(pairTotal);
}
console.log(totals.join(' '));
```

Expected output:

```text
11 6
```

The condition `i + 1 < values.length` first calculates the right member's index and then checks that it exists. At i = 4, the right index is 5; `5 < 5` is false. The body does not execute. The update `i += 2` means `i = i + 2`, selecting the next disjoint pair. An increment of one would produce overlapping pairs instead. `push` appends the calculated total to the result array; `join(' ')` produces a display string with spaces between values without modifying the array.

## Designing a search

Searching requires a clear success condition and a representation of failure. Suppose we need the index of the first price greater than a budget of 50 in `[20, 50, 65, 80]`. The expected answer is 2. Return -1 if none qualifies. Since valid indexes are nonnegative, -1 is an unambiguous failure marker.

```javascript
function firstOverBudget(prices, budget) {
    for (let i = 0; i < prices.length; i++) {
        if (prices[i] > budget) {
            return i;
        }
    }
    return -1;
}
console.log(firstOverBudget([20, 50, 65, 80], 50));
console.log(firstOverBudget([20, 50], 50));
console.log(firstOverBudget([], 50));
```

Expected output:

```text
2
-1
-1
```

At index 0, 20 > 50 is false. At index 1, 50 > 50 is false because the requirement is strictly greater. At index 2, 65 > 50 is true. `return i` ends the entire function, not merely the current iteration. Index 3 is never examined. The final `return -1` is reached only if every candidate fails or there are no candidates.

If we wanted the last qualifying index, we could traverse backwards and return the first match encountered in that direction. Alternatively, scan forward and replace a stored index on each match. The forward version is useful when other information must also be collected. Early termination is appropriate only when later elements cannot affect the required result.

## Tracking changing state

Some problems require remembering information about a sequence, not just combining independent elements. Consider daily attendance `[1, 1, 0, 1, 1, 1, 0]`, where 1 means present and 0 absent. Find the longest uninterrupted attendance streak. The answer is 3. Empty input should return zero.

Manually, the first two days form a streak of two. The absence ends that streak. The next three present days form a streak of three. We need both the current streak and the best streak ever seen. These are different: resetting the current streak must not erase the record.

```javascript
const attendance = [1, 1, 0, 1, 1, 1, 0];
let currentStreak = 0;
let bestStreak = 0;
for (let i = 0; i < attendance.length; i++) {
    if (attendance[i] === 1) {
        currentStreak++;
    } else {
        currentStreak = 0;
    }
    if (currentStreak > bestStreak) {
        bestStreak = currentStreak;
    }
}
console.log(bestStreak);
```

Expected output:

```text
3
```

Both variables begin at zero because no days have been visited. The strict comparison `attendance[i] === 1` identifies a present day. If true, increment the current streak. Otherwise, reset it to zero because an absent day is not part of a present streak. This differs from increasing-number runs, where the element that breaks a run starts a new run of length one.

| Day index | Attendance | Current streak | Best streak |
|---|---|---|---|
| 0 | 1 | 1 | 1 |
| 1 | 1 | 2 | 2 |
| 2 | 0 | 0 | 2 |
| 3 | 1 | 1 | 2 |
| 4 | 1 | 2 | 2 |
| 5 | 1 | 3 | 3 |
| 6 | 0 | 0 | 3 |

The second condition must be evaluated after the current streak is updated. Checking before the update can miss a record on the final day. Resetting `bestStreak` on an absence would forget earlier records. Updating the best after each day also handles a streak that reaches the end without requiring a special finalization step.

## Designing a terminating loop

When the number of iterations depends on changing state, a while loop can express the stopping rule directly.

Problem: a tank contains 3 liters. Add 4 liters per operation until it contains at least 14 liters. Report the number of operations and final volume. Assume exact small integer quantities and a positive addition. The final volume may exceed the target.

```javascript
let volume = 3;
const target = 14;
const addition = 4;
let operations = 0;
while (volume < target) {
    volume += addition;
    operations++;
}
console.log(operations, volume);
```

Expected output:

```text
3 15
```

`volume < target` checks whether another operation is needed. Initially `3 < 14` is true. `volume += addition` calculates `3 + 4` and stores 7. `operations++` records the completed operation. The next volumes are 11 and 15. When the condition is checked again, `15 < 14` is false. The loop ends.

| Operations | Volume | Another operation needed? |
|---|---|---|
| 0 | 3 | Yes |
| 1 | 7 | Yes |
| 2 | 11 | Yes |
| 3 | 15 | No |

Using `volume !== target` would not terminate for these values: the sequence passes 14 without reaching it. The requirement is at least, not exactly. A zero or negative addition cannot guarantee progress when the starting volume is below the target. With general numeric inputs, validate finiteness and consider precision as well; the example assumes small integers.

A termination argument identifies what changes toward the stopping condition. Here, the remaining distance `target - volume` decreases by four each iteration until it is zero or negative. This argument relies on the positive step and exact arithmetic assumptions.

## Combining state variables

Problem: for `[8, -2, 12, 0, 4]`, report the number, sum and average of strictly positive values. If none qualifies, the average should be undefined. The expected count is 3, sum 24, average 8.

First decide which values qualify. Then count them and accumulate their sum. The average is derived from these two quantities after traversal. There is no need to repeatedly calculate an average that will be replaced.

```javascript
function positiveReport(values) {
    let count = 0;
    let sum = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value > 0) {
            count++;
            sum += value;
        }
    }
    let average;
    if (count > 0) {
        average = sum / count;
    }
    return { count: count, sum: sum, average: average };
}
const report = positiveReport([8, -2, 12, 0, 4]);
console.log(report.count, report.sum, report.average);
const emptyReport = positiveReport([-2, 0]);
console.log(emptyReport.count, emptyReport.sum, emptyReport.average);
```

Expected output:

```text
3 24 8
0 0 undefined
```

`let average;` declares a variable initially holding undefined. `count > 0` ensures the division has a nonzero denominator. `sum / count` becomes `24 / 3`, or 8. Dividing by `values.length` would incorrectly include excluded values in the denominator. The returned object groups three related results: the property `count` receives the current value of the variable count, and similarly for sum and average. `report.count` reads that property.

The count and sum are updated within the same condition because they describe the same subset. If count were incremented outside the condition, the sum would still be correct but the average would be wrong. After each iteration, count and sum describe exactly the positive elements in the processed prefix. This shared invariant helps us verify that the two variables remain consistent.

## Independent practice

Attempt these before reading the solutions. Assume dense arrays of finite small integers unless otherwise specified.

1. Count values between 10 and 20 inclusive. Input `[9, 10, 15, 20, 21]` should return 3; an empty array should return 0. Explain the condition and initialization.
2. Find the index of the last smallest value. Input `[4, -2, 7, -2]` should return 3; empty input should return -1. Explain why the comparison includes equality.
3. Find the greatest absolute difference between adjacent values. Input `[4, 9, 2, 6]` should return 7. Arrays shorter than two elements should return 0. Explain the boundaries and absolute value operation.
4. Find how many deposits of 5 units are needed to reach at least 23 from 8. Expected answer: 3. A starting balance of 25 should require zero deposits. Explain why equality is not the stopping rule.
5. Find the longest uninterrupted sequence of values greater than 10. Input `[12, 15, 8, 11, 13, 20, 2]` should return 3. Empty input and `[10, 8]` should return zero. Explain the difference between current and best state.
6. Report the count and sum of even numbers, and the count and sum of odd numbers. Input `[2, 3, -4, 7, 0]` should give even count 3, even sum -2, odd count 2, odd sum 10. Include zero and negative integers correctly.

## Worked solutions

### Exercise 1 - Inclusive range counting

The two boundaries must both hold for the same element. Initialize a counter to zero because no qualifying elements have been seen.

```javascript
function countInRange(values) {
    let count = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value >= 10 && value <= 20) {
            count++;
        }
    }
    return count;
}
console.log(countInRange([9, 10, 15, 20, 21]));
console.log(countInRange([]));
```

Expected output:

```text
3
0
```

For value 20, the left comparison `20 >= 10` is true, and the right comparison `20 <= 20` is true. Logical AND (`&&`) requires both, so count increases. For 21 the right comparison is false. For 9 the left comparison is false and JavaScript short-circuits without evaluating the right comparison. Using OR instead would accept every finite number because any number is either at least 10 or at most 20. The loop visits all elements because every qualifying element must be counted.

### Exercise 2 - Last smallest index

Store an index rather than a value because the required output is a position. Start with index zero after guarding against empty input.

```javascript
function lastSmallestIndex(values) {
    if (values.length === 0) {
        return -1;
    }
    let bestIndex = 0;
    for (let i = 1; i < values.length; i++) {
        if (values[i] <= values[bestIndex]) {
            bestIndex = i;
        }
    }
    return bestIndex;
}
console.log(lastSmallestIndex([4, -2, 7, -2]));
console.log(lastSmallestIndex([]));
console.log(lastSmallestIndex([6]));
```

Expected output:

```text
3
-1
0
```

At index 1, `values[1] <= values[0]` becomes `-2 <= 4`, true. The stored index becomes 1. At index 2, `7 <= -2` is false. At index 3, `-2 <= -2` is true, so index 3 replaces index 1. The assignment stores the index, not the number -2. Using strict `<` would preserve the first minimum instead. The single-element case returns zero without entering the loop.

### Exercise 3 - Greatest adjacent difference

The differences in the input are 5, -7 and 4. Their magnitudes are 5, 7 and 4. We need the greatest magnitude, not the greatest signed difference. `Math.abs(x)` returns the absolute value of x, removing a negative sign if present.

```javascript
function greatestChange(values) {
    let best = 0;
    for (let i = 1; i < values.length; i++) {
        const difference = values[i] - values[i - 1];
        const magnitude = Math.abs(difference);
        if (magnitude > best) {
            best = magnitude;
        }
    }
    return best;
}
console.log(greatestChange([4, 9, 2, 6]));
console.log(greatestChange([4]));
console.log(greatestChange([]));
```

Expected output:

```text
7
0
0
```

At index 2, the previous index is `2 - 1 = 1`. The current value is 2 and previous value is 9. Subtraction gives -7. `Math.abs(-7)` gives 7. Since 7 exceeds the previous best of 5, best becomes 7. Zero is a valid initial maximum here because magnitudes cannot be negative and the contract defines zero for no pairs. This is unlike a general maximum of signed numbers. The loop starts at 1 to guarantee a valid previous index.

### Exercise 4 - Deposits to a target

Use a condition-controlled loop because the stopping rule depends on the balance. Count each completed deposit.

```javascript
function depositsNeeded(start) {
    let balance = start;
    let count = 0;
    while (balance < 23) {
        balance += 5;
        count++;
    }
    return count;
}
console.log(depositsNeeded(8));
console.log(depositsNeeded(25));
console.log(depositsNeeded(23));
```

Expected output:

```text
3
0
0
```

The balances are 8, 13, 18 and 23. The condition becomes false at 23. Starting at 25 or 23 makes the first condition false, so no deposit is made. A `do...while` without an initial guard would incorrectly make one deposit. Equality alone is not sufficient: a start of 9 would reach 24 after three deposits, never exactly 23. The fixed positive step guarantees progress under the stated small-integer assumptions.

### Exercise 5 - Longest qualifying run

A qualifying value extends the current run. A nonqualifying value ends it. The best run must survive the reset.

```javascript
function longestHighRun(values) {
    let current = 0;
    let best = 0;
    for (let i = 0; i < values.length; i++) {
        if (values[i] > 10) {
            current++;
        } else {
            current = 0;
        }
        if (current > best) {
            best = current;
        }
    }
    return best;
}
console.log(longestHighRun([12, 15, 8, 11, 13, 20, 2]));
console.log(longestHighRun([]));
console.log(longestHighRun([10, 8]));
console.log(longestHighRun([11, 12]));
```

Expected output:

```text
3
0
0
2
```

| Value | Current | Best |
|---|---|---|
| 12 | 1 | 1 |
| 15 | 2 | 2 |
| 8 | 0 | 2 |
| 11 | 1 | 2 |
| 13 | 2 | 2 |
| 20 | 3 | 3 |
| 2 | 0 | 3 |

The expression `values[i] > 10` excludes exactly 10. At 8, the false branch resets current to zero; the best remains two. At 20, current increments from two to three. The second comparison becomes `3 > 2`, so best is updated. Checking after each update ensures a run ending at the last element is included. Counting all qualifying values without resetting would produce five, which is not the longest uninterrupted run.

### Exercise 6 - Partitioned statistics

Every integer belongs to exactly one of the even and odd categories. Maintain a count and sum for each category. Remainder `%` returns the remainder of division; an integer is even if its remainder on division by two is zero. Negative even integers can produce negative zero, which compares equal to zero using strict equality.

```javascript
const values = [2, 3, -4, 7, 0];
let evenCount = 0;
let evenSum = 0;
let oddCount = 0;
let oddSum = 0;
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value % 2 === 0) {
        evenCount++;
        evenSum += value;
    } else {
        oddCount++;
        oddSum += value;
    }
}
console.log(evenCount, evenSum);
console.log(oddCount, oddSum);
```

Expected output:

```text
3 -2
2 10
```

At value -4, the expression first calculates `-4 % 2`, which is negative zero. Comparing it with zero gives true. The even count becomes two and the even sum becomes `2 + (-4)`, or -2. At zero, the even count increases but the even sum remains unchanged. This demonstrates why count and sum are distinct. At 7, the remainder is 1, so the else branch adds one to oddCount and seven to oddSum. Avoid testing oddness with `value % 2 === 1` because negative odd integers have remainder -1. The else branch is valid here because the inputs are guaranteed integers.

As a consistency check, evenCount + oddCount equals the input length (5). The two sums add to the total of the input (8). These checks are useful but do not alone prove that every value was classified correctly.

## Summary

A loop design follows from the requirements. Determine the repeated action, the information that must persist, the starting state, the candidates to visit, the update rules and the stopping condition. Trace a small input before implementing a complex solution. Check that every update preserves the intended meaning of the state and that every iteration makes appropriate progress. Select initial values according to the contract, not a memorized pattern. Separate current state from historical records. Use a clear failure representation for searches. Test empty inputs, single elements, ties and exact boundaries where applicable.

[Previous: Understanding the Problem](27-part-01-understanding-the-problem.md) | [Next: Recognizing and Combining Loop Patterns (planned)](27-part-03-recognizing-and-combining-loop-patterns.md)
