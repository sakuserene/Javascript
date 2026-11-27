# Lesson 21 - Part 6: Comprehensive Integrated Exercises

[Previous](21-part-05-sorting-with-loops.md) | [Complete lesson](21-advanced-loop-techniques.md) | Next lesson: Converting and Refactoring Loops (planned)

## Introduction

Attempt each problem before reading its worked solution. Use traditional loops, dense arrays and small finite integer values unless otherwise stated. Verify the output, then explain why the loop terminates and why its updates preserve correctness.

## Contents

- [1. Pair search after sorting](#1-pair-search-after-sorting)
- [2. Best circular three-day total](#2-best-circular-three-day-total)
- [3. Range totals after sorting](#3-range-totals-after-sorting)
- [4. Compact then group into batches](#4-compact-then-group-into-batches)
- [5. Merge and count runs](#5-merge-and-count-runs)
- [6. Longest affordable consecutive range](#6-longest-affordable-consecutive-range)
- [7. Balance position with prefix and suffix reasoning](#7-balance-position-with-prefix-and-suffix-reasoning)
- [8. Stable sorting and top records](#8-stable-sorting-and-top-records)

## 1. Pair search after sorting

### Problem

Given [8, 1, 6, 3, 4], find two distinct elements totaling 10. Preserve the original array. Return their values, not original indexes.

Expected output:

```text
4 6
8,1,6,3,4
```

### Worked solution

Copy the input, then use insertion sort to establish the ordering required by two-pointer search. The sorted copy is [1, 3, 4, 6, 8]. Start at its extremes. A sum below the target eliminates the left candidate; a sum above it eliminates the right candidate.

```javascript
const input = [8, 1, 6, 3, 4];
const a = [];
for (let i = 0; i < input.length; i++) a.push(input[i]);
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
let left = 0;
let right = a.length - 1;
let found = false;
while (left < right) {
  const sum = a[left] + a[right];
  if (sum === 10) {
    console.log(a[left], a[right]);
    found = true;
    break;
  }
  if (sum < 10) left++;
  else right--;
}
if (!found) console.log("No pair");
console.log(input.join(","));
```

### Explanation and correctness

The sums are 9, 11, 9, and 10. In insertion sort, j + 1 is the vacant position after shifting larger values. The copy preserves the original input. Empty and singleton arrays cannot form pairs. Sorting adds work; for one query a nested search may be simpler.

## 2. Best circular three-day total

### Problem

For daily values [8, 1, 2, 7, 6], find the largest total of three consecutive days, allowing wraparound. Report the earliest starting index on ties.

Expected output:

```text
3 21
```

### Worked solution

Build the first window once. For each new start, subtract the outgoing value and add the incoming value. Use remainder to wrap the incoming index.

```javascript
const a = [8, 1, 2, 7, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) sum += a[i];
let best = sum;
let start = 0;
for (let i = 1; i < a.length; i++) {
  sum -= a[i - 1];
  sum += a[(i + k - 1) % a.length];
  if (sum > best) {
    best = sum;
    start = i;
  }
}
console.log(start, best);
```

### Explanation and correctness

The totals are 11, 10, 15, 21, 15. At start 3, the incoming index is (3 + 3 - 1) % 5 = 0, so 8 enters. Strict > preserves the earliest tie. Require a nonempty array and 1 <= k <= length; initialize best from the first window, not zero, to handle negative totals.

## 3. Range totals after sorting

### Problem

Sort [9, 2, 5, 2, 7] and calculate the sum of all values between 2 and 5 inclusive using binary boundaries and a prefix array.

Expected output:

```text
3 9
```

### Worked solution

Sort first. The lower boundary is the first index with value >= 2; the upper boundary is the first index with value > 5. Both searches use a half-open interval. Build prefix totals with an initial zero.

```javascript
const a = [9, 2, 5, 2, 7];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
const prefix = [0];
for (let i = 0; i < a.length; i++) prefix.push(prefix[i] + a[i]);
let left = 0;
let right = a.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (a[mid] < 2) left = mid + 1;
  else right = mid;
}
const start = left;
left = 0;
right = a.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (a[mid] <= 5) left = mid + 1;
  else right = mid;
}
console.log(left - start, prefix[left] - prefix[start]);
```

### Explanation and correctness

Sorted values are [2,2,5,7,9]. Prefix totals are [0,2,4,9,16,25]. Boundaries are 0 and 3. Subtracting prefix[0] from prefix[3] gives 9. The upper search uses <= to include every copy of the upper endpoint. If no values qualify, equal boundaries give zero. Require lower endpoint <= upper endpoint.

## 4. Compact then group into batches

### Problem

Remove negative values from [4,-1,2,7,-3,5,8] in place, then report totals for consecutive batches of two, retaining the incomplete final batch.

Expected output:

```text
6
12
8
```

### Worked solution

Compaction keeps a write pointer behind or equal to the read pointer. After truncation, traverse the retained values in steps of two. Bound the inner loop by both the batch end and the array length.

```javascript
const a = [4,-1,2,7,-3,5,8];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (a[read] >= 0) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
for (let start = 0; start < a.length; start += 2) {
  let sum = 0;
  for (let i = start; i < start + 2 && i < a.length; i++) sum += a[i];
  console.log(sum);
}
```

### Explanation and correctness

The retained sequence is [4,2,7,5,8]. Batch starts are 0, 2, 4. At start 4 the inner condition stops at index 5 rather than reading undefined. If all values are rejected, no batches are printed. The accumulator resets once per batch, not once per element.

## 5. Merge and count runs

### Problem

Merge [1,2,2,5] and [2,3,5,5], then report each value and its total frequency.

Expected output:

```text
1 1
2 3
3 1
5 3
```

### Worked solution

Use sorted merging to make equal values adjacent. Then process consecutive runs, keeping a current value and count. Flush the final run after traversal.

```javascript
const a = [1,2,2,5];
const b = [2,3,5,5];
const merged = [];
let i = 0;
let j = 0;
while (i < a.length || j < b.length) {
  if (i < a.length && (j >= b.length || a[i] <= b[j])) {
    merged.push(a[i]);
    i++;
  } else {
    merged.push(b[j]);
    j++;
  }
}
if (merged.length > 0) {
  let value = merged[0];
  let count = 1;
  for (let k = 1; k < merged.length; k++) {
    if (merged[k] === value) count++;
    else {
      console.log(value, count);
      value = merged[k];
      count = 1;
    }
  }
  console.log(value, count);
}
```

### Explanation and correctness

The merged sequence is [1,2,2,2,3,5,5,5]. At index 4, the value changes from 2 to 3, so the count of 3 occurrences of 2 is printed before resetting. The final run has no following different value to trigger printing, hence the final console.log. Empty inputs are guarded.

## 6. Longest affordable consecutive range

### Problem

Given nonnegative costs [2,4,1,3,2,1] and budget 6, find the longest consecutive range costing at most 6. Report its starting index and length, keeping the earliest tie.

Expected output:

```text
2 3
```

### Worked solution

Expand the right boundary one element at a time. While the total exceeds the budget, remove elements from the left. Nonnegative costs ensure that adding elements cannot reduce the total.

```javascript
const a = [2,4,1,3,2,1];
let left = 0;
let sum = 0;
let bestLength = 0;
let bestStart = -1;
for (let right = 0; right < a.length; right++) {
  sum += a[right];
  while (sum > 6) {
    sum -= a[left];
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

### Explanation and correctness

At right 2 the total is 7; remove 2 to obtain 5. At right 3 the total is 8; remove 4 to obtain 4. At right 4 the total is 6, covering indexes 2 through 4. Length is 4 - 2 + 1 = 3. At right 5 another length-three range occurs, but strict > retains the earlier range. Negative costs invalidate the elimination argument; require a nonnegative budget.

## 7. Balance position with prefix and suffix reasoning

### Problem

For [2,1,3,1,2], find every index where the sum strictly before it equals the sum strictly after it.

Expected output:

```text
2
```

### Worked solution

Calculate the total once. Maintain the sum before the current index. The sum after it equals total minus the preceding sum minus the current value.

```javascript
const a = [2,1,3,1,2];
let total = 0;
for (let i = 0; i < a.length; i++) total += a[i];
let before = 0;
for (let i = 0; i < a.length; i++) {
  const after = total - before - a[i];
  if (before === after) console.log(i);
  before += a[i];
}
```

### Explanation and correctness

At index 2, total is 9, before is 3, and after is 9 - 3 - 3 = 3. Update before only after comparison, otherwise the current element is counted twice. A singleton balances at index zero. An empty array has no balance index. Negative integers are permitted.

## 8. Stable sorting and top records

### Problem

Sort [{name:"A",score:7},{name:"B",score:9},{name:"C",score:9},{name:"D",score:5}] by descending score, preserving input order for equal scores. Print the top two names.

Expected output:

```text
B
C
```

### Worked solution

Insertion sort shifts only strictly smaller scores. Equal scores must not be shifted past one another. After sorting, traverse at most two positions.

```javascript
const a = [{name:"A",score:7},{name:"B",score:9},{name:"C",score:9},{name:"D",score:5}];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j].score < key.score) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
for (let i = 0; i < 2 && i < a.length; i++) console.log(a[i].name);
```

### Explanation and correctness

When C is inserted, A shifts right because 7 < 9. B does not shift because 9 < 9 is false. C is placed after B. Using <= instead would reverse equal-score records. The final loop also works for fewer than two records.

## Final review

Choose a technique by identifying the property that makes its updates safe: sorted order, overlapping ranges, nonnegative costs, consecutive equal values, or a bounded index range. Do not apply a technique merely because its loop resembles a previous example.

[Previous](21-part-05-sorting-with-loops.md) | [Complete lesson](21-advanced-loop-techniques.md)
