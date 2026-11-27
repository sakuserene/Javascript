# Lesson 27 - Solving Problems With Loops

[Previous: Lesson 26](26-writing-clean-and-maintainable-loops.md) | [Contents](#table-of-contents) | [Course conclusion](#course-conclusion)

## Table of contents

- [Understanding the problem](#understanding-the-problem)
- [Designing a loop-based solution](#designing-a-loop-based-solution)
- [Recognizing and combining loop patterns](#recognizing-and-combining-loop-patterns)
- [Multi-stage processing](#multi-stage-processing)
- [Evaluating solutions](#evaluating-solutions)
- [Independent problem-solving](#independent-problem-solving)
- [Worked answers](#worked-answers)
- [Course conclusion](#course-conclusion)

## Learning objectives

By the end of this chapter, you should be able to turn requirements into a precise contract, choose traversal boundaries and state, derive update rules, combine familiar algorithms, verify edge cases and justify implementation decisions. This chapter assumes the traditional loops, arrays, strings, functions, Map, sorting, binary search and testing techniques established in earlier lessons. All examples use plain JavaScript loops.

## Understanding the problem

A problem statement is not yet an algorithm. Before deciding which loop to write, establish what counts as a correct result. Consider the request: "Find the longest period of successful transactions." Does a period mean consecutive transactions or any collection of successes? What qualifies as success? What should happen when two periods have equal length?

For this example, specify that the input is a dense array of numbers, each representing a transaction amount. A positive amount is successful. Return the starting index and length of the longest consecutive positive run, preferring the earliest run on ties. If no run exists, return start -1 and length 0. Do not modify the input.

For `[4, 7, 0, 3, 5, 9, -1, 8]`, the answer is start 3, length 3. Counting all positive numbers would give 6, which answers a different question. Sorting would destroy the original adjacency. The contract rules out both approaches before any code is written.

Try a few examples by hand:

| Input | Start | Length | Reason |
|---|---:|---:|---|
| [] | -1 | 0 | No transactions |
| [0,-2] | -1 | 0 | No positive values |
| [5] | 0 | 1 | One successful transaction |
| [2,3,0,4,5] | 0 | 2 | Earliest tie |
| [0,2,3] | 1 | 2 | Run reaches the end |

These are not merely tests to write afterward. They guide the design. In particular, the last case suggests that a solution which records runs only when a failure occurs must also handle the end of the array.

### Turning ambiguous requests into contracts

Suppose a customer asks for "duplicate orders." There are at least three possible outputs for `[7,7,8,7,8]`: repeated occurrences `[7,7,8]`, distinct repeated identifiers `[7,8]`, or a frequency report containing 7 with count 3 and 8 with count 2. Each has different state requirements. Decide which output is needed, whether order matters, and whether identifiers are compared as numbers or strings.

An input contract can restrict a function to dense arrays of finite numbers. This is legitimate when callers guarantee those conditions. If callers do not, validation must be included. Do not silently treat missing values as zero unless the requirements say so.

### Practice 1

Specify a contract for finding the second largest distinct number in a dense array of finite numbers. Include empty input, duplicates, negative values and the result when fewer than two distinct values exist. Predict the results for `[9,9,5,2]`, `[-8,-2,-5]` and `[4,4]`. See [Answer 1](#answer-1).

## Designing a loop-based solution

Return to the successful transaction problem. As we read each transaction, we need to remember the current run and the best run encountered so far. The current run grows on success and resets on failure. The best run must survive those resets.

Start with four state variables: `currentStart`, `currentLength`, `bestStart`, `bestLength`. The current length is initially zero because no transaction has been examined. The best start is -1 because no qualifying run has been found.

When a positive value is encountered and currentLength is zero, the current index begins a new run. Increment currentLength. If it exceeds bestLength, record the new best. Updating immediately avoids a separate final-run flush.

```javascript
function longestSuccessRun(values) {
  let currentStart = -1;
  let currentLength = 0;
  let bestStart = -1;
  let bestLength = 0;
  for (let i = 0; i < values.length; i++) {
    if (values[i] <= 0) {
      currentLength = 0;
      continue;
    }
    if (currentLength === 0) currentStart = i;
    currentLength++;
    if (currentLength > bestLength) {
      bestLength = currentLength;
      bestStart = currentStart;
    }
  }
  return { start: bestStart, length: bestLength };
}
const result = longestSuccessRun([4,7,0,3,5,9,-1,8]);
console.log(result.start, result.length);
```

Expected output:

```text
3 3
```

The `continue` statement skips the positive-value processing but still executes the for-loop update `i++`. The comparison uses `>` rather than `>=`: equal-length runs must not replace an earlier best run.

| i | Value | Current length | Best start | Best length |
|---:|---:|---:|---:|---:|
| 0 | 4 | 1 | 0 | 1 |
| 1 | 7 | 2 | 0 | 2 |
| 2 | 0 | 0 | 0 | 2 |
| 3 | 3 | 1 | 0 | 2 |
| 4 | 5 | 2 | 0 | 2 |
| 5 | 9 | 3 | 3 | 3 |
| 6 | -1 | 0 | 3 | 3 |
| 7 | 8 | 1 | 3 | 3 |

We can derive currentStart instead of storing it. At index 5, a run of length 3 occupies indexes 3, 4, 5. Subtracting length from index gives 2, one position before the run. Adding one gives 3. Thus `i - currentLength + 1` is the inclusive start. This reduces state, but the formula deserves explanation.

```javascript
let current = 0;
let best = 0;
let start = -1;
const values = [0,2,3,4];
for (let i = 0; i < values.length; i++) {
  current = values[i] > 0 ? current + 1 : 0;
  if (current > best) {
    best = current;
    start = i - current + 1;
  }
}
console.log(start, best);
```

Expected output:

```text
1 3
```

The conditional expression chooses one of two values: the previous length plus one on success, or zero on failure. It does not change the best length directly. A common mistake is to reset best when current resets. That loses historical information and makes the result depend on the final run rather than the longest one.

### Choosing a termination condition

Use a for loop when a bounded index traversal is the natural description. A while loop is often clearer when the next state determines whether processing continues. For example, consuming a budget of 10 with costs `[3,4,5,1]` stops before the first unaffordable item. It must not skip that item and purchase later ones.

```javascript
const costs = [3,4,5,1];
let remaining = 10;
let i = 0;
while (i < costs.length && costs[i] <= remaining) {
  remaining -= costs[i];
  i++;
}
console.log(i, remaining);
```

Expected output:

```text
2 3
```

The left operand of `&&` guards the array access. Once i equals length, JavaScript does not evaluate the second operand. The loop terminates because i increases on every iteration and cannot exceed the array length. Assume costs and budget are nonnegative finite numbers. A different requirement allowing skipped purchases needs a different loop.

### Practice 2

Given `[8,6,4,7,5,3,1,2]`, find the start and length of the longest strictly decreasing contiguous run. Count elements, not comparisons. Prefer the earliest tie; return -1 and 0 for empty input. Expected result: 3 and 4. See [Answer 2](#answer-2).

## Recognizing and combining loop patterns

Patterns are tools, not keywords to match blindly. A count needs a counter; a total needs an accumulator; a consecutive run needs resettable state; a membership query may benefit from a Set or Map. A problem can require several simultaneously.

### Building a report in one traversal

Given sales `[12,0,8,-3,15,15]`, report the count and total of strictly positive sales, their average, the largest sale, and the first index of that largest sale. Ignore zero and negative values. Return null for average and maximum when no sale qualifies, and -1 for its index.

The same qualifying condition controls all five outputs. We can combine counting, accumulation and maximum tracking. Initialize maximum to null rather than zero to represent absence explicitly. Use the count to decide whether an average exists.

```javascript
function salesReport(sales) {
  let count = 0;
  let total = 0;
  let max = null;
  let maxIndex = -1;
  for (let i = 0; i < sales.length; i++) {
    const sale = sales[i];
    if (sale <= 0) continue;
    count++;
    total += sale;
    if (max === null || sale > max) {
      max = sale;
      maxIndex = i;
    }
  }
  const average = count === 0 ? null : total / count;
  return {count, total, average, max, maxIndex};
}
const r = salesReport([12,0,8,-3,15,15]);
console.log(r.count, r.total, r.average, r.max, r.maxIndex);
```

Expected output:

```text
4 50 12.5 15 4
```

The first qualifying sale initializes max because `max === null` is true. Subsequent qualifying sales replace it only if larger. The average is calculated after the traversal, since dividing during each iteration would repeatedly compute a value not yet final. This is a useful case of loop fusion: each output depends only on the same current sale and its own state.

### Recognizing a sliding window

Find the maximum total of exactly three consecutive values in `[4,-2,7,1,5]`. The three candidate totals are 9, 6 and 13. Unlike a run of qualifying values, a window has a specified length. Recomputing each sum works, but adjacent windows share two elements.

```javascript
function bestWindow(values, k) {
  if (!Number.isInteger(k) || k < 1 || k > values.length) return null;
  let sum = 0;
  for (let i = 0; i < k; i++) sum += values[i];
  let best = sum;
  let start = 0;
  for (let right = k; right < values.length; right++) {
    sum -= values[right - k];
    sum += values[right];
    if (sum > best) {
      best = sum;
      start = right - k + 1;
    }
  }
  return {start, total: best};
}
const r = bestWindow([4,-2,7,1,5],3);
console.log(r.start, r.total);
console.log(bestWindow([-5,-2,-8],2).total);
```

Expected output:

```text
2 13
-7
```

When right is 3, the outgoing index is 3 - 3 = 0. Remove 4 and add 1: 9 - 4 + 1 = 6. When right is 4, remove -2 (subtracting a negative increases the sum) and add 5, producing 13. Initializing best to zero would incorrectly return zero for all-negative inputs. The first real window supplies a valid initial candidate.

A variable-size window with a sum threshold requires additional reasoning: negative values can invalidate the assumption that shrinking a window always decreases its sum. Do not choose that technique simply because a problem mentions consecutive values.

### Practice 3

Given `[3,5,3,8,5,5]`, return the distinct values in order of first appearance that occur at least twice, together with their counts. Expected lines: `3 2` and `5 3`. See [Answer 3](#answer-3).

## Multi-stage processing

Not every problem should be solved in one loop. A stage may require information that is not available until an earlier traversal completes.

### Ranking eligible records

Given records with name and score, discard scores below 50, rank remaining records in descending score order, and return the first two. Preserve original order on equal scores and do not rearrange the input array.

The stages are filtering, stable sorting and selecting a prefix. Filtering first reduces the number of records to sort. Insertion sort is stable when equal scores are not shifted past each other. Copying record references into a new array is sufficient here because we rearrange references but do not modify record properties.

```javascript
const records = [
  {name:'A',score:70}, {name:'B',score:45},
  {name:'C',score:90}, {name:'D',score:90},
  {name:'E',score:65}
];
const eligible = [];
for (let i = 0; i < records.length; i++) {
  if (records[i].score >= 50) eligible.push(records[i]);
}
for (let i = 1; i < eligible.length; i++) {
  const key = eligible[i];
  let j = i - 1;
  while (j >= 0 && eligible[j].score < key.score) {
    eligible[j + 1] = eligible[j];
    j--;
  }
  eligible[j + 1] = key;
}
for (let i = 0; i < 2 && i < eligible.length; i++) {
  console.log(eligible[i].name, eligible[i].score);
}
console.log(records[0].name);
```

Expected output:

```text
C 90
D 90
A
```

After filtering, the scores are 70, 90, 90, 65. Inserting C shifts A right. Inserting D shifts A but stops at C because 90 < 90 is false. The final prefix contains C then D. Changing `<` to `<=` would reverse equal-score records and violate stability.

The final loop has two boundaries: the requested count and the number available. This handles zero or one eligible record without accessing an absent record. If only the best two are needed and the collection is large, maintaining two ranked candidates in one traversal can avoid sorting the entire collection. That alternative requires careful tie handling and is less general if the requested number changes.

### Computing deviations from the mean

For `[2,4,9]`, produce each value minus the overall average. The total is 15 and the average is 5, so the result is `[-3,-1,4]`. The final average cannot be known before reading all values. A single loop that subtracts the running average computes a different statistic.

```javascript
function deviations(values) {
  if (values.length === 0) return [];
  let total = 0;
  for (let i = 0; i < values.length; i++) total += values[i];
  const average = total / values.length;
  const result = [];
  for (let i = 0; i < values.length; i++) {
    result.push(values[i] - average);
  }
  return result;
}
console.log(deviations([2,4,9]).join(' '));
console.log(deviations([]).length);
```

Expected output:

```text
-3 -1 4
0
```

Two linear passes still have linear time complexity: n additions plus n subtractions grow proportionally to n. The result uses linear output storage. Separating the stages makes their dependency explicit. The empty-input guard avoids dividing by zero.

### Practice 4

For `[2,9,4,5,10]`, return the original indexes of values strictly above the overall mean, preserving order. Expected indexes: 1 and 4. Return an empty array for empty input. See [Answer 4](#answer-4).

## Evaluating solutions

A solution is not finished when it runs once. Evaluate correctness, termination, time, storage, mutation and readability against the contract.

### Comparing two valid approaches

Given a dense array of finite integers, determine whether two distinct indexes contain values whose sum equals a target. For `[8,1,6,3]` and target 9, the answer is true. A single element `[4]` with target 8 must return false.

The direct approach examines unordered pairs. For each i, j begins at i + 1. This excludes self-pairs and avoids examining both (i,j) and (j,i).

```javascript
function hasPairDirect(values, target) {
  for (let i = 0; i < values.length; i++) {
    for (let j = i + 1; j < values.length; j++) {
      if (values[i] + values[j] === target) return true;
    }
  }
  return false;
}
console.log(hasPairDirect([8,1,6,3],9));
console.log(hasPairDirect([4],8));
```

Expected output:

```text
true
false
```

For n values, the worst case examines (n-1)+(n-2)+...+1 = n(n-1)/2 pairs. That is quadratic time and constant auxiliary space.

A different approach stores previously examined values in a Set. For a current value x, the needed partner is target - x. If that partner has already appeared, two distinct indexes exist. Otherwise store x and continue. Assume values, target and relevant arithmetic results are safe integers, so subtraction and addition are exact.

```javascript
function hasPair(values, target) {
  const seen = new Set();
  for (let i = 0; i < values.length; i++) {
    const value = values[i];
    const needed = target - value;
    if (seen.has(needed)) return true;
    seen.add(value);
  }
  return false;
}
console.log(hasPair([8,1,6,3],9));
console.log(hasPair([4],8));
console.log(hasPair([4,4],8));
```

Expected output:

```text
true
false
true
```

At i=0, needed is 1 and the set is empty. Add 8. At i=1, needed is 8, which is present. Checking before adding is essential: adding first would allow a single 4 to match itself for target 8.

The invariant is that before each iteration, seen contains exactly the distinct values at earlier indexes. Therefore a successful membership check proves a valid pair. If no check succeeds, every possible pair has been considered indirectly when its later member was processed. The loop terminates because i advances to length unless it returns earlier.

Under the usual average constant-time Set operation assumption, the second approach takes linear time and up to linear auxiliary space. JavaScript does not guarantee constant-time Set operations in every implementation. The direct approach may be appropriate for very small inputs or tight memory constraints. If the array is already sorted, two pointers offer linear time and constant auxiliary space without a Set.

### Testing beyond the example

Use the direct implementation as a reference for small inputs. Compare the optimized result against it for many cases. This does not prove correctness for all possible inputs, but it can expose overlooked combinations.

```javascript
function direct(a,t) {
  for (let i=0;i<a.length;i++) {
    for (let j=i+1;j<a.length;j++) {
      if (a[i]+a[j]===t) return true;
    }
  }
  return false;
}
function fast(a,t) {
  const seen=new Set();
  for (let i=0;i<a.length;i++) {
    if (seen.has(t-a[i])) return true;
    seen.add(a[i]);
  }
  return false;
}
const cases=[[],[0],[4],[4,4],[-3,3],[1,2,3],[0,0]];
let checked=0;
for (let i=0;i<cases.length;i++) {
  for (let target=-8;target<=8;target++) {
    if (direct(cases[i],target)!==fast(cases[i],target)) {
      throw new Error('Pair search mismatch');
    }
    checked++;
  }
}
console.log(checked);
```

Expected output:

```text
119
```

There are seven arrays and seventeen targets, so 119 comparisons are made. `throw new Error(...)` stops execution if a mismatch occurs. The test includes repeated values, negative values, empty input and zero. It does not cover every safe integer or all array lengths. Combine tests with the invariant reasoning above.

### Practice 5

Explain why a nested-loop search for a pair may be preferable to the Set version for a tiny input. Identify a test that detects adding the current value to the Set before checking its complement. See [Answer 5](#answer-5).

## Independent problem-solving

Attempt these problems before reading the worked answers. For each, write the contract, identify state and traversal, derive the update rules, trace a small example, implement and test. Unless specified otherwise, numeric inputs are dense arrays of finite safe integers and relevant arithmetic stays within the safe integer range.

### Problem 6 - Inventory reconciliation

Initial stock is 5. Process changes `[3,-4,-6,2,-1]` in order. Reject a change if it would make stock negative; rejected changes do not affect stock. Return final stock, rejected indexes and the lowest stock reached, including the initial stock. Expected: final 5, rejected index 2, minimum 4. For no changes, return initial stock, no rejections and initial minimum. See [Answer 6](#answer-6).

### Problem 7 - Consecutive event compression

Compress `['A','A','B','A','A','A']` into consecutive runs represented by value and count. Expected `A 2`, `B 1`, `A 3`. Do not combine separated runs. Empty input produces no runs. See [Answer 7](#answer-7).

### Problem 8 - Longest affordable interval

Given nonnegative costs `[2,1,4,1,1,2]` and budget 5, find the longest contiguous interval with total at most 5. Prefer the earliest start on ties. Expected start 3, length 3. Return -1 and 0 if no nonempty interval qualifies. Include zero-cost values and an empty array in your tests. See [Answer 8](#answer-8).

### Problem 9 - Range query report

Given sorted values `[2,2,4,7,9,9]`, answer inclusive value-range queries `[2,7]`, `[8,9]`, `[3,3]`. For each query return count and total of matching elements. Expected `4 15`, `2 18`, `0 0`. Assume lower <= upper. Prepare data once and avoid scanning the entire array per query. See [Answer 9](#answer-9).

### Problem 10 - Batch quality report

Process readings `[8,12,9,15,3,11,14]` in nonoverlapping batches of three. Keep the incomplete final batch. For each batch report start index, number of readings, count of readings >= 10, and average of all readings. Expected `0 3 1 9.666666666666666`, `3 3 2 9.666666666666666`, `6 1 1 14`. Empty input produces no batches. See [Answer 10](#answer-10).

### Problem 11 - Stable top two

Given records `[{name:'A',score:7},{name:'B',score:9},{name:'C',score:9},{name:'D',score:8}]`, select the best two records without sorting or mutating the input. Higher scores rank first; ties prefer earlier input positions. Expected B then C. Return fewer records if fewer exist. See [Answer 11](#answer-11).

### Problem 12 - Circular maintenance schedule

Given task durations `[4,2,7,1]`, start at index 3 and visit exactly six tasks in forward circular order. Report visited indexes and total duration. Expected indexes `3 0 1 2 3 0`, total 19. Require a valid start for nonempty input and a nonnegative integer visit count. Empty input is permitted only for zero visits. See [Answer 12](#answer-12).

## Worked answers

### Answer 1

The result is the greatest number strictly smaller than the maximum. Return null if it does not exist. The inputs give 5, -5 and null respectively. Keep two candidates, both initially null. A new maximum moves the old maximum into second place; an equal maximum changes neither. A smaller value can replace second only if second is absent or smaller.

```javascript
function secondLargest(values) {
  let first = null;
  let second = null;
  for (let i=0;i<values.length;i++) {
    const x=values[i];
    if (first===null || x>first) {
      second=first;
      first=x;
    } else if (x<first && (second===null || x>second)) {
      second=x;
    }
  }
  return second;
}
console.log(secondLargest([9,9,5,2]));
console.log(secondLargest([-8,-2,-5]));
console.log(secondLargest([4,4]));
console.log(secondLargest([]));
```

Expected output:

```text
5
-5
null
null
```

When -2 replaces -8 as maximum, second becomes -8. Then -5 is smaller than -2 but greater than -8, so it becomes second. Initializing either candidate to zero would fail for negative inputs. The two candidates remain the greatest two distinct values of the processed prefix. Time O(n), auxiliary space O(1).

### Answer 2

A nonempty array starts with a decreasing run of length one. Begin at index 1 so the preceding value exists. If the current value is strictly smaller than the previous value, extend the run; otherwise reset its length to one, since the current element begins a new run.

```javascript
function decreasingRun(a) {
  if (a.length===0) return {start:-1,length:0};
  let current=1;
  let best=1;
  let start=0;
  for (let i=1;i<a.length;i++) {
    if (a[i]<a[i-1]) current++;
    else current=1;
    if (current>best) {
      best=current;
      start=i-current+1;
    }
  }
  return {start,length:best};
}
const r=decreasingRun([8,6,4,7,5,3,1,2]);
console.log(r.start,r.length);
console.log(decreasingRun([5,5]).length);
```

Expected output:

```text
3 4
1
```

At index 3, 7 is not smaller than 4, so current becomes 1. At index 6, current is 4, giving start 6-4+1=3. Equal neighbors reset the run because the requirement is strictly decreasing. The best is updated only on improvement, preserving earliest ties.

### Answer 3

Count each value using a Map and record the first occurrence in a separate array. The second traversal reads the first-occurrence array and emits only counts >= 2. We use plain indexed loops rather than iterator loops.

```javascript
const values=[3,5,3,8,5,5];
const counts=new Map();
const order=[];
for (let i=0;i<values.length;i++) {
  const x=values[i];
  if (!counts.has(x)) {
    counts.set(x,0);
    order.push(x);
  }
  counts.set(x,counts.get(x)+1);
}
for (let i=0;i<order.length;i++) {
  const x=order[i];
  if (counts.get(x)>=2) console.log(x,counts.get(x));
}
```

Expected output:

```text
3 2
5 3
```

The first occurrence initializes a count of zero, then the common update adds one. Later occurrences bypass initialization. At the end order is [3,5,8], with counts 2,3,1. Filtering while counting would not know final frequencies and could emit a value more than once. The two-stage solution uses O(d) auxiliary storage for d distinct values.

### Answer 4

Compute total 30 and mean 6. Traverse again, collecting indexes rather than values. A value equal to the mean does not qualify.

```javascript
function aboveMean(a) {
  if (a.length===0) return [];
  let total=0;
  for (let i=0;i<a.length;i++) total+=a[i];
  const mean=total/a.length;
  const result=[];
  for (let i=0;i<a.length;i++) {
    if (a[i]>mean) result.push(i);
  }
  return result;
}
console.log(aboveMean([2,9,4,5,10]).join(' '));
console.log(aboveMean([5,5]).length);
```

Expected output:

```text
1 4
0
```

The first loop establishes the final mean; the second uses it unchanged. Pushing `a[i]` instead of i would return 9 and 10, violating the output contract. The two passes are O(n), with O(1) auxiliary storage excluding the result.

### Answer 5

For a tiny input, the direct pair search avoids allocating and maintaining a Set, and its simpler state can be easier to audit. Actual speed depends on the engine and input; measure rather than assume. The input `[4]` with target 8 must return false. If 4 is added before checking, the membership check incorrectly finds the current value itself. `[4,4]` with target 8 must return true, so rejecting equal values outright is also incorrect.

### Answer 6

Maintain stock, minimum and rejected indexes. Compute the proposed stock before committing it. If negative, record the index and skip the update. This separates validation from state mutation.

```javascript
function reconcile(initial,changes) {
  let stock=initial;
  let minimum=initial;
  const rejected=[];
  for (let i=0;i<changes.length;i++) {
    const proposed=stock+changes[i];
    if (proposed<0) {
      rejected.push(i);
      continue;
    }
    stock=proposed;
    if (stock<minimum) minimum=stock;
  }
  return {stock,minimum,rejected};
}
const r=reconcile(5,[3,-4,-6,2,-1]);
console.log(r.stock,r.rejected.join(' '),r.minimum);
```

Expected output:

```text
5 2 4
```

| Index | Proposed | Accepted stock | Minimum |
|---:|---:|---:|---:|
| 0 | 8 | 8 | 5 |
| 1 | 4 | 4 | 4 |
| 2 | -2 | 4 | 4 |
| 3 | 6 | 6 | 4 |
| 4 | 5 | 5 | 4 |

Assume initial stock is nonnegative. The invariant is that stock is initial plus all accepted changes so far and never negative. Updating minimum using proposed before validation would incorrectly record -2. A change resulting in exactly zero is valid because the rejection condition is `< 0`, not `<= 0`.

### Answer 7

Start the first run from the first element after checking for empty input. Compare subsequent elements with the current run value. When the value changes, save the completed run and start a new one. After the loop, save the final run because no later change will trigger its emission.

```javascript
function compress(a) {
  const runs=[];
  if (a.length===0) return runs;
  let value=a[0];
  let count=1;
  for (let i=1;i<a.length;i++) {
    if (a[i]===value) count++;
    else {
      runs.push({value,count});
      value=a[i];
      count=1;
    }
  }
  runs.push({value,count});
  return runs;
}
const r=compress(['A','A','B','A','A','A']);
for (let i=0;i<r.length;i++) console.log(r[i].value,r[i].count);
console.log(compress([]).length);
```

Expected output:

```text
A 2
B 1
A 3
0
```

At index 2, B differs from A, so the A run of length 2 is saved. At index 3, the B run is saved. The final A run reaches length 3 and is saved after the loop. Omitting that final statement loses the last run, including the entire input if all elements are equal. This algorithm assumes strings or other values for which strict equality matches the desired grouping rule.

### Answer 8

Expand the right boundary one element at a time. If the sum exceeds the budget, repeatedly remove the leftmost value until the window qualifies. Since costs are nonnegative, adding more values cannot make an already unaffordable prefix affordable; removing it is safe. Once valid, the current window is the longest affordable window ending at right.

```javascript
function affordable(a,budget) {
  let left=0;
  let sum=0;
  let best=0;
  let start=-1;
  for (let right=0;right<a.length;right++) {
    sum+=a[right];
    while (left<=right && sum>budget) {
      sum-=a[left];
      left++;
    }
    const length=right-left+1;
    if (length>best) {
      best=length;
      start=left;
    }
  }
  return {start,length:best};
}
const r=affordable([2,1,4,1,1,2],5);
console.log(r.start,r.length);
console.log(affordable([0,0],0).length);
console.log(affordable([6],5).start);
console.log(affordable([],5).length);
```

Expected output:

```text
3 3
2
-1
0
```

| Right | Left after shrinking | Sum | Length |
|---:|---:|---:|---:|
| 0 | 0 | 2 | 1 |
| 1 | 0 | 3 | 2 |
| 2 | 1 | 5 | 2 |
| 3 | 2 | 5 | 2 |
| 4 | 3 | 2 | 2 |
| 5 | 3 | 4 | 3 |

At right=4, the previous sum 5 grows to 6; removing the cost 4 at left=2 reduces it to 2. The length formula includes both endpoints. If left becomes right+1, length is zero and the empty window cannot replace the best. The inner while may run several times in one iteration, but left advances at most n times overall, so total time is O(n). Assume budget is nonnegative. Negative costs require a different algorithm.

### Answer 9

Build prefix totals with an initial zero. For each query, binary search for the first index whose value is >= lower and the first index whose value is > upper. The resulting half-open interval [left,right) contains precisely the matching elements. Count is right-left and sum is prefix[right]-prefix[left].

```javascript
function bound(a,target,strict) {
  let left=0;
  let right=a.length;
  while (left<right) {
    const mid=left+Math.trunc((right-left)/2);
    if (a[mid]<target || (strict && a[mid]===target)) left=mid+1;
    else right=mid;
  }
  return left;
}
const a=[2,2,4,7,9,9];
const prefix=[0];
for (let i=0;i<a.length;i++) prefix.push(prefix[i]+a[i]);
const queries=[[2,7],[8,9],[3,3]];
for (let i=0;i<queries.length;i++) {
  const left=bound(a,queries[i][0],false);
  const right=bound(a,queries[i][1],true);
  console.log(right-left,prefix[right]-prefix[left]);
}
```

Expected output:

```text
4 15
2 18
0 0
```

The prefix array is [0,2,4,8,15,24,33]. For [2,7], left=0 and right=4. For [8,9], left=4 and right=6. For [3,3], both boundaries equal 2. In the search, `right-left` is the candidate interval length; dividing by two and truncating gives an integer offset; adding left places mid inside the interval. If mid is too small, exclude it with left=mid+1. Otherwise keep it as a possible boundary with right=mid. Each update shrinks the interval. Using a lower bound for the upper endpoint would exclude values equal to upper. Preprocessing costs O(n) time and space; each query costs O(log n) time. For a single query, direct scanning may be simpler.

### Answer 10

The outer loop advances by batch size, not one. The inner loop visits from start up to the smaller of start+3 and array length. Reset all batch state inside the outer loop so earlier batches do not contaminate later reports.

```javascript
const readings=[8,12,9,15,3,11,14];
const size=3;
for (let start=0;start<readings.length;start+=size) {
  const end=Math.min(start+size,readings.length);
  let total=0;
  let qualifying=0;
  for (let i=start;i<end;i++) {
    total+=readings[i];
    if (readings[i]>=10) qualifying++;
  }
  const count=end-start;
  console.log(start,count,qualifying,total/count);
}
```

Expected output:

```text
0 3 1 9.666666666666666
3 3 2 9.666666666666666
6 1 1 14
```

For start=6, end is min(9,7)=7, so only index 6 is visited. Count is 1, not 3. Dividing by the configured size would produce an incorrect final average. The outer condition guarantees every emitted batch is nonempty. Although loops are nested, each reading is visited exactly once, so time is O(n). If size becomes an input, validate it as a positive integer to ensure termination.

### Answer 11

Maintain the best and second-best records encountered so far. When a new record strictly beats best, the old best moves to second. Otherwise it can replace second if strictly better. Equal scores do not displace earlier records, but an equal-best record can occupy second if that position is absent or lower.

```javascript
function topTwo(records) {
  let first=null;
  let second=null;
  for (let i=0;i<records.length;i++) {
    const r=records[i];
    if (first===null || r.score>first.score) {
      second=first;
      first=r;
    } else if (second===null || r.score>second.score) {
      second=r;
    }
  }
  const result=[];
  if (first!==null) result.push(first);
  if (second!==null) result.push(second);
  return result;
}
const r=topTwo([{name:'A',score:7},{name:'B',score:9},{name:'C',score:9},{name:'D',score:8}]);
for (let i=0;i<r.length;i++) console.log(r[i].name);
console.log(topTwo([]).length);
```

Expected output:

```text
B
C
0
```

After A, first=A. B becomes first and A becomes second. C does not beat B but beats A, so C becomes second. D beats neither. Unlike the second-largest-distinct-number problem, equal scores are allowed because the output ranks records, not distinct scores. The invariant is that first and second represent the top two processed positions in stable rank order. Time O(n), constant auxiliary storage. Returned records share references with the input; the function itself does not mutate them.

### Answer 12

The visit count, rather than returning to the start, determines termination. The index advances by one and wraps to zero at length. Modulo expresses that rule: `(index+1)%length` gives zero when index+1 equals length. Validate before using modulo so a zero-length array cannot cause a remainder of NaN.

```javascript
function schedule(a,start,visits) {
  if (!Number.isInteger(visits) || visits<0) throw new Error('Invalid visits');
  if (a.length===0) {
    if (visits!==0) throw new Error('No tasks');
    return {indexes:[],total:0};
  }
  if (!Number.isInteger(start) || start<0 || start>=a.length) {
    throw new Error('Invalid start');
  }
  let index=start;
  let total=0;
  const indexes=[];
  for (let count=0;count<visits;count++) {
    indexes.push(index);
    total+=a[index];
    index=(index+1)%a.length;
  }
  return {indexes,total};
}
const r=schedule([4,2,7,1],3,6);
console.log(r.indexes.join(' '));
console.log(r.total);
console.log(schedule([],0,0).total);
```

Expected output:

```text
3 0 1 2 3 0
19
0
```

The visited durations are 1,4,2,7,1,4. After the first visit, (3+1)%4=0. The count variable increases independently of index, so wrapping cannot prevent termination. Stopping when index returns to start would visit only four tasks. Time O(visits), output storage O(visits), auxiliary storage O(1).

## Course conclusion

You have now reached the final chapter of the Plain Loops in JavaScript curriculum. Completing the notes is not the same as mastering the material. Use the independent exercises to identify concepts that need further practice.

For future problems, establish the contract first. Work through representative and boundary examples by hand. Identify which information must persist between iterations, what must reset, and how the loop makes progress. Derive the condition and updates before writing the complete implementation. Verify correctness using invariants and tests, then evaluate computational cost and readability. Prefer a straightforward correct implementation before introducing an optimization whose assumptions you cannot justify.

A useful final mastery check is to solve the independent problems without looking at the answers, explain each important expression aloud, change one requirement, adapt the implementation, and construct tests that would expose an incorrect solution. Revisit earlier chapters when a technique is unfamiliar.

The scope of this curriculum is traditional for, while and do...while loops. Iterator-based loops, array iteration methods, recursion and asynchronous iteration remain separate future topics.

[Previous: Lesson 26](26-writing-clean-and-maintainable-loops.md) | [Back to contents](#table-of-contents)
