# Lesson 21 - Part 4: Reducing the Search Space and Binary Search

[Previous](21-part-03-consecutive-values-circular-iteration-and-chunks.md) | [Curriculum](README.md) | [Next (planned)](21-part-05-sorting-with-loops.md)

## Learning objectives

Derive safe search boundaries, eliminate candidates using mathematical properties, implement binary search, find duplicate boundaries and insertion positions, and verify termination and correctness.

## Table of contents

- [1. Reducing the search space](#1-reducing-the-search-space)
- [2. Binary search](#2-binary-search)
- [3. Boundary searches](#3-boundary-searches)
- [4. Searching an answer range](#4-searching-an-answer-range)
- [5. Integrated exercises](#5-integrated-exercises)
- [6. Summary](#6-summary)

## 1. Reducing the search space

A search space is the collection of candidates that might satisfy a problem. Searching does not necessarily require examining every candidate. If we can prove that some candidates cannot satisfy the requirements, we can exclude them without testing them individually.

### Factor pairs

Find all positive integer factors of 36. A straightforward loop tests every integer from 1 through 36. But factors occur in pairs: 1 and 36, 2 and 18, 3 and 12, 4 and 9, 6 and 6. At least one member of each pair must be at most the square root of 36. Otherwise their product would exceed 36.

Instead of calculating a square root, we can continue while `d * d <= n`. This condition means that the candidate squared does not exceed the input. When a divisor is found, `n / d` gives its partner.

```javascript
const n = 36;
const factors = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    factors.push(d);
    const partner = n / d;
    if (partner !== d) factors.push(partner);
  }
}
console.log(factors.join(','));
```

Expected: `1,36,2,18,3,12,4,9,6`

Here `n % d` calculates the remainder. A zero remainder establishes divisibility. At d = 4, 36 % 4 is zero and 36 / 4 is 9. At d = 6, the partner is also 6, so we avoid appending it twice. The output is in discovery order, not sorted order.

For sorted output, store the small factors and large partners separately. Small factors arrive in increasing order, whereas partners arrive in decreasing order. Traverse the latter backward.

```javascript
const n = 36;
const small = [], large = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    small.push(d);
    if (d !== n / d) large.push(n / d);
  }
}
for (let i = large.length - 1; i >= 0; i--) small.push(large[i]);
console.log(small.join(','));
```

Expected: `1,2,3,4,6,9,12,18,36`

### Prime testing

A prime is an integer greater than one with no positive divisors other than one and itself. If a composite integer has a nontrivial factor, it has one at or below its square root. We therefore need not test larger candidates. After checking divisibility by two, we can skip even candidates: any even divisor would imply divisibility by two.

```javascript
const n = 97;
let prime = n >= 2;
if (n > 2 && n % 2 === 0) prime = false;
if (prime) {
  for (let d = 3; d * d <= n; d += 2) {
    if (n % d === 0) {
      prime = false;
      break;
    }
  }
}
console.log(prime);
```

Expected: `true`

This example assumes integer input. For 97, the tested odd candidates are 3, 5, 7 and 9. The next candidate, 11, has square 121, so the loop ends. For 49, the candidate 7 establishes that it is composite. Using a strict `<` boundary would miss that factor. For 2 the even-number rejection does not apply and the loop is empty. For 1 the initial flag is false.

### Practice 1

Find the positive factors of 25 without testing candidates above its square root. Explain why 5 appears only once.

#### Worked answer

```javascript
const n = 25;
const factors = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    factors.push(d);
    if (n / d !== d) factors.push(n / d);
  }
}
console.log(factors.join(','));
```

Expected: `1,25,5`

At d = 1 the partner is 25. Candidates 2, 3 and 4 leave nonzero remainders. At d = 5 the partner is 25 / 5 = 5. The equality guard prevents duplicate insertion. The next candidate is 6, whose square exceeds 25.

## 2. Binary search

### From sequential search to elimination

Suppose the sorted array is [2, 5, 8, 12, 16, 23, 38] and the target is 23. Sequential search checks elements from the beginning. Sorted order permits a stronger decision: if the middle element is 12, all elements to its left are at most 12. None can equal 23. We can discard the entire left portion including the middle element.

Binary search repeatedly examines a middle candidate and discards the half that cannot contain the target. It requires sorted input under the same comparison rule used by the search.

### Deriving the midpoint

Maintain an inclusive candidate range from `left` to `right`. Initially left is zero and right is length minus one. For indexes 0 through 6, the middle index is 3. For indexes 4 through 6, it is 5.

```javascript
const left = 4, right = 7;
const mid = left + Math.trunc((right - left) / 2);
console.log(mid);
```

Expected: `5`

The subtraction 7 - 4 gives the distance 3. Division gives 1.5. Math.trunc removes the fractional part, giving 1. Adding the left boundary gives 5. Math.trunc returns a number with its fractional part removed; for nonnegative distances it rounds downward. We need an integer index. Using only the distance divided by two would incorrectly measure from zero instead of the current left boundary.

### Implementing exact search

```javascript
const values = [2, 5, 8, 12, 16, 23, 38];
const target = 23;
let left = 0, right = values.length - 1;
let found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) {
    found = mid;
    break;
  }
  if (values[mid] < target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `5`

| left | right | mid | value | decision |
|---:|---:|---:|---:|---|
| 0 | 6 | 3 | 12 | left = 4 |
| 4 | 6 | 5 | 23 | found |

The middle element has already been tested. If it is too small, `mid + 1` is the first remaining possible position. If too large, `mid - 1` is the last remaining possible position. The candidate interval strictly shrinks on every unsuccessful comparison.

The condition includes equality because a range with one candidate still needs testing. An empty array starts with right = -1 and never enters the loop. A missing target eventually produces left > right, leaving found at -1.

### Incorrect updates and termination

If left = 4 and right = 5, the midpoint is 4. If its value is too small, setting `left = mid` leaves left at 4. The next iteration repeats the same comparison forever. Use `left = mid + 1` in this inclusive exact-search formulation. Similarly, `right = mid - 1` excludes a tested candidate on the other side.

The invariant (a property maintained throughout the loop) is that if the target exists and has not been found, at least one matching index remains within the candidate interval. Sorted order justifies both exclusions. When the interval is empty, no match remains possible.

### Descending order

For descending arrays, smaller values occur to the right. The movement rules must change accordingly.

```javascript
const values = [38, 23, 16, 12, 8, 5, 2];
const target = 5;
let left = 0, right = values.length - 1, found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) { found = mid; break; }
  if (values[mid] > target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `5`

At midpoint 3, the value 12 is greater than 5. In descending order the target must be to the right, so left becomes 4.

### Practice 2

Trace an unsuccessful search for 9 in [2, 4, 6, 8, 10].

#### Worked answer

| left | right | mid | value | update |
|---:|---:|---:|---:|---|
| 0 | 4 | 2 | 6 | left = 3 |
| 3 | 4 | 3 | 8 | left = 4 |
| 4 | 4 | 4 | 10 | right = 3 |

Now 4 <= 3 is false. The result is -1. Notice why the last single-element interval must be tested.

## 3. Boundary searches

Exact binary search returns an arbitrary matching position when duplicates exist. To find the first or last occurrence, we must continue after discovering a match.

### First occurrence

```javascript
const values = [1, 3, 3, 3, 7, 9];
const target = 3;
let left = 0, right = values.length - 1, found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) {
    found = mid;
    right = mid - 1;
  } else if (values[mid] < target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `1`

The first midpoint is 2, a match. Save it before discarding that position and searching earlier indexes. The next midpoint is 0, whose value is 1, so left becomes 1. The final midpoint is 1, a match. Save it and reduce right to zero. The saved answer is the earliest match. For the last occurrence, save each match and move left to mid + 1 instead.

### Lower bound: first value greater than or equal to the target

Sometimes we need a position even when the target is absent. Inserting 4 into [1, 3, 3, 7] should place it at index 3. The lower bound is the first index whose value is at least the target, or the array length if no such value exists.

Use a half-open interval [left, right), with right initially equal to the length. Here right can also represent the insertion boundary at the end. When a middle value is too small, exclude it. Otherwise it might be the first qualifying value, so preserve its index as the right boundary.

```javascript
const values = [1, 3, 3, 7];
const target = 4;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `3`

| left | right | mid | value | update |
|---:|---:|---:|---:|---|
| 0 | 4 | 2 | 3 | left = 3 |
| 3 | 4 | 3 | 7 | right = 3 |

The loop ends when the boundaries meet. Every index before left has been established as too small; every index at or after right within the array qualifies. Unlike inclusive exact search, `right = mid` is correct here because the midpoint may be the answer. The strict loop condition ensures that mid is always smaller than right, so this update makes progress.

### Upper bound and counting duplicates

The upper bound is the first index whose value is strictly greater than the target. Change the lower-bound comparison from `<` to `<=`. The difference between upper and lower bounds counts occurrences, including zero when absent.

```javascript
const values = [1, 3, 3, 3, 7, 9];
const target = 3;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
const lower = left;
left = 0;
right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] <= target) left = mid + 1;
  else right = mid;
}
const upper = left;
console.log(lower, upper, upper - lower);
```

Expected: `1 4 3`

The matching interval is [1, 4), containing indexes 1, 2 and 3. Its length is 4 - 1 = 3. To retrieve the last matching index, use upper - 1 only if upper > lower. Otherwise no matching index exists.

### Practice 3

For [2, 4, 4, 4, 8], determine lower and upper bounds for targets 1, 4, 5 and 10.

#### Worked answer

| Target | Lower | Upper | Count |
|---:|---:|---:|---:|
| 1 | 0 | 0 | 0 |
| 4 | 1 | 4 | 3 |
| 5 | 4 | 4 | 0 |
| 10 | 5 | 5 | 0 |

For target 4, lower-bound search first examines index 2, which qualifies, and sets right to 2. Index 1 qualifies, so right becomes 1. Index 0 is too small, so left becomes 1. Upper-bound search excludes all equal values and stops at index 4. For target 10 every element is too small, so both boundaries reach the array length.

## 4. Searching an answer range

Binary search can operate on an ordered range of possible answers without constructing an array. The requirement is a monotonic predicate: a condition whose truth changes in only one direction across the ordered candidates.

### Smallest integer whose square reaches a threshold

Find the smallest nonnegative integer whose square is at least 50. Candidates 0 through 7 fail; candidates 8 and above succeed. The condition `mid * mid >= target` therefore has a false region followed by a true region. We can locate the boundary just as in lower-bound search.

```javascript
const target = 50;
let left = 0, right = target;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (mid * mid < target) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `8`

For a nonnegative integer target, the target itself is a sufficient upper bound. At target zero the loop is empty and returns zero. At 50 the first midpoint is 25, whose square qualifies; right becomes 25. The next midpoint is 12, which qualifies; right becomes 12. Midpoint 6 fails, so left becomes 7. Midpoint 9 qualifies, then 8 qualifies, then 7 fails. Both boundaries meet at 8. Its square is 64 and the preceding square is 49, verifying minimality.

These examples assume small, exactly represented integer calculations. JavaScript Number arithmetic can lose integer precision for large values; avoid treating rounded products as exact mathematical comparisons near numerical limits.

### Practice 4

Find the smallest positive number of boxes needed to hold 53 items when each box holds at most 8 items, using a binary search over possible box counts.

#### Worked solution

A candidate count qualifies when count * 8 >= 53. Every larger count also qualifies. Zero boxes fail and 53 boxes suffice. Search for the first qualifying count.

```javascript
const items = 53, capacity = 8;
let left = 0, right = items;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (mid * capacity < items) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `7`

Six boxes hold 48 items, which is insufficient. Seven hold 56, which is sufficient. Direct arithmetic would be simpler for this particular problem; the exercise demonstrates the boundary-search technique for more complex monotonic requirements.

## 5. Integrated exercises

### Exercise 1: Count values in an inclusive interval

Given [1, 2, 2, 4, 6, 6, 9], count values from 2 through 6 inclusive. Do not traverse every element.

#### Worked solution

Find the first value >= 2 and the first value > 6. Their index difference is the number of values in the requested interval.

```javascript
const values = [1, 2, 2, 4, 6, 6, 9];
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < 2) left = mid + 1;
  else right = mid;
}
const start = left;
left = 0; right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] <= 6) left = mid + 1;
  else right = mid;
}
console.log(left - start);
```

Expected: `5`

The first search returns 1, the second returns 6. Indexes 1 through 5 qualify. The subtraction 6 - 1 gives 5. This assumes the lower requested value is no greater than the upper requested value; otherwise return zero or reject the range before searching.

### Exercise 2: Find the nearest sorted value

Given [2, 5, 9, 14], find the value closest to 11, choosing the smaller value on ties.

#### Worked solution

The lower bound identifies the first value at least 11. Only that value and its predecessor can be closest: every earlier value is no greater than the predecessor and every later value is no smaller than the lower-bound value. Handle missing neighbors before comparing distances.

```javascript
const values = [2, 5, 9, 14];
const target = 11;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
let nearest;
if (values.length === 0) nearest = undefined;
else if (left === 0) nearest = values[0];
else if (left === values.length) nearest = values[left - 1];
else if (target - values[left - 1] <= values[left] - target) {
  nearest = values[left - 1];
} else nearest = values[left];
console.log(nearest);
```

Expected: `9`

The lower bound is 3. The predecessor is values[2], or 9. The distances are 11 - 9 = 2 and 14 - 11 = 3. The smaller distance wins. Using <= gives the smaller value priority on ties. For a target below every element, the first element is nearest; above every element, the last is nearest. An empty array has no nearest value.

## 6. Summary

Candidate elimination must be justified by a property of the problem, not merely by a desire to perform fewer iterations. Factor searches use paired divisors; binary searches use sorted order or a monotonic condition. Inclusive exact search and half-open boundary search use different termination and update rules. Do not mix them. Binary search approximately halves the remaining interval at each step, while sequential search removes one candidate at a time. Sorting unsorted data solely for a single search may cost more than scanning it directly. Later lessons develop formal performance analysis.

[Previous](21-part-03-consecutive-values-circular-iteration-and-chunks.md) | [Curriculum](README.md) | [Next (planned)](21-part-05-sorting-with-loops.md)
