# Lesson 25 - Loop Performance and Optimization

[Previous: Debugging and Testing Loops](24-debugging-and-testing-loops.md) | [Next: Writing Clean and Maintainable Loops (planned)](26-writing-clean-and-maintainable-loops.md)

A faster loop is useful only if it still solves the original problem. Begin with a correct implementation, establish its cost, identify unnecessary work, and verify any replacement before measuring its performance.

## Table of contents

- [Computational cost](#computational-cost)
- [Time complexity](#time-complexity)
- [Space complexity](#space-complexity)
- [Reducing iterations](#reducing-iterations)
- [Reducing work inside loops](#reducing-work-inside-loops)
- [Optimizing traversals](#optimizing-traversals)
- [JavaScript engine considerations](#javascript-engine-considerations)
- [Measuring performance](#measuring-performance)
- [Integrated practice](#integrated-practice)
- [Summary](#summary)

## Learning objectives

Derive operation counts, distinguish growth rates from measured time, analyze auxiliary storage, recognize redundant work, select suitable traversal strategies, and design repeatable performance experiments. Use the array, Map, Set, binary search, sorting and correctness techniques established in earlier lessons.

## Computational cost

Suppose we need the sum of [4, 7, 2, 9]. Each element must contribute once. We can count additions independently of the speed of the computer.

```javascript
const values = [4, 7, 2, 9];
let sum = 0;
let additions = 0;
for (let i = 0; i < values.length; i++) {
  sum += values[i];
  additions++;
}
console.log(sum, additions);
```

Expected output:

```text
22 4
```

The counter measures one selected operation, not every machine instruction. The loop also initializes i once, checks its condition five times and increments i four times. In general, for n elements, there are n additions, n increments and n+1 condition checks. The final check fails and ends execution. Counting additions is useful because their count grows with the input size.

Consider finding the first occurrence of 7. We do not need to examine elements after finding it.

```javascript
const values = [4, 7, 2, 9];
let position = -1;
let comparisons = 0;
for (let i = 0; i < values.length; i++) {
  comparisons++;
  if (values[i] === 7) {
    position = i;
    break;
  }
}
console.log(position, comparisons);
```

Expected output:

```text
1 2
```

The first comparison fails and the second succeeds. If the target were 4, one comparison would suffice; if absent, all four would be necessary. These are best and worst cases. Average cost requires an assumption about how inputs are distributed. For a successful search with equally likely target positions in an array of distinct values, the average comparison count is (n+1)/2. Without that assumption we cannot claim this average.

### Practice: count operations

For a loop traversing 12 elements and adding each to a total, count additions, condition checks and increments. What changes when the array is empty?

### Worked answer

There are 12 additions, 13 checks and 12 increments. With no elements there is still one condition check, but no body execution or increment. The distinction matters when analyzing fixed overhead separately from input-dependent work.

## Time complexity

Time complexity describes how computational work grows with input size. It is not a prediction of elapsed seconds. We commonly use Big O notation to express an asymptotic upper bound: O(n) means that beyond some input size, work is bounded by a constant times n. When the growth is tightly proportional to n, Theta(n) is a more precise description. In this chapter, common Big O labels describe tight worst-case growth unless stated otherwise.

### Constant and linear work

Reading values[0] performs a fixed number of operations regardless of array length, under the usual array access cost model. This is O(1). It does not mean exactly one instruction. Summing every element is O(n). Two separate complete traversals perform roughly 2n body executions, still O(n). Constants matter in real measurements even though asymptotic notation suppresses them.

### Quadratic and rectangular work

To compare every distinct pair of elements, use an outer index i and an inner index j starting at i+1. This avoids comparing an element with itself or repeating a reversed pair.

```javascript
const n = 5;
let pairs = 0;
for (let i = 0; i < n; i++) {
  for (let j = i + 1; j < n; j++) {
    pairs++;
  }
}
console.log(pairs);
```

Expected output:

```text
10
```

| i | Inner executions |
|---|---:|
| 0 | 4 |
| 1 | 3 |
| 2 | 2 |
| 3 | 1 |
| 4 | 0 |

The sum is n(n-1)/2. Expanding gives (n*n-n)/2. The squared term dominates as n grows, so the cost is O(n^2). Halving the number of pairs does not change the growth class. If two independent arrays have lengths n and m, comparing every cross-array pair costs O(n*m), not necessarily O(n^2).

Nested loops are not automatically quadratic. An inner loop with exactly three executions per outer iteration costs 3n, or O(n). Similarly, a pointer that advances across an array without resetting can make nested loops linear in total.

### Logarithmic work

A loop that repeatedly halves a positive integer reaches zero much sooner than one that subtracts one each time.

```javascript
let remaining = 32;
let steps = 0;
while (remaining > 1) {
  remaining = Math.trunc(remaining / 2);
  steps++;
}
console.log(steps);
```

Expected output:

```text
5
```

The states are 32, 16, 8, 4, 2, 1. Five halvings suffice because 2 raised to the fifth power is 32. The base-two logarithm of 32 is 5. Doubling the initial size adds only one halving. This is O(log n) for positive integer n. Binary search uses the same principle by discarding roughly half the remaining candidates after each comparison.

### Combining growth rates

A linear traversal followed by a quadratic traversal costs O(n+n^2), simplified to O(n^2). A linear traversal that performs binary search on an independent sorted array of length m for each element costs O(n log m) for m >= 2 (or O(n log(m+1)) when m may be 1). Sorting costs must also be included if the array is not already sorted.

| Growth | n=10 | n=100 | n=1000 |
|---|---:|---:|---:|
| Constant | 1 | 1 | 1 |
| Logarithmic (rounded up) | 4 | 7 | 10 |
| Linear | 10 | 100 | 1000 |
| Quadratic | 100 | 10000 | 1000000 |

These are illustrative operation counts, not timings. A costly constant operation may be slower than a small linear traversal.

### Practice: analyze a dependent loop

An outer loop runs n times. Its inner loop runs from zero through i inclusive. Derive the total number of inner executions and its complexity.

### Worked answer

The inner counts are 1, 2, ..., n. Pairing the first and last terms gives n(n+1)/2. For n=4 the count is 1+2+3+4=10. The dominant term is quadratic. The outer loop contributes only linear overhead, so total work is O(n^2).

## Space complexity

Space complexity describes storage growth. Auxiliary space excludes the input and, by convention here, the required output. State that convention explicitly because some analyses include output storage.

To reverse an array, we can create a new array or exchange elements in place. The first preserves the original and requires n output slots. The second changes the input but needs only a few variables.

```javascript
const values = [3, 8, 1, 6];
let left = 0;
let right = values.length - 1;
while (left < right) {
  const temporary = values[left];
  values[left] = values[right];
  values[right] = temporary;
  left++;
  right--;
}
console.log(values.join(' '));
```

Expected output:

```text
6 1 8 3
```

The temporary variable preserves the overwritten value. Both pointers move toward the center. There are floor(n/2) swaps, hence O(n) time and O(1) auxiliary space. An in-place algorithm is inappropriate when callers require the original array unchanged.

### Trading memory for repeated work

For many range-total queries, a prefix array stores intermediate totals. Building it costs O(n) time and O(n) auxiliary space; each later query costs O(1). Direct summation needs O(1) auxiliary space but may cost O(n) per query. For q queries the worst-case totals are O(n+q) versus O(n*q). If values change, stored totals must be updated or rebuilt. The optimization is most useful when queries are frequent and data is relatively stable.

### Practice: count distinct values

Compare a nested search that checks every earlier element before counting a value with a Set-based approach. Assume primitive values and consistent equality semantics.

### Worked answer

The nested approach uses O(1) auxiliary space and O(n^2) worst-case comparisons. A Set can retain up to n distinct values, requiring O(n) auxiliary space. Typical hash-based implementations offer expected constant-time membership operations, giving expected O(n) total work. JavaScript requires average sublinear access for Set, not a universal constant-time guarantee. For small inputs or strict memory limits the nested approach may still be appropriate.

## Reducing iterations

### Stop when the answer is known

To determine whether any number is negative, stop at the first negative. Continuing cannot change the answer from true back to false.

```javascript
const values = [8, 3, -2, 9, -5];
let found = false;
let checks = 0;
for (let i = 0; i < values.length; i++) {
  checks++;
  if (values[i] < 0) {
    found = true;
    break;
  }
}
console.log(found, checks);
```

Expected output:

```text
true 3
```

This reduces work for early matches, but the worst case remains linear. Do not use this optimization when the requirement is to count every negative or perform a side effect for every element.

### Eliminate candidates mathematically

To test whether 97 is prime, a divisor d larger than its square root must have a partner smaller than the square root. Therefore checking smaller factors suffices. After handling even numbers, only odd candidates are necessary.

```javascript
const n = 97;
let prime = n >= 2;
let checks = 0;
if (n > 2 && n % 2 === 0) prime = false;
for (let d = 3; prime && d <= n / d; d += 2) {
  checks++;
  if (n % d === 0) prime = false;
}
console.log(prime, checks);
```

Expected output:

```text
true 4
```

The tested divisors are 3, 5, 7, 9. The condition d <= n/d expresses d*d <= n for positive d. It avoids computing a potentially large product. The example assumes a safe positive integer. For n=2 the loop does not execute and prime remains true; for n=1 prime is false. The worst-case candidate count is O(sqrt(n)) in terms of the numeric value n, not the number of digits in its representation.

### Replace repeated searching with indexing

Suppose two arrays contain IDs and we need to count how many entries in the first occur anywhere in the second. Repeated linear search scans the second array for every first-array element. A Set built from the second array allows direct membership queries.

```javascript
const requests = [4, 9, 4, 2];
const allowed = [2, 4, 7];
const membership = new Set();
for (let i = 0; i < allowed.length; i++) {
  membership.add(allowed[i]);
}
let count = 0;
for (let i = 0; i < requests.length; i++) {
  if (membership.has(requests[i])) count++;
}
console.log(count);
```

Expected output:

```text
3
```

The repeated 4 counts twice because the requirement counts requests, not distinct IDs. The original nested approach costs O(n*m) in the worst case. The Set approach typically costs expected O(n+m) and uses O(m) auxiliary storage. Building the Set may not pay off for a single small search. Set uses SameValueZero equality, which treats NaN as equal to NaN; a strict-equality search does not. Restrict IDs to ordinary integers here to preserve equivalence.

### Practice: first qualifying pair

Given sorted distinct integers [1, 3, 5, 8, 12], find whether two different positions sum to 13. Avoid enumerating every pair.

### Worked answer

Start at the smallest and largest values. Their sum is 13, so the answer is immediately true. More generally, if the sum is too small, the smallest value cannot pair with any remaining value to reach the target; advance left. If too large, decrease right. Each unsuccessful comparison removes one candidate. Stop when left meets right, since distinct positions are required.

```javascript
const a = [1, 3, 5, 8, 12];
let left = 0;
let right = a.length - 1;
let found = false;
while (left < right) {
  const sum = a[left] + a[right];
  if (sum === 13) {
    found = true;
    break;
  }
  if (sum < 13) left++;
  else right--;
}
console.log(found);
```

Expected output:

```text
true
```

The worst-case time is O(n), auxiliary space O(1). Sortedness is essential to the elimination argument.

## Reducing work inside loops

### Move invariant calculations outside

A loop-invariant expression has the same value on every relevant iteration. Suppose a fixed tax rate is applied to each price. The multiplier 1+rate need not be recalculated each time.

```javascript
const prices = [100, 200, 300];
const rate = 0.25;
const multiplier = 1 + rate;
let total = 0;
for (let i = 0; i < prices.length; i++) {
  total += prices[i] * multiplier;
}
console.log(total);
```

Expected output:

```text
750
```

The multiplier is 1.25, so the contributions are 125, 250 and 375. Moving the addition outside reduces source-level additions from n to one. An engine might already perform such optimization, so a speed improvement is not guaranteed. Moving an expression is unsafe if its value depends on changing state, if evaluation has side effects, or if moving it changes whether an error occurs on an empty input.

### Reuse overlapping calculations

For every consecutive group of three values, calculate its sum. Directly adding three values per group is simple; for a general group size k, direct recomputation costs O((n-k+1)*k). Consecutive windows overlap in k-1 elements. Remove the outgoing value and add the incoming value instead.

```javascript
const a = [4, 2, 7, 1, 5];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) sum += a[i];
const totals = [sum];
for (let right = k; right < a.length; right++) {
  sum -= a[right - k];
  sum += a[right];
  totals.push(sum);
}
console.log(totals.join(' '));
```

Expected output:

```text
13 10 13
```

At right=3, right-k=0 identifies the outgoing 4. The incoming value is a[3]=1. The total becomes 13-4+1=10. At right=4, remove 2 and add 5. Initialization costs k additions; each of the n-k later windows costs two arithmetic updates. Total work is O(n) for valid 1<=k<=n. Output storage is O(n-k+1), auxiliary storage O(1). With floating-point values the changed arithmetic order can change rounding, so use an appropriate numerical contract.

### Avoid unnecessary allocation

If only a total is required, accumulating qualifying values directly avoids constructing a filtered array and then summing it. But if the filtered array is also required, constructing it is not wasted work. Reusing mutable objects can introduce aliasing: pushing the same object into an array repeatedly stores references to the same object, not independent snapshots.

### Practice: invariant or not?

Can `values.length` be cached before a loop that appends elements to values? Can `Math.abs(target)` be cached if target is a fixed number?

### Worked answer

Caching length changes a dynamic traversal into a traversal of the original length. It is correct only when that is the intended contract. Caching the absolute value of an unchanged numeric target is safe when Math.abs has its normal built-in behavior. Neither change guarantees a measurable speed improvement.

## Optimizing traversals

### Combine compatible passes

Calculate the sum and maximum of a nonempty numeric array. Separate passes are correct, but both read every element. A single traversal can maintain both states.

```javascript
const a = [-8, -2, -5];
let total = 0;
let maximum = a[0];
for (let i = 0; i < a.length; i++) {
  const value = a[i];
  total += value;
  if (value > maximum) maximum = value;
}
console.log(total, maximum);
```

Expected output:

```text
-15 -2
```

Initialize maximum from the first element, not zero, because all elements may be negative. The fused loop has n body executions rather than 2n. Both approaches remain O(n). Fusion may harm readability or change observable ordering when the passes perform side effects. Keep separate passes if one depends on the completed results of another.

### Avoid resetting a moving boundary

For nonnegative numbers, find the longest contiguous range whose sum is at most 5. A nested approach starts from every index. A variable window instead extends right and advances left only when the total exceeds the limit.

```javascript
const a = [2, 1, 3, 1, 1];
let left = 0;
let sum = 0;
let best = 0;
for (let right = 0; right < a.length; right++) {
  sum += a[right];
  while (sum > 5) {
    sum -= a[left];
    left++;
  }
  const length = right - left + 1;
  if (length > best) best = length;
}
console.log(best);
```

Expected output:

```text
3
```

Although there is a while loop inside a for loop, left never resets. Each index enters once and leaves at most once, so there are at most n additions and n removals. This is an aggregate analysis of total work across iterations, yielding O(n). Negative values invalidate the permanent-removal argument. A nonnegative limit and nonnegative elements are required for this implementation.

### Traversal order and locality

A computer often fetches nearby memory locations together into a small fast cache. Accessing nearby data consecutively can reduce memory delays. In a rectangular array of arrays, row-first traversal also avoids repeatedly selecting different row objects. JavaScript does not guarantee contiguous physical storage for ordinary arrays, so do not infer a universal timing advantage. Typed arrays represent fixed-width numeric elements in an underlying buffer, but introduce different storage and conversion rules; they are not needed for this lesson.

### Practice: remove repeated scans

Given [2, 4, 6, 8], report the number and sum of even values in one pass. Explain the cost and what happens for an empty array.

### Worked answer

Initialize both states to zero. For each qualifying value, increment count and add the value. Both updates depend on the same condition. An empty input leaves both at zero.

```javascript
const a = [2, 4, 6, 8];
let count = 0;
let sum = 0;
for (let i = 0; i < a.length; i++) {
  if (a[i] % 2 === 0) {
    count++;
    sum += a[i];
  }
}
console.log(count, sum);
```

Expected output:

```text
4 20
```

Time is O(n), auxiliary space O(1). Two passes would have the same growth rate but more traversals.

## JavaScript engine considerations

JavaScript engines may interpret code and compile frequently executed code into optimized machine instructions while the program runs. This is called just-in-time compilation (JIT). The engine can use observations about values and object structures to specialize code, and may abandon an optimization when its assumptions stop holding. This is often called deoptimization.

For example, a numeric array may be represented differently from an array containing numbers, strings and holes. The language permits all of these. An engine may optimize a consistently numeric traversal more effectively, but representation choices differ by engine and version. Do not change the meaning of data merely to satisfy a presumed optimization.

Allocating many temporary arrays or objects can increase memory pressure. Garbage collection is the automatic reclamation of objects that are no longer reachable. Collection work can interrupt or overlap execution and affect timings. Avoid unnecessary allocations when the algorithm does not require them, but do not reuse objects in ways that corrupt results.

Avoid universal claims such as 'while is faster than for', 'caching length always improves speed', or '++i is faster than i++'. When the increment result is unused, both increment forms update the counter equivalently; an engine can optimize them similarly. Select the loop structure that communicates the intended behavior. Measure realistic workloads if a performance difference matters.

### Practice: evaluate an optimization claim

A developer replaces all for loops with while loops because one benchmark ran faster. Is that enough evidence?

### Worked answer

No. The result applies only to that benchmark and environment. Warm-up, input shape, garbage collection, measurement noise and differences in the actual loop body may explain the result. First verify equivalence, then compare representative workloads repeatedly. Prefer algorithmic improvements over speculative syntax changes.

## Measuring performance

### Establish a fair experiment

A benchmark measures a particular workload under particular conditions. Use identical inputs and requirements, exclude unrelated setup and console output from timed regions, repeat measurements, and verify results. A profiler is a tool that attributes execution time to functions or code regions; it helps identify where optimization matters before rewriting code.

In modern browsers and Node.js, `performance.now()` returns a timestamp in milliseconds relative to a time origin. Subtracting two readings gives elapsed time. Unlike a wall-clock date, this clock is intended for monotonic elapsed-time measurement. Its precision may vary by environment.

```js
const start = performance.now();
// Perform the operation being measured here.
const elapsed = performance.now() - start;
console.log(elapsed);
```

This fragment demonstrates the API, not a useful benchmark. A very short operation may be smaller than timer resolution or measurement overhead. Repeat it enough times to obtain measurable durations.

### Compare direct and rolling window totals

The following runnable benchmark builds the same numeric input once and compares two functions that return the sum of all window totals. This checksum is a compact observable result, not a complete correctness proof. Earlier tests should compare each individual window as well.

```js
function direct(a, k) {
  let checksum = 0;
  for (let start = 0; start <= a.length - k; start++) {
    let sum = 0;
    for (let j = 0; j < k; j++) sum += a[start + j];
    checksum += sum;
  }
  return checksum;
}
function rolling(a, k) {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += a[i];
  let checksum = sum;
  for (let right = k; right < a.length; right++) {
    sum -= a[right - k];
    sum += a[right];
    checksum += sum;
  }
  return checksum;
}
const a = [];
for (let i = 0; i < 20000; i++) a.push(i % 17);
const k = 100;
const expected = direct(a, k);
if (rolling(a, k) !== expected) throw new Error('Mismatch');
// Warm up both functions before recording samples.
for (let i = 0; i < 5; i++) {
  direct(a, k);
  rolling(a, k);
}
function measure(fn) {
  const start = performance.now();
  let result = 0;
  for (let i = 0; i < 10; i++) result = fn(a, k);
  const elapsed = performance.now() - start;
  if (result !== expected) throw new Error('Mismatch');
  return elapsed / 10;
}
for (let trial = 0; trial < 6; trial++) {
  let d, r;
  if (trial % 2 === 0) {
    d = measure(direct);
    r = measure(rolling);
  } else {
    r = measure(rolling);
    d = measure(direct);
  }
  console.log(d, r);
}
```

The output consists of six pairs of elapsed milliseconds per call. Exact values vary. The function parameter fn holds a function that can be called using fn(a,k). Dividing elapsed time by 10 gives mean time per call within that sample. Alternating order reduces, but does not eliminate, systematic bias. The warm-up count is illustrative, not a guarantee of stable compilation. Repeated identical calls may also differ from production workloads; use varied representative inputs when appropriate.

Compare the distribution of samples rather than the single fastest reading. The median is the middle sorted observation (or the mean of the two middle observations for an even count). It is less sensitive to isolated large delays than the arithmetic mean. Repeat with several n and k values. If k stays constant, both algorithms scale linearly in n; increasing k reveals the direct method's additional work. Avoid claiming a universal speedup from one machine.

### Practice: design a benchmark

You want to compare linear search with binary search for 1000 queries against 10000 IDs. What should be included in the measurement?

### Worked answer

Use the same queries, including early matches, late matches and missing IDs. If binary search requires sorting, measure preprocessing separately and also report end-to-end cost when relevant. Verify identical membership results; duplicate positions may differ unless the contract specifies first occurrence. Warm up both implementations, repeat and alternate order, avoid logging inside timed regions, and report environment and input sizes. For repeated queries on already sorted data, measure query-only cost as well. Theoretical costs are O(q*n) and O(q*log n) respectively, excluding sorting.

## Integrated practice

Attempt each problem before reading its solution.

### Exercise 1: repeated range queries

Given [3, 1, 4, 1, 5, 9], calculate inclusive totals for [0,2], [2,4], [0,5] and [3,3]. Expected totals: 8, 10, 23, 1. Build an implementation that avoids scanning each range again. Analyze preprocessing, query cost and storage.

#### Worked solution

Store prefix[0]=0 and let prefix[i+1] contain the total through index i. Subtracting prefix[left] from prefix[right+1] removes all elements before left. The extra zero makes a range starting at index zero use the same expression as every other range.

```javascript
const a = [3, 1, 4, 1, 5, 9];
const queries = [[0, 2], [2, 4], [0, 5], [3, 3]];
const prefix = [0];
for (let i = 0; i < a.length; i++) {
  prefix.push(prefix[i] + a[i]);
}
const results = [];
for (let i = 0; i < queries.length; i++) {
  const left = queries[i][0];
  const right = queries[i][1];
  results.push(prefix[right + 1] - prefix[left]);
}
console.log(results.join(' '));
```

Expected output:

```text
8 10 23 1
```

Prefix values are 0,3,4,8,9,14,23. For [2,4], prefix[5]-prefix[2]=14-4=10. Preprocessing is O(n), queries O(q), auxiliary space O(n), output O(q). The code assumes valid integer bounds. In a public function validate 0<=left<=right<n. A single short query may not justify preprocessing.

### Exercise 2: duplicate detection

Determine whether [7, 2, 9, 2, 4] contains a duplicate, stopping as soon as one is found. Compare with pair enumeration. Test empty and distinct arrays too.

#### Worked solution

Store values already encountered in a Set. Test membership before adding the current value; reversing this order would make every element appear duplicated immediately.

```javascript
function hasDuplicate(a) {
  const seen = new Set();
  for (let i = 0; i < a.length; i++) {
    if (seen.has(a[i])) return true;
    seen.add(a[i]);
  }
  return false;
}
console.log(hasDuplicate([7, 2, 9, 2, 4]));
console.log(hasDuplicate([]));
console.log(hasDuplicate([1, 2, 3]));
```

Expected output:

```text
true
false
false
```

The Set grows through 7,2,9. At the next 2, membership succeeds and the function returns. Expected time under typical hash-based access is O(n), auxiliary space O(n). Pair enumeration costs O(n^2) worst-case time and O(1) auxiliary space. This implementation uses Set equality; the inputs are ordinary integers.

### Exercise 3: compare window implementations

For [3, -2, 5, 1, -4, 6] and k=3, find the greatest window sum. Verify a rolling implementation against a direct nested-loop reference. Include all-negative and full-length cases.

#### Worked solution

The window sums are 6,4,2,3. The maximum is 6. Initialize the best value from a real window, not zero. For each later window, subtract the element k positions behind the incoming index and add the incoming element. Unlike variable windows, this fixed-window identity works for negative values.

```javascript
function direct(a, k) {
  let best = -Infinity;
  for (let start = 0; start <= a.length - k; start++) {
    let sum = 0;
    for (let j = 0; j < k; j++) sum += a[start + j];
    if (sum > best) best = sum;
  }
  return best;
}
function rolling(a, k) {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += a[i];
  let best = sum;
  for (let right = k; right < a.length; right++) {
    sum -= a[right - k];
    sum += a[right];
    if (sum > best) best = sum;
  }
  return best;
}
const cases = [[[3,-2,5,1,-4,6],3], [[-8,-2,-3],2], [[2,3],2]];
for (let i = 0; i < cases.length; i++) {
  const a = cases[i][0];
  const k = cases[i][1];
  console.log(rolling(a,k), rolling(a,k) === direct(a,k));
}
```

Expected output:

```text
6 true
-5 true
5 true
```

Both functions assume finite numeric elements and valid k. The reference uses -Infinity, a value smaller than every finite number. The optimized version performs O(n) work rather than O((n-k+1)*k), with O(1) auxiliary space in both cases. The comparison tests provide evidence, not a proof for every input; the rolling invariant establishes correctness for exact arithmetic. Floating-point rounding may require tolerance-based comparisons.

### Exercise 4: analyze a proposed optimization

A program sorts an unsorted array with insertion sort, then performs one binary search. A colleague claims this is always faster than linear search because binary search is logarithmic. Evaluate the claim for one query and for many queries.

#### Worked solution

Insertion sort has O(n^2) worst-case time, O(n) best-case time for an already sorted array, and O(1) auxiliary space when modifying the input. Binary search adds O(log n) for one query. The total worst-case cost is therefore O(n^2), versus O(n) for direct linear search. For q queries, the sorting approach costs O(n^2+q log n), whereas repeated linear search costs O(q*n). With sufficiently many queries the preprocessing can pay off, depending on input order and constants. If only membership is required, a Set may be a better alternative, at the cost of additional storage. If original order must be preserved, sorting in place violates the contract; copying adds O(n) storage and time. Measure the actual workload rather than comparing query costs alone.

## Summary

Correctness precedes optimization. Count meaningful operations, identify input dimensions, distinguish worst-case from typical behavior, and account for preprocessing and storage. Reduce unnecessary candidates, reuse computations, and avoid repeated traversals where dependencies permit. Treat engine-specific advice as a hypothesis and benchmark representative workloads. Keep the simpler implementation when an optimization offers no meaningful benefit.

[Previous: Debugging and Testing Loops](24-debugging-and-testing-loops.md) | [Next: Writing Clean and Maintainable Loops (planned)](26-writing-clean-and-maintainable-loops.md)
