# Lesson 21 - Advanced Loop Techniques

## Part 2 - Prefix and Suffix Aggregates and Sliding Window

[Previous: Part 1](21-part-01-two-pointer-and-multi-pointer-techniques.md) | [Contents](#table-of-contents) | Next: Part 3 (planned)

## Introduction

In Part 1, we learned to maintain several positions rather than treating every iteration as an independent operation. Here we extend that idea to ranges. When two ranges overlap, much of the information calculated for one range may be useful for the next. Instead of repeatedly calculating everything, we can preserve cumulative information or update an aggregate as its boundaries move.

By the end of this part, you should be able to derive prefix and suffix aggregates, answer range queries, calculate fixed and variable window results, justify boundary updates, recognize when a window strategy is invalid, and verify solutions against edge cases.

We use dense arrays of numbers and traditional loops. Unless otherwise stated, examples use small integers so that arithmetic is exact. A contiguous range contains consecutive indexes, with no skipped elements. A range query asks for information about a specified range without necessarily modifying it.

## Table of contents

- [1. From repeated calculations to prefix sums](#1-from-repeated-calculations-to-prefix-sums)
- [2. Range queries using prefix sums](#2-range-queries-using-prefix-sums)
- [3. Other prefix aggregates](#3-other-prefix-aggregates)
- [4. Suffix aggregates](#4-suffix-aggregates)
- [5. Combining prefix and suffix information](#5-combining-prefix-and-suffix-information)
- [6. Fixed-size sliding windows](#6-fixed-size-sliding-windows)
- [7. Fixed-window variations](#7-fixed-window-variations)
- [8. Variable-size sliding windows](#8-variable-size-sliding-windows)
- [9. Window validity and limitations](#9-window-validity-and-limitations)
- [10. Integrated exercises](#10-integrated-exercises)
- [11. Summary](#11-summary)

## 1. From repeated calculations to prefix sums

Suppose a shop records daily sales as `[4, 7, 2, 9, 3]`. A manager asks for the total from day one through day three (indexes 0 through 2), then the total from indexes 1 through 3. We already know how to accumulate a range.

```javascript
const sales = [4, 7, 2, 9, 3];
let total = 0;
for (let i = 0; i <= 2; i++) {
  total += sales[i];
}
console.log(total);
```

Expected output:

```text
13
```

The next query requires `7 + 2 + 9`. Notice that both queries include 7 and 2. Repeating the loop for every query is correct, but it repeatedly processes overlapping information.

A prefix is the beginning portion of a sequence. We can store the sum of every prefix once, then use those stored totals later. The prefix containing no elements has sum zero. The prefix containing one element has sum 4. The prefix containing two elements has sum 11, and so on.

```text
Original index:       0   1   2   3   4
Original value:       4   7   2   9   3
Prefix boundary:  0   1   2   3   4   5
Prefix sum:       0   4  11  13  22  25
```

The prefix array contains one more element than the original. Its index represents a boundary, or equivalently the number of original elements included. Thus `prefix[3]` includes indexes 0, 1 and 2, not index 3.

### Building the prefix array

Each new total is the previous total plus the next original value. We do not need a nested loop.

```javascript
const sales = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < sales.length; i++) {
  prefix.push(prefix[i] + sales[i]);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 4 11 13 22 25
```

Here `join(' ')` returns a string containing the array elements separated by spaces. It does not change the array. For example, `[4, 7].join(' ')` returns `'4 7'`. We use it only to make expected console output consistent.

The central statement combines three operations. `prefix[i]` retrieves the total of the first `i` elements. `sales[i]` retrieves the next element. Addition produces the new total, and `push` appends it. At `i = 2`, the expression becomes `prefix[2] + sales[2]`, then `11 + 2`, then `13`. Appending creates `prefix[3]`.

| i | Previous total | Next value | Appended total |
|---:|---:|---:|---:|
| 0 | 0 | 4 | 4 |
| 1 | 4 | 7 | 11 |
| 2 | 11 | 2 | 13 |
| 3 | 13 | 9 | 22 |
| 4 | 22 | 3 | 25 |

An equivalent implementation maintains a separate running total. This can be easier to read when learning.

```javascript
const values = [4, -7, 2, 0];
const prefix = [0];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
  prefix.push(total);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 4 -3 -1 -1
```

Negative numbers and zeros are valid. Prefix sums need not increase. For an empty input the result is `[0]`, representing the empty prefix.

### Practice 1 - Construct and trace

Construct prefix sums for `[3, -2, 5, -6]`. Explain the third appended value.

#### Worked solution

```javascript
const values = [3, -2, 5, -6];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 3 1 6 0
```

At index 2 the previous total is 1, because 3 plus negative 2 equals 1. Adding 5 gives 6. This becomes boundary 3. The final total returns to zero, but that does not mean the array was empty.

## 2. Range queries using prefix sums

Suppose we need the total from index 1 through index 3 inclusive. The prefix ending immediately after index 3 includes `4 + 7 + 2 + 9`. The prefix ending immediately before index 1 includes only `4`. Subtracting the second from the first removes the unwanted beginning.

```text
prefix[4] = 4 + 7 + 2 + 9 = 22
prefix[1] = 4             =  4
                            ----
Desired sum                 18
```

For an inclusive range `[start, end]`, the formula is `prefix[end + 1] - prefix[start]`. The addition of one converts the last included index into the boundary immediately after it.

```javascript
const values = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
const start = 1;
const end = 3;
console.log(prefix[end + 1] - prefix[start]);
```

Expected output:

```text
18
```

### Half-open ranges

A half-open range includes its start but excludes its end. For `[start, stop)`, the formula becomes `prefix[stop] - prefix[start]`. For example, `[1, 4)` includes indexes 1, 2 and 3. This convention matches the familiar loop condition `i < stop`. An empty range has equal boundaries and sum zero.

### Multiple queries and validation

The prefix array can be reused for many queries. Store each query as a pair of boundaries. We use `Number.isInteger(value)` to check whether a value is an integer: it returns true for 3 and false for 3.5 or the string `'3'`. A valid half-open range has integer boundaries satisfying `0 <= start <= stop <= length`. JavaScript does not interpret chained comparisons mathematically, so we express each comparison separately.

```javascript
const values = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
const queries = [[1, 4], [0, 5], [2, 2], [4, 5], [-1, 2], [2, 6]];
for (let q = 0; q < queries.length; q++) {
  const start = queries[q][0];
  const stop = queries[q][1];
  if (!Number.isInteger(start) || !Number.isInteger(stop) ||
      start < 0 || stop < start || stop > values.length) {
    console.log('Invalid range');
    continue;
  }
  console.log(prefix[stop] - prefix[start]);
}
```

Expected output:

```text
18
25
0
3
Invalid range
Invalid range
```

`queries[q][0]` first retrieves the query array and then its first boundary. The validation prevents accessing missing prefix entries. Without it, an out-of-range access can produce `undefined`, and subtraction can produce `NaN`.

### Mistakes and trade-offs

Using `prefix[end] - prefix[start]` for inclusive boundaries excludes the final element. For indexes 1 through 3, it gives `13 - 4 = 9`, not 18. Another mistake is forgetting the initial zero: the boundary interpretation then changes and the formula no longer works as written.

Building the prefix array requires one traversal and storage for `n + 1` totals, where `n` is the number of input elements. Each subsequent query uses two indexed reads and one subtraction, regardless of range length. For a single short query, direct accumulation may be simpler. If original values change, stored totals become stale. For example, replacing the first value affects every nonempty prefix. Rebuild or update affected totals before answering more queries.

### Practice 2 - Inclusive and empty ranges

For `[8, -3, 4, 6]`, find the sum of indexes 1 through 3 inclusive, the sum of index 2 alone, and the sum of the empty half-open range `[2, 2)`.

#### Worked solution

```javascript
const values = [8, -3, 4, 6];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
console.log(prefix[4] - prefix[1]);
console.log(prefix[3] - prefix[2]);
console.log(prefix[2] - prefix[2]);
```

Expected output:

```text
7
4
0
```

The boundaries contain `[0, 8, 5, 9, 15]`. The first answer is 15 minus 8. A single-element range has adjacent boundaries, so the second answer is 9 minus 5. Equal boundaries cancel to zero.

## 3. Other prefix aggregates

An aggregate summarizes several values. Addition is not the only useful aggregation operation. We can maintain counts, products, minimums and maximums. However, not all aggregates support range extraction by subtracting two prefixes.

### Conditional counts

Suppose a sensor reports `[12, 5, 18, 7, 20]`. We want to count readings of at least 10 in any range. Instead of accumulating the reading itself, add one when the reading qualifies and zero otherwise.

```javascript
const readings = [12, 5, 18, 7, 20];
const counts = [0];
for (let i = 0; i < readings.length; i++) {
  let contribution = 0;
  if (readings[i] >= 10) {
    contribution = 1;
  }
  counts.push(counts[i] + contribution);
}
console.log(counts.join(' '));
console.log(counts[4] - counts[1]);
```

Expected output:

```text
0 1 1 2 2 3
1
```

The range `[1, 4)` contains 5, 18 and 7. Only 18 qualifies. The difference `2 - 1` removes the qualifying reading before the range. This is a prefix sum of indicator values (zeros and ones), not of the original readings.

### Prefix minimum and maximum

For every position, suppose we want the greatest value encountered up to that position. We preserve the best value and update it when a larger one arrives. Unlike the sum, a maximum has no ordinary numeric empty-prefix value. For this example, store one result per original index, initializing the best value from the first element only when the input is nonempty.

```javascript
const values = [-8, -3, -6, 2, 1];
const maxima = [];
if (values.length > 0) {
  let best = values[0];
  for (let i = 0; i < values.length; i++) {
    if (values[i] > best) {
      best = values[i];
    }
    maxima.push(best);
  }
}
console.log(maxima.join(' '));
```

Expected output:

```text
-8 -3 -3 2 2
```

Here `maxima[i]` includes original index `i`. This differs from the boundary-indexed sum convention. Initializing `best` to zero would incorrectly report zero for the negative-only beginning. For a minimum, reverse the comparison. Neither minimum nor maximum can generally be recovered for an arbitrary range by subtracting prefix extrema: the earlier maximum may hide every value in the later range.

### Prefix products

Multiplication has identity one: multiplying by one preserves a value. A product prefix therefore starts with one. With nonzero factors and exact arithmetic, dividing two prefix products removes the earlier factors.

```javascript
const values = [2, 3, 4, 5];
const products = [1];
for (let i = 0; i < values.length; i++) {
  products.push(products[i] * values[i]);
}
console.log(products.join(' '));
console.log(products[3] / products[1]);
```

Expected output:

```text
1 2 6 24 120
12
```

A zero destroys this division strategy: after a zero, every ordinary prefix product is zero. Dividing zero by zero yields `NaN`. One solution is to maintain a separate prefix count of zeros and a product of nonzero factors. A range containing any zero has product zero; otherwise divide the nonzero products. Large products can overflow or lose precision, so this is not a universally safe numeric solution.

```javascript
const values = [2, 0, 3, 4];
const zeros = [0];
const products = [1];
for (let i = 0; i < values.length; i++) {
  if (values[i] === 0) {
    zeros.push(zeros[i] + 1);
    products.push(products[i]);
  } else {
    zeros.push(zeros[i]);
    products.push(products[i] * values[i]);
  }
}
const ranges = [[0, 3], [2, 4], [1, 1]];
for (let i = 0; i < ranges.length; i++) {
  const start = ranges[i][0];
  const stop = ranges[i][1];
  if (zeros[stop] - zeros[start] > 0) {
    console.log(0);
  } else {
    console.log(products[stop] / products[start]);
  }
}
```

Expected output:

```text
0
12
1
```

The empty product is one. For `[2, 4)`, the zero count difference is zero, and the nonzero product ratio is `24 / 2 = 12`.

### Practice 3 - Count failures and find running minimums

For `[6, 2, 8, 1, 4]`, count values below 5 in `[1, 5)` and produce the running minimum at every index.

#### Worked solution

```javascript
const values = [6, 2, 8, 1, 4];
const counts = [0];
const minima = [];
let best = values[0];
for (let i = 0; i < values.length; i++) {
  let contribution = 0;
  if (values[i] < 5) contribution = 1;
  counts.push(counts[i] + contribution);
  if (values[i] < best) best = values[i];
  minima.push(best);
}
console.log(counts[5] - counts[1]);
console.log(minima.join(' '));
```

Expected output:

```text
3
6 2 2 1 1
```

The qualifying values are 2, 1 and 4. The minimum changes only when 2 and then 1 are encountered. These are independent accumulators, even though they share a traversal. The given input is nonempty; for reusable code, guard initialization as in the earlier maximum example.

## 4. Suffix aggregates

A suffix is an ending portion of a sequence. Suppose a warehouse wants to know the total demand remaining from each day onward. For `[4, 7, 2, 9, 3]`, the demand from index 3 onward is 12, while the demand from index 2 onward is 14.

Rather than adding the same ending values repeatedly, traverse backward. Define `suffix[i]` as the sum from index `i` through the end. The boundary at `length` represents an empty suffix and has sum zero.

```javascript
const demand = [4, 7, 2, 9, 3];
const suffix = [];
suffix[demand.length] = 0;
for (let i = demand.length - 1; i >= 0; i--) {
  suffix[i] = demand[i] + suffix[i + 1];
}
console.log(suffix.join(' '));
```

Expected output:

```text
25 21 14 12 3 0
```

Assigning to index 5 of an empty array temporarily creates holes at indexes 0 through 4. The backward loop fills all of them before the array is used. Alternatively, initialize all positions to zero with a loop first. At `i = 3`, `suffix[i + 1]` retrieves `suffix[4]`, which already contains 3. Adding `demand[3]` gives 12. Forward traversal would attempt to read totals that have not yet been calculated.

| i | Current value | Known suffix to right | New suffix |
|---:|---:|---:|---:|
| 4 | 3 | 0 | 3 |
| 3 | 9 | 3 | 12 |
| 2 | 2 | 12 | 14 |
| 1 | 7 | 14 | 21 |
| 0 | 4 | 21 | 25 |

A half-open range `[start, stop)` can also be calculated as `suffix[start] - suffix[stop]`. For `[1, 4)`, this gives `21 - 3 = 18`.

### Suffix maximums and future comparisons

Suppose we want to identify days whose sales exceed every later day's sales. Maintain the maximum of the already processed suffix. The last day qualifies because no later day exists. Equal values do not qualify under the strict requirement.

```javascript
const sales = [8, 3, 7, 7, 2];
const leaders = [];
if (sales.length > 0) {
  let best = sales[sales.length - 1];
  leaders.push(best);
  for (let i = sales.length - 2; i >= 0; i--) {
    if (sales[i] > best) {
      leaders.push(sales[i]);
      best = sales[i];
    }
  }
}
console.log(leaders.join(' '));
```

Expected output:

```text
2 7 8
```

The results are in reverse traversal order. The rightmost 7 qualifies, but the earlier 7 does not because it equals a later value. If original order is required, traverse `leaders` backward into a new array or reverse it using the technique from Part 1. We need only a running suffix maximum here, not a stored array of all suffix maximums.

### Practice 4 - Remaining demand

For `[5, 0, 8, 2]`, construct suffix sums and find demand from index 1 onward and demand strictly after index 2.

#### Worked solution

```javascript
const values = [5, 0, 8, 2];
const suffix = [];
suffix[values.length] = 0;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] + suffix[i + 1];
}
console.log(suffix.join(' '));
console.log(suffix[1]);
console.log(suffix[2 + 1]);
```

Expected output:

```text
15 10 10 2 0
10
2
```

The phrase strictly after excludes the current index, so we add one to the index before accessing the suffix.

## 5. Combining prefix and suffix information

### Sum excluding each element

For `[4, 7, 2, 9]`, produce the sum of all other elements for every position. The values before index `i` have sum `prefix[i]`; the values after it have sum `suffix[i + 1]`. Adding them excludes the current element exactly once.

```javascript
const values = [4, 7, 2, 9];
const prefix = [0];
const suffix = [];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
suffix[values.length] = 0;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] + suffix[i + 1];
}
const result = [];
for (let i = 0; i < values.length; i++) {
  result.push(prefix[i] + suffix[i + 1]);
}
console.log(result.join(' '));
```

Expected output:

```text
18 15 20 13
```

At index 1, the left sum is 4 and the right sum is 11, giving 15. For sums alone, calculating the total once and subtracting each current value is simpler. The combined technique becomes more valuable when subtraction or division is unavailable or unsuitable.

### Product excluding each element without division

Given `[2, 0, 3, 4]`, calculate the product of every element except the current one. The result at the zero position should be 24; every other result should be zero. Dividing the total product by the current value fails because the total is zero and the zero position requires division by zero.

```javascript
const values = [2, 0, 3, 4];
const prefix = [1];
const suffix = [];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] * values[i]);
}
suffix[values.length] = 1;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] * suffix[i + 1];
}
const result = [];
for (let i = 0; i < values.length; i++) {
  result.push(prefix[i] * suffix[i + 1]);
}
console.log(result.join(' '));
```

Expected output:

```text
0 24 0 0
```

| i | Product before i | Product after i | Result |
|---:|---:|---:|---:|
| 0 | 1 | 0 | 0 |
| 1 | 2 | 12 | 24 |
| 2 | 0 | 4 | 0 |
| 3 | 0 | 1 | 0 |

The identity one makes the first and last positions work without special branches. A single-element array produces `[1]` because the product of no other elements is one. Two or more zeros produce all-zero results. An empty array produces an empty result.

We can avoid storing both aggregate arrays. First store the product strictly before each index in the result. Then traverse backward, multiplying by a running product strictly after each index.

```javascript
const values = [2, 3, 4, 5];
const result = [];
let before = 1;
for (let i = 0; i < values.length; i++) {
  result.push(before);
  before *= values[i];
}
let after = 1;
for (let i = values.length - 1; i >= 0; i--) {
  result[i] *= after;
  after *= values[i];
}
console.log(result.join(' '));
```

Expected output:

```text
60 40 30 24
```

The order of operations is essential. `result.push(before)` must occur before multiplying by the current value, otherwise the stored product includes the excluded element. Similarly, `result[i] *= after` must occur before updating `after`. The compound assignment means multiply the existing result at index `i` by the current suffix product and store it back.

### Practice 5 - Balance position

Find every index where the sum strictly before the index equals the sum strictly after it. For `[1, 3, 2, 2, 2]`, the expected index is 2.

#### Worked solution

```javascript
const values = [1, 3, 2, 2, 2];
let total = 0;
for (let i = 0; i < values.length; i++) total += values[i];
let left = 0;
const positions = [];
for (let i = 0; i < values.length; i++) {
  const right = total - left - values[i];
  if (left === right) positions.push(i);
  left += values[i];
}
console.log(positions.join(' '));
```

Expected output:

```text
2
```

At index 2, the total is 10, the left sum is 4, and the current value is 2. Subtracting both from the total gives 4. Update the left sum only after comparing, so the current element is excluded.

## 6. Fixed-size sliding windows

A window is a contiguous portion of a collection. A fixed-size window always contains the same number of elements. Suppose we need the greatest total sales over any three consecutive days in `[4, 7, 2, 9, 3, 6]`.

The possible windows are:

```text
Indexes 0..2: 4 7 2 = 13
Indexes 1..3: 7 2 9 = 18
Indexes 2..4: 2 9 3 = 14
Indexes 3..5: 9 3 6 = 18
```

We require the greatest sum and its earliest starting index. The expected answer is 18 at index 1.

### Direct calculation

```javascript
const values = [4, 7, 2, 9, 3, 6];
const k = 3;
let best = 0;
let bestStart = -1;
for (let start = 0; start <= values.length - k; start++) {
  let sum = 0;
  for (let i = start; i < start + k; i++) {
    sum += values[i];
  }
  if (bestStart === -1 || sum > best) {
    best = sum;
    bestStart = start;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
18 1
```

The last possible start is `length - k`: for six values and a window of three, index 3 is the last valid start. The inner loop visits exactly `k` elements. `bestStart === -1` ensures the first valid sum becomes the best even if it is negative. This solution is straightforward but adds the shared elements again for every window.

### Deriving the rolling sum

Moving a window one position to the right removes its first value and adds the next value. All other values remain unchanged.

```text
Original window: [4 7 2] 9 3 6   sum = 13
Remove 4:          7 2          sum = 9
Add 9:            [7 2 9]       sum = 18
```

We can calculate the first window normally, then maintain its sum using one subtraction and one addition per shift.

```javascript
const values = [4, 7, 2, 9, 3, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) {
  sum += values[i];
}
let best = sum;
let bestStart = 0;
for (let right = k; right < values.length; right++) {
  const outgoing = values[right - k];
  const incoming = values[right];
  sum = sum - outgoing + incoming;
  const start = right - k + 1;
  if (sum > best) {
    best = sum;
    bestStart = start;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
18 1
```

The variable `right` identifies the incoming element, not the old window's right boundary. Initially it equals 3. The outgoing index is `right - k`, which evaluates to `3 - 3 = 0`. The incoming value is `values[3]`, or 9. The updated sum is `13 - 4 + 9 = 18`. The new start is `3 - 3 + 1 = 1`. Adding one is necessary because the incoming index is included in the new window.

| Incoming index | Outgoing value | Incoming value | Updated sum | Start |
|---:|---:|---:|---:|---:|
| Initial | - | - | 13 | 0 |
| 3 | 4 | 9 | 18 | 1 |
| 4 | 7 | 3 | 14 | 2 |
| 5 | 2 | 6 | 18 | 3 |

Strict `sum > best` preserves the earliest tied result. Using `>=` instead selects the latest tied result.

### Validating window size

A nonempty fixed window requires an integer `k` between one and the array length. A zero-size window could be defined separately, but is excluded here. Validate before initializing the first sum.

```javascript
const values = [-8, -2, -5];
const k = 2;
if (!Number.isInteger(k) || k < 1 || k > values.length) {
  console.log('Invalid window size');
} else {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += values[i];
  let best = sum;
  for (let right = k; right < values.length; right++) {
    sum += values[right] - values[right - k];
    if (sum > best) best = sum;
  }
  console.log(best);
}
```

Expected output:

```text
-7
```

Initializing `best` to zero would produce an impossible result because both valid windows have negative sums. With `k = length`, the second loop does not execute; the first window is the only candidate. With `k = 1`, each element is a separate window.

### Practice 6 - Minimum window and ties

Find the minimum sum of two consecutive elements in `[5, -2, 3, -2, 6]`, reporting the latest tied start.

#### Worked solution

```javascript
const values = [5, -2, 3, -2, 6];
const k = 2;
let sum = values[0] + values[1];
let best = sum;
let bestStart = 0;
for (let right = k; right < values.length; right++) {
  sum += values[right] - values[right - k];
  if (sum <= best) {
    best = sum;
    bestStart = right - k + 1;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
1 2
```

The sums are 3, 1, 1 and 4. The inclusive comparison updates the result at both occurrences of 1, retaining start 2. The initial sum is written explicitly because the specified window size is two; a general implementation should use the initialization loop.

## 7. Fixed-window variations

### Moving averages

A moving average summarizes the most recent fixed number of observations. For temperatures `[20, 22, 24, 26, 28]`, calculate every complete three-reading average. The sum changes as before; divide each completed sum by the constant window size.

```javascript
const values = [20, 22, 24, 26, 28];
const k = 3;
let sum = 0;
const averages = [];
for (let i = 0; i < values.length; i++) {
  sum += values[i];
  if (i >= k) {
    sum -= values[i - k];
  }
  if (i >= k - 1) {
    averages.push(sum / k);
  }
}
console.log(averages.join(' '));
```

Expected output:

```text
22 24 26
```

This single-loop variation grows the initial window before sliding it. At index 2, three values have arrived and the first average is ready. At index 3, adding 26 temporarily creates a four-element sum; subtracting `values[0]` restores the required size. The condition `i >= k - 1` prevents reporting incomplete windows. If partial averages were required, divide by the actual number of elements instead during initialization.

### Counting qualifying elements in a window

Find how many windows of size three in `[1, 0, 1, 1, 0, 1]` contain at least two ones. Maintain a count rather than a sum of arbitrary readings. Each incoming one increases the count; each outgoing one decreases it.

```javascript
const values = [1, 0, 1, 1, 0, 1];
const k = 3;
let ones = 0;
let qualifying = 0;
for (let right = 0; right < values.length; right++) {
  if (values[right] === 1) ones++;
  if (right >= k && values[right - k] === 1) ones--;
  if (right >= k - 1 && ones >= 2) qualifying++;
}
console.log(qualifying);
```

Expected output:

```text
4
```

There are two separate counts. `ones` describes the current window; `qualifying` describes the number of completed windows that satisfy the requirement. Resetting `ones` on every iteration would lose the retained information.

### Window frequency tables and distinct values

Suppose we need the number of distinct values in each window of size three in `[1, 2, 1, 3, 2]`. A set alone is insufficient for removal: when one occurrence leaves, another occurrence of the same value may remain. We need occurrence counts.

Recall that a `Map` associates keys with values. `new Map()` creates an empty map; `has(key)` tests presence, `get(key)` retrieves the associated value (or undefined when absent), `set(key, value)` stores a value, `delete(key)` removes a key, and the `size` property counts keys. For example, setting key 1 to count 2 means two occurrences, not two keys. We keep only keys whose counts are positive.

```javascript
const values = [1, 2, 1, 3, 2];
const k = 3;
const counts = new Map();
const result = [];
for (let right = 0; right < values.length; right++) {
  const incoming = values[right];
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  if (right >= k) {
    const outgoing = values[right - k];
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) {
      counts.delete(outgoing);
    } else {
      counts.set(outgoing, remaining);
    }
  }
  if (right >= k - 1) result.push(counts.size);
}
console.log(result.join(' '));
```

Expected output:

```text
2 3 3
```

For the first window, the map stores 1 -> 2 and 2 -> 1. When 3 enters, it receives count one. When the first 1 leaves, its count decreases from two to one, so it must not be deleted. The new map has three keys. Keeping zero-count keys would make `size` overstate the number of distinct values.

### Practice 7 - Moving average and frequency

For `[2, 4, 2, 8]`, report the average and number of distinct values for each complete window of size two.

#### Worked solution

```javascript
const values = [2, 4, 2, 8];
const k = 2;
let sum = 0;
const counts = new Map();
for (let i = 0; i < values.length; i++) {
  const incoming = values[i];
  sum += incoming;
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  if (i >= k) {
    const outgoing = values[i - k];
    sum -= outgoing;
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) counts.delete(outgoing);
    else counts.set(outgoing, remaining);
  }
  if (i >= k - 1) console.log(sum / k, counts.size);
}
```

Expected output:

```text
3 2
3 2
5 2
```

Both aggregates describe the same window. When an incoming value equals the outgoing value, adding and removing one occurrence leaves the final count unchanged. The sum likewise remains unchanged. The two aggregate updates must finish before reporting.

## 8. Variable-size sliding windows

A variable-size window changes its length according to a condition. Rather than advancing both boundaries together, we expand the right boundary and shrink the left boundary when necessary. The appropriate shrinking rule depends on the problem.

### Longest range with sum at most a limit

Given nonnegative values `[2, 1, 4, 1, 1, 3]` and limit 5, find the longest contiguous range whose sum does not exceed 5. Report the earliest start among ties. The expected length is 3 at start 3 (values 1, 1, 3).

We begin with an empty window and sum zero. Adding a value cannot decrease the sum because all values are nonnegative. If the sum exceeds the limit, remove values from the left until it becomes valid again. Removing a nonnegative value cannot increase the sum. These properties justify the direction of pointer movement.

```javascript
const values = [2, 1, 4, 1, 1, 3];
const limit = 5;
let left = 0;
let sum = 0;
let bestLength = 0;
let bestStart = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (sum > limit) {
    sum -= values[left];
    left++;
  }
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
console.log(bestLength, bestStart);
```

Expected output:

```text
3 3
```

| right | Added | Sum before shrinking | Removed | left after shrinking | Valid sum | Length |
|---:|---:|---:|---|---:|---:|---:|
| 0 | 2 | 2 | none | 0 | 2 | 1 |
| 1 | 1 | 3 | none | 0 | 3 | 2 |
| 2 | 4 | 7 | 2 | 1 | 5 | 2 |
| 3 | 1 | 6 | 1 | 2 | 5 | 2 |
| 4 | 1 | 6 | 4 | 3 | 2 | 2 |
| 5 | 3 | 5 | none | 3 | 5 | 3 |

At right index 2, adding 4 makes the sum 7. Subtracting `values[left]` removes 2. Incrementing `left` changes the represented window from `[0, 2]` to `[1, 2]`. The length expression `right - left + 1` counts both included endpoints. At the final iteration it is `5 - 3 + 1 = 3`.

The inner loop is a `while`, not an `if`, because removing one element may be insufficient. For `[1, 1, 10]` with limit 2, all three elements must be removed when 10 enters. The resulting empty window has `left = right + 1` and length zero. With nonnegative values and a nonnegative limit, the empty sum is always valid, so shrinking cannot run beyond the window.

Why is it safe never to move left backward? A discarded start produced a sum greater than the limit. Extending that same range to the right with nonnegative values cannot make it valid later. The algorithm retains the earliest valid start for each right endpoint, giving the longest valid window ending there.

### Shortest range reaching a target

Now find the shortest nonempty contiguous range whose sum is at least 7 in `[2, 3, 1, 2, 4, 3]`. The expected length is two at start 4.

The condition has changed. Previously we shrank invalid windows until valid. Here we shrink valid windows to discover whether a shorter valid window exists. Record a candidate before removing its leftmost element.

```javascript
const values = [2, 3, 1, 2, 4, 3];
const target = 7;
let left = 0;
let sum = 0;
let bestLength = values.length + 1;
let bestStart = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (left <= right && sum >= target) {
    const length = right - left + 1;
    if (length < bestLength) {
      bestLength = length;
      bestStart = left;
    }
    sum -= values[left];
    left++;
  }
}
if (bestStart === -1) console.log('No matching range');
else console.log(bestLength, bestStart);
```

Expected output:

```text
2 4
```

`values.length + 1` is larger than any possible valid length, so the first candidate replaces it. The explicit `left <= right` guard prevents considering empty ranges. Assume nonnegative values and a positive target. At right index 3 the sum reaches 8, and the length-four window is recorded. Removing 2 leaves 6, so shrinking stops. At right index 4 the sum becomes 10. Removing 3 after recording leaves 7, allowing another valid candidate of length three. At right index 5, the window `[4, 5]` reaches 7 and improves the answer to two.

A discarded valid start cannot improve the shortest result by extending farther right: any future window with that start would be longer than the one already recorded. This is the justification for permanent removal in this problem.

### Longest substring without repeated characters

The same strategy can maintain a condition other than a sum. Given `"abcaefbb"`, find the longest contiguous substring with no repeated characters. The expected result is `"bcaef"`, length five.

Use a set of characters in the current window. A set stores distinct values; `new Set()` creates an empty set, `has` tests membership, `add` inserts a value and `delete` removes it. Because the maintained window has no duplicates, deleting a character removes its only occurrence.

```javascript
const text = 'abcaefbb';
const seen = new Set();
let left = 0;
let bestStart = 0;
let bestLength = 0;
for (let right = 0; right < text.length; right++) {
  const incoming = text[right];
  while (seen.has(incoming)) {
    seen.delete(text[left]);
    left++;
  }
  seen.add(incoming);
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
let result = '';
for (let i = bestStart; i < bestStart + bestLength; i++) {
  result += text[i];
}
console.log(result, bestLength);
```

Expected output:

```text
bcaef 5
```

When the second `a` arrives, the set contains `a`, `b`, `c`. Remove the first `a`, move left to index 1, and insert the new `a`. Later, when a repeated `b` arrives, remove characters until the previous `b` has left. The set is updated before calculating the candidate length. This example compares UTF-16 code units, suitable for the given English text.

### Practice 8 - Longest range with zeros

Find the longest contiguous range in `[0, 2, 0, 3, 0, 0]` with sum at most 3. Report the earliest start.

#### Worked solution

```javascript
const values = [0, 2, 0, 3, 0, 0];
let left = 0;
let sum = 0;
let best = 0;
let start = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (sum > 3) {
    sum -= values[left];
    left++;
  }
  const length = right - left + 1;
  if (length > best) {
    best = length;
    start = left;
  }
}
console.log(best, start);
```

Expected output:

```text
4 2
```

When 3 enters at index 3, the sum is 5. Removing the first zero does not reduce the sum, but advancing left still makes progress. Removing 2 reduces the sum to 3. The final window spans indexes 2 through 5 and includes three zeros. Zero values do not invalidate the monotonicity reasoning.

### Practice 9 - Shortest qualifying range and no match

For `[1, 1, 6, 2]`, find the shortest range with sum at least 6. Then consider target 11.

#### Worked solution

```javascript
const values = [1, 1, 6, 2];
const targets = [6, 11];
for (let t = 0; t < targets.length; t++) {
  let left = 0;
  let sum = 0;
  let best = values.length + 1;
  for (let right = 0; right < values.length; right++) {
    sum += values[right];
    while (left <= right && sum >= targets[t]) {
      const length = right - left + 1;
      if (length < best) best = length;
      sum -= values[left];
      left++;
    }
  }
  if (best > values.length) console.log('No matching range');
  else console.log(best);
}
```

Expected output:

```text
1
No matching range
```

When 6 arrives, the sum is 8. The algorithm records lengths three, two and one while removing the preceding ones. The entire input totals 10, so no range can reach 11. Reset all window state for each target; otherwise one query contaminates the next.

## 9. Window validity and limitations

### Why negative values can invalidate variable sum windows

Consider `[5, -4, 2]` with limit 3. The longest valid range is the entire array, whose sum is 3. The earlier longest-window algorithm would discard 5 immediately because it exceeds the limit. When negative 4 arrives, that discarded start would have become valid again. The algorithm cannot recover it because left never moves backward.

The failure is not caused by negative numbers in every sliding-window problem. Fixed-size rolling sums still work with negative values. The failure arises specifically because the variable-window elimination rule assumed that extending a range cannot decrease its sum.

A correct general fallback is to examine all contiguous ranges while maintaining a running sum for each start.

```javascript
const values = [5, -4, 2];
const limit = 3;
let bestLength = 0;
let bestStart = -1;
for (let start = 0; start < values.length; start++) {
  let sum = 0;
  for (let end = start; end < values.length; end++) {
    sum += values[end];
    const length = end - start + 1;
    if (sum <= limit && length > bestLength) {
      bestLength = length;
      bestStart = start;
    }
  }
}
console.log(bestLength, bestStart);
```

Expected output:

```text
3 0
```

Do not break the inner loop when the sum exceeds the limit: a later negative value may restore validity. This approach examines all ranges, but avoids recomputing each range from scratch. More advanced algorithms exist, but require additional techniques beyond this part.

### Counting ranges with an exact sum, including negatives

For `[1, -1, 1, 0]`, count nonempty contiguous ranges whose sum equals 1. Prefix sums offer another solution. If the current prefix is `p`, an earlier prefix must equal `p - target` for their difference to be the target. We can store how often each earlier prefix occurred in a map.

```javascript
const values = [1, -1, 1, 0];
const target = 1;
const counts = new Map();
counts.set(0, 1);
let prefix = 0;
let answer = 0;
for (let i = 0; i < values.length; i++) {
  prefix += values[i];
  const needed = prefix - target;
  if (counts.has(needed)) answer += counts.get(needed);
  let previousCount = 0;
  if (counts.has(prefix)) previousCount = counts.get(prefix);
  counts.set(prefix, previousCount + 1);
}
console.log(answer);
```

Expected output:

```text
5
```

| i | Current prefix | Needed earlier prefix | New matches | Total matches |
|---:|---:|---:|---:|---:|
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | -1 | 0 | 1 |
| 2 | 1 | 0 | 2 | 3 |
| 3 | 1 | 0 | 2 | 5 |

The initial zero prefix represents the boundary before the first element. Without it, ranges starting at index zero would be missed. Count matches before inserting the current prefix; otherwise a target of zero would count an empty range. Multiple earlier occurrences of the needed prefix correspond to different starting boundaries, so add their count rather than merely one.

### Why maximums cannot be updated like sums

If a window contains `[9, 2, 4]`, its maximum is 9. When 9 leaves and 3 enters, the new maximum is 4. Knowing only the old maximum and the incoming and outgoing values is insufficient to determine 4. One straightforward solution is to rescan each window. A more advanced solution maintains candidate positions in a special ordered queue, but that structure is outside this part. Do not apply the rolling-sum subtraction rule to a maximum.

### Work and numerical correctness

A direct fixed-window scan performs approximately `k` additions per window. A rolling sum performs one initialization traversal and two arithmetic updates per shift. A variable window may contain a nested while loop, but each element enters once and leaves at most once; the total number of pointer advances is bounded by twice the input length. This is why nested syntax alone does not establish computational cost.

JavaScript Number arithmetic can round fractional values or large integers. Repeated addition and subtraction may accumulate error; prefix subtraction can also lose small differences between large totals. For exact monetary calculations, represent amounts in integer minor units when values remain within the safe integer range. Do not assume an exact equality comparison is reliable for arbitrary floating-point totals. Choose a numerical representation appropriate to the requirements.

### Practice 10 - Explain a failed elimination

Why does the longest-sum-at-most algorithm fail for `[4, -3]` with limit 1? Would a fixed window of size two fail for the same input?

#### Worked answer

The variable algorithm discards 4 when the first window exceeds 1. Adding negative 3 would have made the original two-element range valid, but its start was lost. A fixed window of size two calculates `4 + (-3) = 1` correctly. Fixed-size updates do not discard possible starts based on the sign of the sum.

## 10. Integrated exercises

Attempt each problem before consulting its worked solution.

### Exercise 10.1 - Sales range report

Given `[10, 0, 20, 5, 15]`, report total sales and number of positive-sales days for half-open ranges `[0, 3)`, `[2, 5)` and `[3, 3)`.

#### Worked solution

```javascript
const sales = [10, 0, 20, 5, 15];
const totals = [0];
const days = [0];
for (let i = 0; i < sales.length; i++) {
  totals.push(totals[i] + sales[i]);
  let positive = 0;
  if (sales[i] > 0) positive = 1;
  days.push(days[i] + positive);
}
const ranges = [[0, 3], [2, 5], [3, 3]];
for (let i = 0; i < ranges.length; i++) {
  const start = ranges[i][0];
  const stop = ranges[i][1];
  console.log(totals[stop] - totals[start], days[stop] - days[start]);
}
```

Expected output:

```text
30 2
40 3
0 0
```

Both arrays use the same boundary convention. The total prefix is `[0, 10, 10, 30, 35, 50]`; the positive-day prefix is `[0, 1, 1, 2, 3, 4]`. For the second query, subtract boundary 2 from boundary 5 in both arrays. The empty range cancels both aggregates.

### Exercise 10.2 - Product except self with two zeros

For `[0, 2, 0, 5]`, calculate every product excluding the current element, without division.

#### Worked solution

```javascript
const values = [0, 2, 0, 5];
const result = [];
let before = 1;
for (let i = 0; i < values.length; i++) {
  result.push(before);
  before *= values[i];
}
let after = 1;
for (let i = values.length - 1; i >= 0; i--) {
  result[i] *= after;
  after *= values[i];
}
console.log(result.join(' '));
```

Expected output:

```text
0 0 0 0
```

Every excluded position leaves at least one zero among the remaining factors. The forward pass stores `[1, 0, 0, 0]`. The backward pass multiplies each by the product strictly to its right. This handles zeros naturally without special division rules.

### Exercise 10.3 - Best three-day average

For `[6, 3, 9, 12, 0, 6]`, report the earliest three-day window with greatest average, including its start and average.

#### Worked solution

```javascript
const values = [6, 3, 9, 12, 0, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) sum += values[i];
let best = sum;
let start = 0;
for (let right = k; right < values.length; right++) {
  sum += values[right] - values[right - k];
  if (sum > best) {
    best = sum;
    start = right - k + 1;
  }
}
console.log(start, best / k);
```

Expected output:

```text
1 8
```

Since all windows have equal size, comparing sums is equivalent to comparing averages. The sums are 18, 24, 21 and 18. Divide only the best sum by three at the end. This avoids unnecessary division during each iteration.

### Exercise 10.4 - Longest range containing at most two distinct values

Given `[1, 2, 1, 3, 3, 2, 2]`, find the longest contiguous range containing at most two distinct values. Report earliest start and length.

#### Worked solution

```javascript
const values = [1, 2, 1, 3, 3, 2, 2];
const counts = new Map();
let left = 0;
let bestStart = -1;
let bestLength = 0;
for (let right = 0; right < values.length; right++) {
  const incoming = values[right];
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  while (counts.size > 2) {
    const outgoing = values[left];
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) counts.delete(outgoing);
    else counts.set(outgoing, remaining);
    left++;
  }
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
console.log(bestStart, bestLength);
```

Expected output:

```text
3 4
```

When 3 first enters, there are three distinct values. Removing the first 1 leaves another 1, so the number of keys stays three. Removing 2 eliminates its key and restores validity. Later, when 2 returns, removing the remaining 1 restores validity again. The final window `[3, 3, 2, 2]` begins at index 3. Extending a window cannot reduce its distinct count, so discarded starts cannot become valid through rightward extension alone.

### Exercise 10.5 - Exact zero-sum ranges

Count all nonempty zero-sum ranges in `[0, 1, -1, 0]`.

#### Worked solution

```javascript
const values = [0, 1, -1, 0];
const counts = new Map();
counts.set(0, 1);
let prefix = 0;
let answer = 0;
for (let i = 0; i < values.length; i++) {
  prefix += values[i];
  let previous = 0;
  if (counts.has(prefix)) previous = counts.get(prefix);
  answer += previous;
  counts.set(prefix, previous + 1);
}
console.log(answer);
```

Expected output:

```text
6
```

The boundary prefixes are `[0, 0, 1, 0, 0]`. Every pair of distinct zero-prefix boundaries defines a nonempty zero-sum range. Four such boundaries produce six pairs. The loop counts them incrementally: 1, 0, 2 and 3 new matches. Inserting the current boundary before counting would incorrectly include empty ranges.

### Exercise 10.6 - Verify a rolling algorithm independently

For `[3, -5, 7, 0, 2]`, calculate every window sum of size three using both direct accumulation and rolling updates. Compare the two result arrays element by element.

#### Worked solution

```javascript
const values = [3, -5, 7, 0, 2];
const k = 3;
const direct = [];
const rolling = [];
for (let start = 0; start <= values.length - k; start++) {
  let sum = 0;
  for (let i = start; i < start + k; i++) sum += values[i];
  direct.push(sum);
}
let sum = 0;
for (let i = 0; i < values.length; i++) {
  sum += values[i];
  if (i >= k) sum -= values[i - k];
  if (i >= k - 1) rolling.push(sum);
}
let equal = direct.length === rolling.length;
for (let i = 0; i < direct.length && equal; i++) {
  if (direct[i] !== rolling[i]) equal = false;
}
console.log(direct.join(' '));
console.log(rolling.join(' '));
console.log(equal);
```

Expected output:

```text
5 2 9
5 2 9
true
```

The direct implementation serves as an independent reference for the optimized implementation. Check lengths before comparing elements. Testing negative values helps establish that fixed windows do not depend on nonnegative inputs. Additional useful tests include size one, size equal to the array length, all zeros and invalid sizes (which should be rejected before calculation).

## 11. Summary

| Technique | Retained information | Main requirement |
|---|---|---|
| Prefix sum | Totals before boundaries | Valid range boundaries; unchanged source |
| Suffix sum | Totals from boundaries onward | Backward dependency order |
| Prefix/suffix combination | Information before and after a position | Correct exclusion boundaries |
| Fixed window | Aggregate of current fixed-size range | Valid size; correct removal and addition |
| Variable window | Aggregate and movable boundaries | Justified permanent elimination of starts |
| Prefix frequency map | Counts of earlier prefix totals | Query before inserting current boundary |

When deriving a solution, first identify whether the problem concerns arbitrary range queries, every range of a fixed size, or a range whose size depends on validity. Determine what information can be retained, how it changes when a boundary moves, and why any discarded candidate cannot become useful again. Verify the initial state, boundary cases, update order and tie policy.

[Previous: Part 1](21-part-01-two-pointer-and-multi-pointer-techniques.md) | [Back to contents](#table-of-contents) | Next: Part 3 - Processing consecutive values, circular iteration and chunks (planned)
