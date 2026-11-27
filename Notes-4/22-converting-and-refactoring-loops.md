# Lesson 22 - Converting and Refactoring Loops

[Previous: Advanced Loop Techniques](21-advanced-loop-techniques.md) | [Contents](#table-of-contents) | Next: Loop Correctness and Edge Cases (planned)

A working loop is not necessarily the clearest or most suitable implementation of a solution. Sometimes, changing its structure makes the code easier to understand, maintain or execute.

Refactoring means changing the internal structure of existing code without changing its intended observable behavior. Observable behavior includes returned results, printed messages, changes to existing objects and arrays, and whether execution terminates or throws an error.

## Table of contents

- [1. Establishing equivalence](#1-establishing-equivalence)
- [2. Converting between loop types](#2-converting-between-loop-types)
- [3. Simplifying loop bodies](#3-simplifying-loop-bodies)
- [4. Combining and splitting loops](#4-combining-and-splitting-loops)
- [5. Changing nested loop order](#5-changing-nested-loop-order)
- [6. Flattening nested loops](#6-flattening-nested-loops)
- [7. Integrated exercises](#7-integrated-exercises)
- [8. Review](#8-review)

## 1. Establishing equivalence

By the end of this chapter, you should be able to convert traditional loops, simplify their control flow, fuse and split traversals, interchange nested loops and replace nested traversal with a single index. You should also be able to decide when such changes are unsafe.

Recall initialization, pre-test and post-test conditions, updates, break, continue, block scope, array indexing, nested loops and accumulators. We will combine these established concepts rather than introduce new iteration APIs.

Consider summing positive values. A useful refactoring must preserve which values qualify, the order in which they are added, and the initial total. Merely obtaining the same answer for one array is insufficient. For example, changing `value > 0` to `value >= 0` preserves a numeric sum in many ordinary cases but changes a count of qualifying values. A change to the requirements is not a refactoring.

Before changing a loop, identify:

- The initial state and variables visible outside the loop.
- The sequence of candidates visited and the condition for stopping.
- The operations performed on each candidate, including skipped candidates.
- The order of observable effects and dependencies between operations.
- The result for empty, single-element and boundary inputs.

A dependency exists when an operation needs a value produced by another operation. For example, a running total depends on its previous value. Preserving the final total alone does not necessarily preserve all intermediate totals.

## 2. Converting between loop types

### From for to while

Suppose we need to print the integers 2, 4 and 6. A `for` loop puts initialization, condition and update in one header. To convert it, move initialization before the `while`, keep the condition and place the update after the body.

```javascript
for (let i = 2; i <= 6; i += 2) {
  console.log(i);
}
{
  let i = 2;
  while (i <= 6) {
    console.log(i);
    i += 2;
  }
}
```

Expected output:

```text
2
4
6
2
4
6
```

The extra block around the while version limits the visibility of `i`. Without it, `i` would remain accessible after the loop in the surrounding block, unlike the variable declared in the for header.

The execution sequence is initialization once, condition, body, update, condition again. At i = 6, the body runs and the update makes i = 8. The next condition is false. Moving the update before the print would change the sequence to 4, 6, 8.

### The continue trap

In a for loop, continue transfers control to the update expression before the next condition check. In a while loop, continue transfers control directly to the condition check. A mechanical conversion can therefore become infinite.

Consider printing only odd values from 0 through 4. This incorrect conversion gets stuck at zero:

```js
let i = 0;
while (i < 5) {
  if (i % 2 === 0) continue;
  console.log(i);
  i++;
}
```

Zero satisfies the skip condition. The update never executes, so zero remains the current value forever. One correction is to perform the update on every path that continues. Another is to avoid continue by expressing the operation positively.

```javascript
for (let i = 0; i < 5; i++) {
  if (i % 2 === 0) continue;
  console.log(i);
}
let i = 0;
while (i < 5) {
  if (i % 2 !== 0) {
    console.log(i);
  }
  i++;
}
```

Expected output:

```text
1
3
1
3
```

The expression `i % 2 !== 0` first computes the remainder after division by two, then checks whether it differs from zero. The update is outside the conditional block, so both qualifying and nonqualifying values advance the loop.

If the original body contains break, do not move the update before that break: the original for loop does not execute its update when break exits the loop. Similarly, return and throw exit without the ordinary update.

A more subtle case involves closures (functions retaining access to their surrounding variables). A for loop with a let declaration creates a fresh binding for each iteration. A while loop with one outer let variable does not. The following demonstrates why matching numeric traversal is not always enough.

```javascript
const first = [];
for (let i = 0; i < 3; i++) {
  first.push(() => i);
}
const second = [];
let j = 0;
while (j < 3) {
  const current = j;
  second.push(() => current);
  j++;
}
console.log(first[0](), first[1](), first[2]());
console.log(second[0](), second[1](), second[2]());
```

Expected output:

```text
0 1 2
0 1 2
```

The arrow function `() => current` takes no arguments and returns the captured value when called later. Declaring current inside the body creates a fresh binding each time. Capturing j instead would return 3 for every call. This repair works for this read-only capture; it is not a general conversion rule for closures that mutate loop bindings.

### From while to for

A while loop may have no separate counter. Suppose a balance of 100 is repeatedly reduced by 30 until it is no greater than zero. We want the number of payments and the final balance.

```javascript
let balance = 100;
let payments = 0;
for (; balance > 0; ) {
  balance -= 30;
  payments++;
}
console.log(payments, balance);
```

Expected output:

```text
4 -20
```

The empty initialization and update fields are legal. The two semicolons still separate the three fields. The state changes from 100 to 70, 40, 10 and -20. Moving `balance -= 30` into the update field would be possible here, but separating it from the payment count may make the relationship less clear. A while loop is usually more natural for this requirement. Conversion is not automatically improvement.

### Converting do...while

A post-test loop always enters its body once. A pre-test loop may enter zero times. Suppose a machine must attempt an operation at least once, even when its starting value already meets a stopping condition. To preserve this behavior, a while loop can use an unconditional condition and an explicit break after the body.

```javascript
let a = 5;
do {
  console.log(a);
  a++;
} while (a < 3);
let b = 5;
while (true) {
  console.log(b);
  b++;
  if (!(b < 3)) break;
}
```

Expected output:

```text
5
5
```

The negation reverses the continuation condition: if b is not less than 3, exit. If the original do...while has continue statements, they must still reach the post-test. Simply retaining continue in the while(true) version would skip the explicit break check. One possible repair is to restructure the body into conditional branches that all reach the test.

Conversely, converting a pre-test loop to do...while requires an initial guard if zero iterations are possible. The guard checks whether the original loop would have entered.

```javascript
const values = [];
let i = 0;
if (i < values.length) {
  do {
    console.log(values[i]);
    i++;
  } while (i < values.length);
}
console.log('finished', i);
```

Expected output:

```text
finished 0
```

Without the guard, the loop would read values[0], which is undefined, and increment i incorrectly.

### Practice: conversion with an early exit

Convert the following for loop to while. It visits [2, -1, 4, 0, 8], skips negative numbers, stops at zero and sums the remaining visited values. Expected total: 6. The final index should be 3.

```js
for (; i < values.length; i++) {
  if (values[i] === 0) break;
  if (values[i] < 0) continue;
  total += values[i];
}
```

#### Worked solution

The update must run for negative values but not for the zero sentinel. Keep the sentinel check first, conditionally add nonnegative values, then update unconditionally on the remaining paths.

```javascript
const values = [2, -1, 4, 0, 8];
let i = 0;
let total = 0;
while (i < values.length) {
  if (values[i] === 0) break;
  if (values[i] >= 0) total += values[i];
  i++;
}
console.log(total, i);
```

Expected output:

```text
6 3
```

At index 1 the total remains 2 but i advances. At index 2 the total becomes 6. At index 3 break prevents both addition and increment. An empty array leaves both variables at zero. This assumes ordinary numeric elements; NaN would require preserving the original negative-test logic exactly, using `!(values[i] < 0)` rather than `>= 0`.

## 3. Simplifying loop bodies

### Replace unnecessary branches with direct operations

Suppose we count passing scores (at least 50) and failing scores. The original might contain an if inside another if, even though each score only belongs to one category. An if/else expresses that exclusivity directly.

```javascript
const scores = [40, 50, 80, 20];
let passed = 0;
let failed = 0;
for (let i = 0; i < scores.length; i++) {
  if (scores[i] >= 50) passed++;
  else failed++;
}
console.log(passed, failed);
```

Expected output:

```text
2 2
```

This is equivalent to two independent comparisons `>= 50` and `< 50` only for values for which one of those comparisons must be true. NaN satisfies neither. Decide the input contract before replacing independent tests with else.

### Guard clauses and reduced nesting

A guard clause handles a condition early, avoiding deeper nesting in the rest of the body. Suppose we sum positive even numbers. We can skip nonpositive values, then skip odd values. This makes the successful path easy to read.

```javascript
const values = [-4, 0, 3, 6, 8];
let total = 0;
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value <= 0) continue;
  if (value % 2 !== 0) continue;
  total += value;
}
console.log(total);
```

Expected output:

```text
14
```

The first guard excludes -4 and 0. The second excludes 3. Only 6 and 8 reach the addition. For ordinary finite numbers this matches `if (value > 0 && value % 2 === 0)`. Prefer the compound condition when the whole operation is short; guards become useful when the successful path contains several statements.

Do not introduce a continue if the skipped path originally performed necessary cleanup or state updates later in the body.

### Extract repeated operations and invariant calculations

A loop-invariant expression has the same value on every iteration under the stated assumptions. Suppose a fixed tax rate of 10 percent applies to every price. The multiplier 1 + rate can be computed once outside the loop.

```javascript
const prices = [100, 200, 300];
const rate = 0.1;
const multiplier = 1 + rate;
let total = 0;
for (let i = 0; i < prices.length; i++) {
  total += prices[i] * multiplier;
}
console.log(total.toFixed(2));
```

Expected output:

```text
660.00
```

`toFixed(2)` returns a string displaying a number rounded to two decimal places. It is used here only for output, not to make floating-point arithmetic exact. The multiplier is 1.1, and each price is multiplied before addition. Moving the multiplier outside the loop is safe because rate never changes. Moving the multiplication itself after the summation changes the order of floating-point operations and may change rounding. Also, moving an expression that throws or has side effects outside a loop can change behavior for empty inputs.

### Simplify state without losing information

Suppose we need the index of the first negative value, or -1 if none exists. A separate found flag is redundant because the index already represents success or failure.

```javascript
const values = [4, 2, -3, -5];
let position = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] < 0) {
    position = i;
    break;
  }
}
console.log(position, position !== -1);
```

Expected output:

```text
2 true
```

The sentinel -1 cannot be a valid array index in this traversal. Do not use `if (position)` to test success: index zero is falsy and -1 is truthy. Removing a flag is appropriate only if all of its information can be recovered from the remaining state.

### Practice: simplify a nested body

For [3, -2, 6, 0, 9], collect positive multiples of three and count them. Expected array: [3, 6, 9], count: 3. Remove the separate counter when possible.

#### Worked solution

The result array grows by exactly one for each qualifying value. Its length therefore represents the count. Combine the conditions using &&, which evaluates the second condition only when the first is truthy.

```javascript
const values = [3, -2, 6, 0, 9];
const result = [];
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value > 0 && value % 3 === 0) result.push(value);
}
console.log(result.join(' '));
console.log(result.length);
```

Expected output:

```text
3 6 9
3
```

The length starts at zero and increases after each push. It remains correct for empty input. If the result array already contained values, its length would not represent just the newly collected count; the initial length would need to be subtracted.

## 4. Combining and splitting loops

### Loop fusion: one traversal, several operations

Suppose a report needs the sum and maximum of a nonempty array. Separate loops are easy to understand, but both visit exactly the same indices. Fusion combines them into one traversal.

```javascript
const values = [4, 9, 2, 7];
let total = 0;
let maximum = values[0];
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  total += value;
  if (value > maximum) maximum = value;
}
console.log(total, maximum);
```

Expected output:

```text
22 9
```

| Value | Total | Maximum |
|---|---:|---:|
| Initial | 0 | 4 |
| 4 | 4 | 4 |
| 9 | 13 | 9 |
| 2 | 15 | 9 |
| 7 | 22 | 9 |

Each accumulator depends only on its own previous state and the current value. Interleaving their updates does not change either result. For n values, two separate loops perform 2n visits; the fused loop performs n visits, but still performs both operations per value. Both have linear growth in work. A speed improvement is not guaranteed without measurement. For empty input, define maximum explicitly, for example undefined, rather than inventing a numeric maximum.

### When fusion changes behavior

Suppose the first loop doubles every value and the second calculates adjacent sums from the fully transformed array. Fusing them naively reads a neighbor before it has been doubled.

```javascript
const a = [1, 2, 3];
const correct = [];
for (let i = 0; i < a.length; i++) a[i] *= 2;
for (let i = 0; i + 1 < a.length; i++) correct.push(a[i] + a[i + 1]);
const b = [1, 2, 3];
const wrong = [];
for (let i = 0; i < b.length; i++) {
  b[i] *= 2;
  if (i + 1 < b.length) wrong.push(b[i] + b[i + 1]);
}
console.log(correct.join(' '));
console.log(wrong.join(' '));
```

Expected output:

```text
6 10
4 7
```

At i = 0 the fused version adds 2 + 2 instead of 2 + 4. To repair it, compute each adjacent sum when the second member has been transformed. The previous member is already ready.

```javascript
const values = [1, 2, 3];
const sums = [];
for (let i = 0; i < values.length; i++) {
  values[i] *= 2;
  if (i > 0) sums.push(values[i - 1] + values[i]);
}
console.log(sums.join(' '));
```

Expected output:

```text
6 10
```

At i = 1 both values[0] and values[1] have been doubled. At i = 2 the next pair is ready. Empty and single-element inputs produce no pairs. This repair preserves final arrays for ordinary numeric inputs, but interleaves mutations and result construction differently. If those intermediate effects are observable, further analysis is needed.

### Loop fission: separating responsibilities

Fission splits one loop into multiple traversals. It can clarify stages, isolate dependencies or make each stage independently testable. Suppose we transform temperatures from Celsius to Fahrenheit and count converted readings above 80. The formula is `c * 9 / 5 + 32`.

```javascript
const celsius = [0, 20, 30];
const fahrenheit = [];
for (let i = 0; i < celsius.length; i++) {
  fahrenheit.push(celsius[i] * 9 / 5 + 32);
}
let hot = 0;
for (let i = 0; i < fahrenheit.length; i++) {
  if (fahrenheit[i] > 80) hot++;
}
console.log(fahrenheit.join(' '));
console.log(hot);
```

Expected output:

```text
32 68 86
1
```

The first stage establishes the entire converted array. The second stage depends on that array but does not modify it. If only the count were needed, a fused loop could avoid storing converted readings. Here the array is itself a required result, so splitting does not require an additional array beyond the output.

Fission is unsafe when operations rely on interleaved state. For example, adding a deposit then subtracting a fee on each day and printing the daily balance cannot be split into all deposits followed by all fees without changing the daily balances. Even if the final balance agrees, the printed history differs.

### Practice: fuse two independent reports

For [5, -2, 0, 7, -4], calculate the positive sum and negative count. Expected: 12 and 2. Use one traversal and explain why fusion is safe.

#### Worked solution

The two results depend only on their respective previous values and the current number. Zero belongs to neither category. Neither operation changes the input or the other accumulator.

```javascript
const values = [5, -2, 0, 7, -4];
let positiveSum = 0;
let negativeCount = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) positiveSum += values[i];
  if (values[i] < 0) negativeCount++;
}
console.log(positiveSum, negativeCount);
```

Expected output:

```text
12 2
```

The positive sum evolves 5, 5, 5, 12, 12. The count evolves 0, 1, 1, 1, 2. Empty input produces 0 and 0. The order of additions is unchanged.

## 5. Changing nested loop order

### Rectangular traversal

Loop interchange swaps the roles of outer and inner loops. Consider a grid of two rows and three columns. Row-major traversal completes one row before moving to the next. Column-major traversal completes one column before moving to the next.

```javascript
const grid = [[1, 2, 3], [4, 5, 6]];
const byRow = [];
const byColumn = [];
for (let r = 0; r < 2; r++) {
  for (let c = 0; c < 3; c++) byRow.push(grid[r][c]);
}
for (let c = 0; c < 3; c++) {
  for (let r = 0; r < 2; r++) byColumn.push(grid[r][c]);
}
console.log(byRow.join(' '));
console.log(byColumn.join(' '));
```

Expected output:

```text
1 2 3 4 5 6
1 4 2 5 3 6
```

Both visit six cells exactly once, but the order differs. If the goal is to set every cell to zero independently, either order is suitable. If the goal is to construct a row-major list, interchange changes the required result. Even a floating-point sum can differ because the addition order changes.

### Dependent boundaries

Consider all unique pairs of indices from four elements, with i < j. The original inner loop starts at i + 1. If j becomes the outer index, we must derive the new bounds rather than merely swap headers.

For each j, valid i values satisfy 0 <= i < j. The first possible j is 1 and the last is 3. This visits the same pairs in a different order.

```javascript
const original = [];
const changed = [];
for (let i = 0; i < 4; i++) {
  for (let j = i + 1; j < 4; j++) original.push(`${i},${j}`);
}
for (let j = 1; j < 4; j++) {
  for (let i = 0; i < j; i++) changed.push(`${i},${j}`);
}
console.log(original.join(' '));
console.log(changed.join(' '));
```

Expected output:

```text
0,1 0,2 0,3 1,2 1,3 2,3
0,1 0,2 1,2 0,3 1,3 2,3
```

A template literal uses backticks and `${expression}` to insert evaluated values into a string. Here it represents each pair for inspection. Both loops satisfy i < j, but a search that stops at the first matching pair may return a different pair after interchange.

### Ragged arrays and dependencies

A ragged array contains rows of different lengths. A column-first traversal must determine the maximum row length and skip rows that lack the current column. Reading grid[0].length alone fails when later rows are longer or the grid is empty.

```javascript
const grid = [[1], [], [2, 3, 4]];
let columns = 0;
for (let r = 0; r < grid.length; r++) {
  if (grid[r].length > columns) columns = grid[r].length;
}
const result = [];
for (let c = 0; c < columns; c++) {
  for (let r = 0; r < grid.length; r++) {
    if (c < grid[r].length) result.push(grid[r][c]);
  }
}
console.log(result.join(' '));
```

Expected output:

```text
1 2 3 4
```

This assumes dense rows. The bounds check avoids nonexistent positions beyond each row's length, not holes within a sparse row. The extra checks can make column-first traversal more expensive for highly uneven rows.

When a cell depends on an earlier cell, interchange must preserve the dependency. A computation that needs the cell immediately above and the cell immediately left can use either ascending row-major or ascending column-major traversal because both predecessors are ready. A computation that needs the entire previous row may not permit column-major traversal. Check the actual dependency, not merely the shape of the loops.

### Practice: interchange a triangular traversal

Visit all coordinates satisfying 0 <= c <= r < 3, first by row and then by column. Expected row order: 0,0 1,0 1,1 2,0 2,1 2,2. Derive the column-first bounds.

#### Worked solution

For a fixed column c, the condition c <= r means r starts at c and ends at 2. The outer column ranges from 0 through 2. The same six coordinates are visited.

```javascript
const result = [];
for (let c = 0; c < 3; c++) {
  for (let r = c; r < 3; r++) result.push(`${r},${c}`);
}
console.log(result.join(' '));
```

Expected output:

```text
0,0 1,0 2,0 1,1 2,1 2,2
```

For c = 0 there are three rows; for c = 1 there are two; for c = 2 there is one. No coordinate violates c <= r. If order is part of the requirement, this is not an equivalent replacement.

## 6. Flattening nested loops

### Mapping a single index to a rectangular grid

Flattening replaces multiple nested loop counters with a single traversal counter. Suppose we have two rows of three cells and want to visit them in row-major order. There are six positions, numbered 0 through 5.

| Flat index k | Row | Column |
|---:|---:|---:|
| 0 | 0 | 0 |
| 1 | 0 | 1 |
| 2 | 0 | 2 |
| 3 | 1 | 0 |
| 4 | 1 | 1 |
| 5 | 1 | 2 |

Every three positions complete a row. Dividing k by the column count tells us how many rows have been passed. `Math.trunc` removes the fractional part. The remainder tells us the offset within the current row.

```javascript
const grid = [[10, 20, 30], [40, 50, 60]];
const rows = 2;
const columns = 3;
const result = [];
for (let k = 0; k < rows * columns; k++) {
  const r = Math.trunc(k / columns);
  const c = k % columns;
  result.push(grid[r][c]);
}
console.log(result.join(' '));
```

Expected output:

```text
10 20 30 40 50 60
```

At k = 4, 4 / 3 is approximately 1.333; truncation yields row 1. The remainder 4 % 3 is 1, yielding column 1. grid[1][1] is 50. The reverse mapping is `k = r * columns + c`: skip r complete rows, then advance c positions. For row 1, column 1, this gives 1 * 3 + 1 = 4.

The boundary is strict: k < rows * columns. Using <= would visit k = 6, yielding row 2, which does not exist. The dimensions must be nonnegative integers and the grid rectangular. If columns is zero, the loop has no iterations, so division by zero is avoided. For extremely large dimensions, integer precision must also be considered.

### Flattening without division

A single loop can maintain explicit row and column counters instead of deriving them from k. This is useful when we want to avoid repeated index arithmetic or make the transition between rows explicit.

```javascript
const grid = [[1, 2], [3, 4], [5, 6]];
let r = 0;
let c = 0;
const result = [];
while (r < grid.length) {
  result.push(grid[r][c]);
  c++;
  if (c === 2) {
    c = 0;
    r++;
  }
}
console.log(result.join(' '));
```

Expected output:

```text
1 2 3 4 5 6
```

The column counter advances after each visit. When it reaches 2, the current row is exhausted; reset it to zero and advance the row. This implementation assumes exactly two columns in every row. A nested loop is often clearer for ordinary rectangular traversal. Flattening is especially useful when a single position must be stored, resumed or divided into chunks.

### Flattening ragged traversal

For rows of different lengths, division by a fixed column count cannot locate the correct row. Instead, track the current row and column. Before visiting, check whether the current row is exhausted. If so, advance to the next row and reset the column.

```javascript
const grid = [[], [10, 20], [], [30], []];
let r = 0;
let c = 0;
const result = [];
while (r < grid.length) {
  if (c >= grid[r].length) {
    r++;
    c = 0;
    continue;
  }
  result.push(grid[r][c]);
  c++;
}
console.log(result.join(' '));
```

Expected output:

```text
10 20 30
```

Every iteration either visits an element and increments c, or advances r. The row counter never decreases. Empty rows are skipped, including leading and trailing ones. Checking the row boundary after reading instead would incorrectly collect undefined from empty rows. For R rows and E elements, this loop executes R + E iterations; it does not reduce the amount of work to merely R or E in all cases.

### Flattening and control flow

An unlabeled break in a nested loop exits only the innermost loop. In a flattened loop, break exits the entire traversal. For example, if each row is scanned until its first zero, flattening must explicitly advance to the next row on zero rather than break the whole loop. Similarly, a continue targeting the outer loop must be translated into a row transition. The original control-flow target matters more than the spelling of the statement.

### Practice: flatten column-major traversal

Visit [[1,2,3],[4,5,6]] using a single index in column-major order. Expected: 1 4 2 5 3 6.

#### Worked solution

Each column contains two rows. Divide by the row count to obtain the column and take the remainder to obtain the row. This reverses the roles used in row-major mapping.

```javascript
const grid = [[1, 2, 3], [4, 5, 6]];
const rows = 2;
const columns = 3;
const result = [];
for (let k = 0; k < rows * columns; k++) {
  const c = Math.trunc(k / rows);
  const r = k % rows;
  result.push(grid[r][c]);
}
console.log(result.join(' '));
```

Expected output:

```text
1 4 2 5 3 6
```

At k = 3, c = 1 and r = 1, so the value is 5. The inverse mapping is k = c * rows + r. A zero row count produces zero iterations. The mapping assumes a rectangular grid.

## 7. Integrated exercises

Attempt each problem before reading its worked solution.

### Exercise 1: preserve sentinel and skip behavior

Convert a for loop that scans [3, -2, 5, 0, 9], ignores negatives and stops at zero, into a while loop. Collect accepted values and their running totals. Expected accepted values: 3 5; running totals: 3 8. Preserve the final index 3.

#### Solution

Use one index, a persistent total and two output arrays. Check the sentinel before any other operation. Use a conditional block instead of continue so the update is reached for skipped negatives.

```javascript
const values = [3, -2, 5, 0, 9];
const accepted = [];
const totals = [];
let total = 0;
let i = 0;
while (i < values.length) {
  const value = values[i];
  if (value === 0) break;
  if (value >= 0) {
    accepted.push(value);
    total += value;
    totals.push(total);
  }
  i++;
}
console.log(accepted.join(' '));
console.log(totals.join(' '));
console.log(i);
```

Expected output:

```text
3 5
3 8
3
```

At index 0 the total becomes 3 and both arrays grow. Index 1 only advances. Index 2 adds 5 to the previous total. Index 3 exits. For an empty array both results are empty; for an array starting with zero the index remains zero. The numeric input contract excludes NaN.

### Exercise 2: diagnose unsafe fusion

A program first replaces each negative value with zero, then counts adjacent equal pairs. Input [-1, 0, -2, 3]. Expected transformed array: 0 0 0 3; equal pairs: 2. Fuse safely.

#### Solution

A pair can be checked only after both values have been transformed. Compare the current value with the previous one after transforming the current value. The first index has no predecessor.

```javascript
const values = [-1, 0, -2, 3];
let equalPairs = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] < 0) values[i] = 0;
  if (i > 0 && values[i - 1] === values[i]) equalPairs++;
}
console.log(values.join(' '));
console.log(equalPairs);
```

Expected output:

```text
0 0 0 3
2
```

At i = 1 the first pair is equal. At i = 2 the second pair becomes equal after replacement. At i = 3 the pair differs. Checking the next value instead would miss the second pair before -2 is replaced. Empty and singleton arrays have zero adjacent pairs.

### Exercise 3: separate a dependent calculation

Given [2, 4, 6], create cumulative totals and then find every position whose cumulative total is at least half the final total. Expected totals: 2 6 12; qualifying indices: 1 2. Explain why a single immediate qualification pass is unsuitable.

#### Solution

The threshold depends on the final total, which is not known during the first pass. Build the cumulative array first. Then calculate the threshold and scan the completed array. This is a deliberate two-stage design.

```javascript
const values = [2, 4, 6];
const cumulative = [];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
  cumulative.push(total);
}
const threshold = total / 2;
const positions = [];
for (let i = 0; i < cumulative.length; i++) {
  if (cumulative[i] >= threshold) positions.push(i);
}
console.log(cumulative.join(' '));
console.log(positions.join(' '));
```

Expected output:

```text
2 6 12
1 2
```

The threshold is 6. The first cumulative value is 2, which fails; 6 and 12 qualify. Using half the running total during the first pass would use a changing threshold and incorrectly accept index zero. Empty input yields no positions. An alternative is to compute the final sum first, then build and classify cumulative totals in a second pass.

### Exercise 4: preserve row-local break when flattening

For [[1,0,9],[],[2,3,0,8],[4]], collect values from each row until its first zero. Continue with the next row. Expected: 1 2 3 4. Use one while loop.

#### Solution

The row transition occurs in two cases: the current row is exhausted, or the current value is zero. Test exhaustion first to avoid accessing an invalid column. The || operator short-circuits: if the first condition is true, the second is not evaluated.

```javascript
const grid = [[1, 0, 9], [], [2, 3, 0, 8], [4]];
let r = 0;
let c = 0;
const result = [];
while (r < grid.length) {
  if (c >= grid[r].length || grid[r][c] === 0) {
    r++;
    c = 0;
    continue;
  }
  result.push(grid[r][c]);
  c++;
}
console.log(result.join(' '));
```

Expected output:

```text
1 2 3 4
```

The zero in row 0 advances directly to row 1, which is empty and immediately skipped. Row 2 contributes 2 and 3 before its sentinel. Row 3 contributes 4. A plain break at the first zero would incorrectly terminate the entire traversal. Each iteration either advances a column or advances a row, ensuring termination for finite dense rows.

### Exercise 5: choose whether to interchange

A search returns the first cell containing 7 in [[0,7],[7,0]]. Would column-major traversal preserve the result? Give both coordinates and explain.

#### Solution

Row-major visits (0,0) then (0,1), so it returns (0,1). Column-major visits (0,0) then (1,0), so it returns (1,0). The same set of candidates is visited only if the traversal runs to completion; early exit changes the actual visited set. Interchange is not safe when the required result is the first row-major match. It may be safe when any matching coordinate is acceptable, but that is a different specification.

## 8. Review

A conversion preserves initialization, tests, updates, control-flow targets and variable bindings. Simplification removes unnecessary complexity without changing the input contract. Fusion interleaves operations and requires dependency analysis; fission separates stages and may alter observable order. Interchange changes traversal order and may require new dependent bounds. Flattening maps coordinates to a single index or maintains explicit traversal state, and must preserve the meaning of break and continue.

When verifying a refactoring, compare outputs and mutations on normal, empty, singleton and boundary inputs. Trace skipped iterations and early exits. Check dependencies and side effects before assuming equivalence. A shorter loop is not necessarily clearer, and a single loop is not necessarily faster than nested loops performing the same number of visits.

[Previous: Advanced Loop Techniques](21-advanced-loop-techniques.md) | [Contents](#table-of-contents) | Next: Loop Correctness and Edge Cases (planned)
