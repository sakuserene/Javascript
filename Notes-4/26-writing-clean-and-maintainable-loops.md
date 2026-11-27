# Lesson 26 - Writing Clean and Maintainable Loops

[Previous: Lesson 25](25-loop-performance-and-optimization.md) | [Next: Lesson 27 (planned)](27-solving-problems-with-loops.md)

A maintainable loop communicates what it does, why its boundaries are correct and which state changes matter. Refactoring should preserve the intended behavior, including ordering, mutation and termination. Shorter code is not automatically clearer.

## Contents

- [Learning objectives](#learning-objectives)
- [Naming variables](#naming-variables)
- [Formatting and structure](#formatting-and-structure)
- [Managing complexity](#managing-complexity)
- [Commenting loops](#commenting-loops)
- [Implementation trade-offs](#implementation-trade-offs)
- [Integrated exercises](#integrated-exercises)
- [Summary](#summary)

## Learning objectives

By the end, you should be able to explain naming choices, restructure complex conditions, extract coherent operations, document non-obvious decisions and verify that a refactoring preserves behavior. You should also be able to choose between competing implementations rather than apply stylistic rules mechanically.

Prerequisites: traditional loops, functions, arrays, objects, scope, control flow, refactoring, testing and computational complexity from previous lessons.

## Naming variables

### Names describe roles

Consider a shop that needs the total price of all items costing at least 100. For prices 80, 120, 50 and 200, the expected total is 320.

```javascript
const a = [80, 120, 50, 200];
let s = 0;
for (let i = 0; i < a.length; i++) {
  if (a[i] >= 100) s += a[i];
}
console.log(s);
```

Expected output:

```text
320
```

The implementation is correct, but `a` and `s` do not communicate the problem. A reader must infer their meaning from operations. Rename the collection and accumulator according to their roles. Give the threshold a name so its purpose is visible.

```javascript
const prices = [80, 120, 50, 200];
const minimumPrice = 100;
let qualifyingTotal = 0;
for (let i = 0; i < prices.length; i++) {
  const price = prices[i];
  if (price >= minimumPrice) {
    qualifyingTotal += price;
  }
}
console.log(qualifyingTotal);
```

Expected output:

```text
320
```

`prices[i]` retrieves the current element. Assigning it to `price` makes the condition and accumulation refer to the domain value instead of its storage location. `qualifyingTotal += price` means `qualifyingTotal = qualifyingTotal + price`. It changes the accumulated total, not the current price.

The name `i` is appropriate for a short, conventional traversal. Replacing every index with a long name is unnecessary. When several indexes have distinct roles, however, their names should reveal those roles.

### Indexes, counts and pointers

Suppose a sorted array contains duplicate values. We want to retain one occurrence of each value in the original array. For `[2, 2, 5, 5, 8]`, the result is `[2, 5, 8]`.

```javascript
const values = [2, 2, 5, 5, 8];
let writeIndex = 0;
for (let readIndex = 0; readIndex < values.length; readIndex++) {
  const value = values[readIndex];
  if (writeIndex === 0 || value !== values[writeIndex - 1]) {
    values[writeIndex] = value;
    writeIndex++;
  }
}
values.length = writeIndex;
console.log(values.join(' '));
```

Expected output:

```text
2 5 8
```

`readIndex` identifies the next original element to inspect. `writeIndex` identifies the next available position in the retained prefix. It also equals the number of retained elements. The expression `writeIndex - 1` identifies the last retained element, not necessarily the previous original element. The first condition prevents access to index -1 when the retained prefix is empty.

| Read index | Value | Write index before | Retained prefix |
| --- | --- | --- | --- |
| 0 | 2 | 0 | 2 |
| 1 | 2 | 1 | 2 |
| 2 | 5 | 1 | 2, 5 |
| 3 | 5 | 2 | 2, 5 |
| 4 | 8 | 2 | 2, 5, 8 |

The names expose the invariant: the write position never passes the read position. Thus, writing cannot destroy an unprocessed element. Truncation must happen after traversal. Naming alone does not establish correctness, but it helps readers recognize the proof.

For nested matrix traversal, `rowIndex` and `columnIndex` are often clearer than `i` and `j`. For binary search, `left`, `right` and `mid` communicate boundaries. For a Boolean, `hasMatch` or `isValid` reads as a proposition. Avoid names such as `flag`, `temp2` and `data` when a more precise role is known. A temporary swap variable named `temp` is fine when its role is obvious and its lifetime is short.

### Scope and changing roles

Declare variables as close as practical to their use. A per-item discount belongs inside the loop; a total belongs outside. Use `const` for a binding that is not reassigned and `let` for one that changes. `const` does not make an array or object immutable.

A misleading name can be worse than a short name. A variable named `average` that currently contains a sum makes intermediate expressions difficult to reason about. Maintain a `total` and compute `average` only when the count is known.

### Practice 1

Refactor the following code without changing its behavior. It counts values strictly above 10 and records the first qualifying index. Explain why the first index must not be overwritten. Expected output: `2 1`.

```js
const a = [4, 12, 3, 18];
let x = 0, y = -1;
for (let i = 0; i < a.length; i++) {
  if (a[i] > 10) {
    x++;
    if (y === -1) y = i;
  }
}
console.log(x, y);
```

#### Worked solution

There are two independent state variables: the number of qualifying values and the position of the first one. A sentinel of -1 represents no matching index. The sentinel is valid because array indexes begin at zero.

```javascript
const values = [4, 12, 3, 18];
const threshold = 10;
let matchCount = 0;
let firstMatchIndex = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] > threshold) {
    matchCount++;
    if (firstMatchIndex === -1) {
      firstMatchIndex = i;
    }
  }
}
console.log(matchCount, firstMatchIndex);
```

Expected output:

```text
2 1
```

At index 1 the count becomes 1 and the sentinel becomes 1. At index 3 the count becomes 2, but the sentinel condition is false. Without that condition the variable would track the last match instead. For an empty array or no matches the result remains `0 -1`.

## Formatting and structure

### Make the execution structure visible

Indentation should reveal nesting, not create a misleading impression of it. JavaScript uses braces to define blocks; indentation does not determine which statements belong to a condition.

```javascript
let count = 0;
let total = 0;
const values = [2, -3, 4];
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0)
    count++;
    total += values[i];
}
console.log(count, total);
```

Expected output:

```text
2 3
```

Only `count++` belongs to the `if`. The total includes -3, despite its indentation. If the requirement is to count and sum positive values, both operations must belong to the block.

```javascript
let count = 0;
let total = 0;
const values = [2, -3, 4];
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) {
    count++;
    total += values[i];
  }
}
console.log(count, total);
```

Expected output:

```text
2 6
```

This is a bug fix, not a behavior-preserving refactoring. Distinguish these activities when reviewing changes. A consistent brace policy prevents accidental changes when a second statement is added later.

### Keep loop headers focused

The conventional `for` header contains initialization, continuation condition and update. It can contain multiple expressions, but hiding business operations there makes execution order harder to see.

```javascript
const values = [3, 5, 7];
let total = 0;
for (let i = 0; i < values.length; total += values[i], i++) {
  // The update expression performs the accumulation.
}
console.log(total);
```

Expected output:

```text
15
```

The comma operator evaluates its left expression and then its right expression. Here the total is updated before the index advances. This is legal, but the accumulation is the purpose of the loop, not merely its traversal mechanism. Move it into the body:

```javascript
const values = [3, 5, 7];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
}
console.log(total);
```

Expected output:

```text
15
```

These implementations are equivalent for this body. If a `continue` were introduced before accumulation in the second version, equivalence could fail: a `for` loop still evaluates its update expression after `continue`. Review control flow when moving operations.

### Organize phases

Use blank lines to separate meaningful phases: validation, initialization, traversal and result construction. Inside a loop, group related statements. Avoid arbitrary blank lines between every statement.

When a condition spans several independent requirements, named Boolean expressions can make the logic visible. But preserve short-circuit behavior. In `record !== null && record.active`, the property access is skipped when the record is null. Computing `record.active` unconditionally before checking for null would throw.

### Practice 2

The following loop should count only even positive numbers. Explain its actual behavior and correct it. Input: `[-2, 0, 2, 3, 4]`. Expected correct count: 2.

```js
let count = 0;
for (let i = 0; i < values.length; i++)
  if (values[i] > 0)
    if (values[i] % 2 === 0)
      count++;
```

#### Worked solution

The original fragment is already logically correct when `values` is supplied. Each nested condition controls the next statement. The improvement is structural, not a bug fix. Both conditions must hold, so they can be combined using `&&`.

```javascript
const values = [-2, 0, 2, 3, 4];
let count = 0;
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (value > 0 && value % 2 === 0) {
    count++;
  }
}
console.log(count);
```

Expected output:

```text
2
```

For -2 and 0 the first operand is false, so the second is not evaluated. For 3 the first is true but the remainder is 1. Only 2 and 4 increment the count. Braces make future modifications safer.

## Managing complexity

### Reject unsuitable values early

Suppose we must total completed orders with positive amounts. Null entries are allowed and should be ignored. A deeply nested implementation might first check the record, then its status, then its amount.

```javascript
const orders = [null, { status: 'done', amount: 20 },
  { status: 'pending', amount: 50 }, { status: 'done', amount: -4 }];
let total = 0;
for (let i = 0; i < orders.length; i++) {
  const order = orders[i];
  if (order !== null) {
    if (order.status === 'done') {
      if (order.amount > 0) {
        total += order.amount;
      }
    }
  }
}
console.log(total);
```

Expected output:

```text
20
```

The successful operation is buried three levels deep. A guard clause rejects an unsuitable case before proceeding. In a loop, `continue` can serve this role.

```javascript
const orders = [null, { status: 'done', amount: 20 },
  { status: 'pending', amount: 50 }, { status: 'done', amount: -4 }];
let total = 0;
for (let i = 0; i < orders.length; i++) {
  const order = orders[i];
  if (order === null) continue;
  if (order.status !== 'done') continue;
  if (order.amount <= 0) continue;
  total += order.amount;
}
console.log(total);
```

Expected output:

```text
20
```

The null guard must precede property access. The `for` update still executes after each `continue`. This example assumes numeric, non-NaN amounts. If NaN is allowed, `amount <= 0` is not the logical opposite of `amount > 0`: both comparisons are false. Use `if (!(order.amount > 0)) continue;` to preserve the original condition exactly. A guard refactoring must negate the actual condition, not just choose a visually opposite operator.

For a `while` loop, a guard can accidentally skip the index update. Either update before the guard when that preserves semantics, or choose a `for` loop with a guaranteed update expression.

### Extract a meaningful operation

A function packages a reusable operation. Parameters receive input values, and `return` supplies a result to the caller. A predicate is a function that returns a Boolean describing whether a condition holds.

Suppose a report includes only completed orders whose amounts are finite and positive. The same eligibility rule is needed in multiple reports. Repeating it creates a maintenance risk: one copy might change while another does not.

```javascript
function isEligibleOrder(order) {
  return order !== null &&
    order.status === 'done' &&
    Number.isFinite(order.amount) &&
    order.amount > 0;
}
const orders = [null, { status: 'done', amount: 20 },
  { status: 'done', amount: NaN }, { status: 'pending', amount: 50 }];
let total = 0;
let count = 0;
for (let i = 0; i < orders.length; i++) {
  const order = orders[i];
  if (!isEligibleOrder(order)) continue;
  total += order.amount;
  count++;
}
console.log(total, count);
```

Expected output:

```text
20 1
```

The function's contract assumes each entry is either null or an order object. `Number.isFinite` rejects NaN, infinities and non-number values without converting them. Each `&&` operand is evaluated only if earlier operands are truthy. `!isEligibleOrder(order)` negates the returned Boolean. The traversal now expresses the reporting operation separately from the eligibility policy.

Extraction is valuable when the operation has a coherent purpose. A function named `doStuff` that updates unrelated external variables merely hides complexity. Prefer explicit inputs and returned results when practical. Avoid extracting every trivial expression into a function: readers should not need to jump across many definitions to understand a simple sum.

### Separate responsibilities or combine traversals?

Consider a report requiring the total and count of positive values, then their average. The total and count are related aggregates with the same eligibility rule, so one traversal is natural. Computing the average inside every iteration would repeat work and could divide by zero before the first match.

```javascript
function summarizePositive(values) {
  let total = 0;
  let count = 0;
  for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value > 0) {
      total += value;
      count++;
    }
  }
  const average = count === 0 ? null : total / count;
  return { total, count, average };
}
const report = summarizePositive([-1, 4, 6]);
console.log(report.total, report.count, report.average);
console.log(summarizePositive([]).average);
```

Expected output:

```text
10 2 5
null
```

The conditional operator chooses `null` when no qualifying values exist; otherwise it evaluates the division. The returned object uses property shorthand: `{ total }` means `{ total: total }`. The caller receives the related results together. The contract assumes finite numeric values and sums that remain representable.

If the next stage must select values above the final average, it cannot safely be fused into the same traversal using the running average. The final average depends on every qualifying value. Two passes express this dependency clearly.

### Practice 3

Given `[null, {active: true, score: 8}, {active: false, score: 9}, {active: true, score: 3}]`, collect scores of active records with scores at least 5. Use a predicate and a guard. Expected result: `8`. Assume records are null or objects with Boolean active and finite numeric score properties.

#### Worked solution

Eligibility is independent of the collection operation. First reject null, then require active status and the minimum score. A result array stores only qualifying scores.

```javascript
function qualifies(record) {
  return record !== null && record.active && record.score >= 5;
}
const records = [null, {active: true, score: 8},
  {active: false, score: 9}, {active: true, score: 3}];
const scores = [];
for (let i = 0; i < records.length; i++) {
  const record = records[i];
  if (!qualifies(record)) continue;
  scores.push(record.score);
}
console.log(scores.join(' '));
```

Expected output:

```text
8
```

The null entry fails before property access. The inactive record fails even though its score is high. The final record fails the threshold. `push` appends the score only after all requirements hold. Empty input produces an empty result array; a score of exactly 5 qualifies.

## Commenting loops

### Explain decisions rather than syntax

A comment such as `// Increment i` beside `i++` usually repeats what the code already says. A useful comment records information that is not obvious from the expression itself: a requirement, invariant, reason for a boundary, or deliberate trade-off.

For example, when deleting negative values from an array, traverse backward to avoid skipping elements that shift into the removed position.

```javascript
const values = [1, -2, -3, 4];
// Traverse backward so deletion cannot shift an unvisited item past us.
for (let i = values.length - 1; i >= 0; i--) {
  if (values[i] < 0) {
    values.splice(i, 1);
  }
}
console.log(values.join(' '));
```

Expected output:

```text
1 4
```

`splice(i, 1)` removes one element at index `i`. Elements to its right shift left. Those elements have already been examined, so the next lower index remains valid. The comment explains why backward traversal is intentional. A forward traversal with unconditional increment would skip -3 after removing -2.

### Document contracts and invariants

A contract states what inputs a function accepts and what it promises to return. A loop invariant states a property that holds before each iteration and is preserved by the body. These are especially useful when an algorithm has non-obvious boundaries.

```javascript
// Requires ascending sorted numbers. Returns the first index with value >= target,
// or values.length when no such element exists.
function lowerBound(values, target) {
  let left = 0;
  let right = values.length;
  // All indexes below left are too small; all indexes >= right qualify.
  while (left < right) {
    const mid = left + Math.trunc((right - left) / 2);
    if (values[mid] < target) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }
  return left;
}
console.log(lowerBound([2, 4, 4, 9], 4));
console.log(lowerBound([2, 4, 4, 9], 10));
```

Expected output:

```text
1
4
```

The search uses a half-open interval `[left, right)`. Subtracting left from right gives the remaining width; dividing by two and truncating gives an integer offset; adding left gives an index within the interval. If the midpoint is too small, every earlier value is too small. Otherwise the midpoint qualifies, so it becomes the new upper boundary. The invariant explains why `right = mid`, rather than `mid - 1`, is correct for this representation. The width strictly decreases until the boundaries meet.

Comments must evolve with code. A stale comment claiming an inclusive upper boundary would be actively misleading. Prefer names and structure first, then document the reasoning that remains non-obvious.

### Practice 4

Explain why the following loop begins at 1, and write a useful comment. It should count adjacent equal pairs, including overlapping pairs. Input `[4, 4, 4, 2]` should produce 2.

#### Worked solution

```javascript
const values = [4, 4, 4, 2];
let equalPairCount = 0;
// Start at 1 because each comparison needs a preceding element.
// Overlapping pairs are intentional: a run of k equal values contributes k - 1.
for (let i = 1; i < values.length; i++) {
  if (values[i] === values[i - 1]) {
    equalPairCount++;
  }
}
console.log(equalPairCount);
```

Expected output:

```text
2
```

Indexes 1 and 2 each match their predecessor. Index 3 does not. Starting at zero would compare against a nonexistent predecessor; starting at two would omit the first pair. Empty and single-element arrays produce zero. The second comment distinguishes overlapping pairs from disjoint pairs, which is a requirement not apparent from the name alone.

## Implementation trade-offs

### Clarity versus repeated work

Suppose a report needs the maximum total of any three consecutive values. A direct nested loop expresses the definition clearly: enumerate each valid starting position and sum its three values. A rolling window avoids repeated additions by reusing the previous sum.

```javascript
function directBest(values, size) {
  let best = -Infinity;
  for (let start = 0; start <= values.length - size; start++) {
    let total = 0;
    for (let offset = 0; offset < size; offset++) {
      total += values[start + offset];
    }
    if (total > best) best = total;
  }
  return best;
}
function rollingBest(values, size) {
  let total = 0;
  for (let i = 0; i < size; i++) total += values[i];
  let best = total;
  for (let right = size; right < values.length; right++) {
    const outgoingIndex = right - size;
    total -= values[outgoingIndex];
    total += values[right];
    if (total > best) best = total;
  }
  return best;
}
const values = [4, -2, 7, 1, 5];
console.log(directBest(values, 3), rollingBest(values, 3));
```

Expected output:

```text
13 13
```

Both functions require an integer size between 1 and the array length, and finite numbers. For exact equivalence assume integer arithmetic stays within the safe integer range. Floating-point sums may differ because addition and subtraction are performed in different orders.

The direct version computes `n - size + 1` windows, each requiring `size` additions. Its time cost is O(n * size) as an upper bound. The rolling version initializes one window, then removes the outgoing element and adds the incoming element once per shift. Its time cost is O(n). Both use O(1) auxiliary space. If size is a fixed constant, both are O(n), although operation counts differ.

For the example, initial sum is 9. The next sum is `9 - 4 + 1 = 6`. The final sum is `6 - (-2) + 5 = 13`. The named outgoing index and separate updates expose the reasoning. The direct version remains valuable as a simple reference implementation for tests.

### Mutation versus copying

Reversing an array in place saves auxiliary storage, but changes the caller's array. Creating a reversed copy preserves the original at the cost of O(n) extra storage. Neither choice is universally better; the function contract determines which behavior is acceptable.

```javascript
function reversedCopy(values) {
  const result = [];
  for (let i = values.length - 1; i >= 0; i--) {
    result.push(values[i]);
  }
  return result;
}
const original = [1, 2, 3];
const reversed = reversedCopy(original);
console.log(original.join(' '));
console.log(reversed.join(' '));
```

Expected output:

```text
1 2 3
3 2 1
```

This creates a shallow copy: object elements, if present, remain shared references. It assumes a dense array. A sparse array would have holes converted into explicit undefined values by this implementation. If preserving holes matters, the contract and implementation must account for that.

### Practice 5

A function must determine whether an array contains a negative number. Compare counting all negatives with returning immediately after the first negative. Input `[2, -1, 3, -4]` should return true. Explain which approach is preferable when no count is required.

#### Worked solution

```javascript
function hasNegative(values) {
  for (let i = 0; i < values.length; i++) {
    if (values[i] < 0) return true;
  }
  return false;
}
console.log(hasNegative([2, -1, 3, -4]));
console.log(hasNegative([]));
console.log(hasNegative([0, 2]));
```

Expected output:

```text
true
false
false
```

The requirement is existential: one match is sufficient. Returning immediately expresses that requirement and avoids unnecessary work. Returning false inside the loop after the first nonnegative value would be incorrect, because later elements may qualify. The worst case remains O(n), but the best case is O(1). Counting is preferable only when the count itself is required or when traversal has other required effects.

## Integrated exercises

Attempt these before reading the worked solutions.

### Exercise 1 - Refactor a transaction report

Given transactions with numeric amounts and a Boolean `approved`, report the count and total of approved positive transactions. Ignore null entries. Return an object. For `[null, {approved:true, amount:10}, {approved:false, amount:20}, {approved:true, amount:-3}, {approved:true, amount:5}]`, expect count 2 and total 15. Use meaningful names, guards and a reusable predicate. Assume finite amounts and safe totals.

#### Worked solution

The eligibility rule must precede aggregation. The count and total share the same qualifying population, so combine them in one traversal. The null guard prevents property access on null.

```javascript
function isApprovedCredit(transaction) {
  return transaction !== null && transaction.approved && transaction.amount > 0;
}
function summarizeCredits(transactions) {
  let creditCount = 0;
  let creditTotal = 0;
  for (let i = 0; i < transactions.length; i++) {
    const transaction = transactions[i];
    if (!isApprovedCredit(transaction)) continue;
    creditCount++;
    creditTotal += transaction.amount;
  }
  return { count: creditCount, total: creditTotal };
}
const transactions = [null, {approved:true, amount:10},
  {approved:false, amount:20}, {approved:true, amount:-3},
  {approved:true, amount:5}];
const report = summarizeCredits(transactions);
console.log(report.count, report.total);
console.log(summarizeCredits([]).total);
```

Expected output:

```text
2 15
0
```

After the first qualifying transaction, state is count 1 and total 10. The next two records are rejected. The last record changes state to 2 and 15. Both accumulators start at their additive identity zero, so empty input works naturally. The function does not mutate its input. Separating the predicate makes future eligibility changes local, while the report remains readable.

### Exercise 2 - Simplify nested traversal

A ragged matrix contains rows of different lengths. Find the first negative value in row-major order and return its row and column indexes, or null if none exists. For `[[2, 4], [], [7, -3], [-8]]`, expect `2 1`. Avoid flags and unnecessary traversal.

#### Worked solution

Row-major order means finish each row before moving to the next. The outer boundary is the number of rows; the inner boundary is the current row length. Returning from the function exits both loops at once.

```javascript
function findFirstNegative(matrix) {
  for (let rowIndex = 0; rowIndex < matrix.length; rowIndex++) {
    const row = matrix[rowIndex];
    for (let columnIndex = 0; columnIndex < row.length; columnIndex++) {
      if (row[columnIndex] < 0) {
        return { rowIndex, columnIndex };
      }
    }
  }
  return null;
}
const position = findFirstNegative([[2, 4], [], [7, -3], [-8]]);
console.log(position.rowIndex, position.columnIndex);
console.log(findFirstNegative([[], [0, 2]]));
```

Expected output:

```text
2 1
null
```

The empty row causes zero inner iterations. The first negative is found at row 2, column 1. A plain `break` would exit only the inner loop and could allow a later match to overwrite the result. A labeled break with stored state is possible, but returning directly is clearer when the search is encapsulated in a function. The worst-case cost is proportional to the number of rows plus the number of elements visited.

### Exercise 3 - Verify a refactoring

Compare two implementations of stable filtering: one builds a new array, the other compacts the input. Retain nonnegative values, preserving their order. For `[3, -1, 0, -2, 4]`, expect `[3, 0, 4]`. Verify empty input, all rejected, all retained and mixed input. Explain why equal returned values do not imply equal observable behavior.

#### Worked solution

The copying implementation appends qualifying values. The compaction implementation writes each qualifying value at the next retained position. A separate copy of each test input is required so the mutating implementation does not alter the reference input.

```javascript
function filterCopy(values) {
  const result = [];
  for (let i = 0; i < values.length; i++) {
    if (values[i] >= 0) result.push(values[i]);
  }
  return result;
}
function compactNonnegative(values) {
  let writeIndex = 0;
  for (let readIndex = 0; readIndex < values.length; readIndex++) {
    const value = values[readIndex];
    if (value >= 0) {
      values[writeIndex] = value;
      writeIndex++;
    }
  }
  values.length = writeIndex;
  return values;
}
function sameValues(a, b) {
  if (a.length !== b.length) return false;
  for (let i = 0; i < a.length; i++) {
    if (a[i] !== b[i]) return false;
  }
  return true;
}
const cases = [[], [-2, -1], [0, 2], [3, -1, 0, -2, 4]];
for (let i = 0; i < cases.length; i++) {
  const input = cases[i];
  const workingCopy = [];
  for (let j = 0; j < input.length; j++) workingCopy.push(input[j]);
  const expected = filterCopy(input);
  const actual = compactNonnegative(workingCopy);
  console.log(sameValues(expected, actual));
}
```

Expected output:

```text
true
true
true
true
```

The comparison checks lengths before comparing corresponding values. For the mixed case, writeIndex advances only for 3, 0 and 4. Because writeIndex never exceeds readIndex, unread values remain intact. Truncating after traversal removes the obsolete tail. Both implementations preserve order and take O(n) time. The copy requires O(n) output storage in the worst case; compaction uses O(1) auxiliary storage but mutates the input. They are equivalent with respect to retained values, not with respect to mutation or reference identity. These tests cover representative dense numeric arrays, not every possible input.

### Exercise 4 - Choose a maintainable search

Given ascending sorted prices `[10, 20, 20, 35, 50]`, find the first price at least 20. Compare a linear scan with lower-bound binary search. Return the array length if no price qualifies. Explain which implementation you would choose for a small one-time search versus repeated searches on a large sorted array.

#### Worked solution

The linear scan visits values in order and stops at the first qualifying index. Binary search eliminates ranges using sortedness. Both must return the first duplicate, not just any matching index.

```javascript
function firstQualifyingLinear(prices, minimum) {
  for (let i = 0; i < prices.length; i++) {
    if (prices[i] >= minimum) return i;
  }
  return prices.length;
}
function firstQualifyingBinary(prices, minimum) {
  let left = 0;
  let right = prices.length;
  while (left < right) {
    const mid = left + Math.trunc((right - left) / 2);
    if (prices[mid] < minimum) left = mid + 1;
    else right = mid;
  }
  return left;
}
const prices = [10, 20, 20, 35, 50];
const targets = [5, 20, 21, 60];
for (let i = 0; i < targets.length; i++) {
  const target = targets[i];
  console.log(firstQualifyingLinear(prices, target),
    firstQualifyingBinary(prices, target));
}
```

Expected output:

```text
0 0
1 1
3 3
5 5
```

For target 20, binary search first examines index 2. That value qualifies, so right becomes 2. Index 1 also qualifies, so right becomes 1. Index 0 is too small, so left becomes 1. The boundaries meet at the first qualifying index. The linear version is simpler and may be appropriate for small inputs; binary search has O(log n) worst-case time for already sorted data, versus O(n) for linear search. Sorting solely for a single search can cost more than scanning. Document sortedness and the no-match sentinel rather than hide these requirements.

## Summary

Maintainability is a property of the relationship between code and its requirements. Choose names that reveal roles, use formatting that reflects actual execution, keep traversal mechanics separate from business operations, and extract coherent responsibilities when useful. Comments should explain non-obvious intent and invariants. Preserve short-circuit evaluation, scope, update order, mutation and termination during refactoring. Use tests to compare behavior, and choose algorithms based on actual constraints rather than stylistic absolutes.

[Previous: Lesson 25](25-loop-performance-and-optimization.md) | [Next: Lesson 27 (planned)](27-solving-problems-with-loops.md)
