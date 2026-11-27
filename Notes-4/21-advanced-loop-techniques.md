# Lesson 21 - Advanced Loop Techniques

[Previous](20-generating-patterns.md) | [Next (planned)](22-converting-and-refactoring-loops.md)

## Contents

- [Two pointers](#two-pointers)
- [Prefix and suffix aggregates](#prefix-and-suffix-aggregates)
- [Sliding window](#sliding-window)
- [Consecutive values](#consecutive-values)
- [Circular iteration](#circular-iteration)
- [Chunks](#chunks)
- [Reducing the search space](#reducing-the-search-space)
- [Binary search](#binary-search)
- [Sorting](#sorting)
- [Integrated exercises](#integrated-exercises)

All examples assume dense arrays of finite numbers unless stated otherwise. Advanced traversal techniques reuse information or eliminate candidates rather than blindly repeat work.

## Two pointers

A pointer here is an ordinary numeric index, not a memory address. Opposite-end pointers can reverse an array, compare mirrored positions, or search sorted pairs. Same-direction pointers can separate reading from writing. Independent pointers can merge sorted collections.

```javascript
const a = [10,20,30,40,50];
let left = 0;
let right = a.length - 1;
while (left < right) {
  const temporary = a[left];
  a[left] = a[right];
  a[right] = temporary;
  left++;
  right--;
}
console.log(a);
```

Output: [50,40,30,20,10]. The last valid index is length minus one. Strict inequality stops before exchanging a middle element with itself. The temporary variable preserves the left value before assignment overwrites it. At each iteration the two outermost remaining values reach their final positions.

For a sorted array, a pair whose sum is too small cannot be improved by decreasing the right index. A sum too large cannot be improved by increasing the left index. These observations justify eliminating a candidate.

```javascript
const a = [1,2,4,7,11,15];
let l = 0, r = a.length - 1;
const target = 15;
let found = false;
while (l < r) {
  const sum = a[l] + a[r];
  if (sum === target) { console.log(a[l], a[r]); found = true; break; }
  if (sum < target) l++;
  else r--;
}
if (!found) console.log('No pair');
```

Output: 4 11. The sums are 16, 12, 13, 15. The expression a[l] + a[r] retrieves two numbers before adding them. Moving either pointer removes one candidate. Unsorted input invalidates this reasoning: [1,8,2,7,4] with target 15 contains 8 and 7, but the same search misses them.

Read/write compaction retains values without shifting the unprocessed suffix repeatedly.

```javascript
const a = [0,4,0,7,2,0,9];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (a[read] !== 0) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
console.log(a);
```

Output: [4,7,2,9]. When read is 3 and write is 1, a[1] = a[3] copies 7 into the retained prefix. Write never exceeds read, so unread elements remain safe. Truncation removes stale suffix values. For an empty array write remains zero.

Merging sorted arrays uses independent pointers. Select the smaller current value, advance only its pointer, and append leftovers when one input ends.

```javascript
const a = [1,4,7], b = [2,3,8], result = [];
let i = 0, j = 0;
while (i < a.length && j < b.length) {
  if (a[i] <= b[j]) { result.push(a[i]); i++; }
  else { result.push(b[j]); j++; }
}
while (i < a.length) { result.push(a[i]); i++; }
while (j < b.length) { result.push(b[j]); j++; }
console.log(result);
```

Output: [1,2,3,4,7,8]. The AND condition protects both accesses. The final loops are necessary: after 7 is appended, 8 remains. Equality selects the first array, preserving its priority on ties. Three sorted inputs can be merged by tracking three indexes, or by merging two and then merging the intermediate result with the third.

### Practice and solutions

1. Verify whether radar, hello, abba and the empty string are palindromes. Expected true, false, true, true.

```javascript
const texts = ['radar','hello','abba',''];
for (let t = 0; t < texts.length; t++) {
  const text = texts[t];
  let l = 0, r = text.length - 1, ok = true;
  while (l < r) {
    if (text[l] !== text[r]) { ok = false; break; }
    l++; r--;
  }
  console.log(ok);
}
```

The flag records whether a mismatch has been discovered. Empty input has no mismatch. This compares UTF-16 code units, not grapheme clusters.

2. Retain nonnegative values in [-3,5,-1,0,8,-4]. Expected [5,0,8].

```javascript
const a = [-3,5,-1,0,8,-4];
let w = 0;
for (let r = 0; r < a.length; r++) {
  if (a[r] >= 0) { a[w] = a[r]; w++; }
}
a.length = w;
console.log(a);
```

Use >= rather than > to retain zero. The write index is also the retained count.

## Prefix and suffix aggregates

Suppose sales are [4,7,2,9,3]. To sum indexes 1 through 3, add 7+2+9=18. Repeating a loop for every query repeats work. A prefix sum stores the total of all values before a boundary. Use an extra initial zero so every range has two boundaries.

```text
Index:       0  1  2  3  4
Values:      4  7  2  9  3
Boundary:  0  1  2  3  4  5
Prefix:    0  4 11 13 22 25
```

```javascript
const a = [4,7,2,9,3];
const prefix = [0];
for (let i = 0; i < a.length; i++) {
  prefix.push(prefix[i] + a[i]);
}
const start = 1, end = 3;
console.log(prefix);
console.log(prefix[end + 1] - prefix[start]);
```

Output: [0,4,11,13,22,25], then 18. At i=2, prefix[2] is 11 and a[2] is 2; their sum 13 is appended at index 3. prefix[end+1] includes the last requested element; prefix[start] contains exactly the unwanted earlier values. Subtraction removes them: 22-4=18. For half-open ranges [start,end), use prefix[end]-prefix[start]. Empty ranges yield zero. Validate 0 <= start <= end <= length for half-open queries. Negative numbers work too. If the original array changes, the prefix may need rebuilding.

A suffix sum stores the total from an index to the end. Build backward because the next suffix must already exist.

```javascript
const a = [4,7,2,9,3];
const suffix = [];
suffix[a.length] = 0;
for (let i = a.length - 1; i >= 0; i--) {
  suffix[i] = a[i] + suffix[i+1];
}
console.log(suffix);
```

Output: [25,21,14,12,3,0]. At i=3, 9+suffix[4] gives 12. The extra zero represents the empty suffix. Prefix and suffix aggregates can also track minima or maxima, but subtracting two minima cannot recover a range minimum. Subtraction works for sums because addition can be undone.

### Practice and solution

Find the sum excluding each element of [3,5,2]. Expected [7,5,8]. Build a prefix and suffix and combine disjoint ranges.

```javascript
const a = [3,5,2], p = [0], s = [], result = [];
for (let i = 0; i < a.length; i++) p.push(p[i]+a[i]);
s[a.length] = 0;
for (let i = a.length-1; i >= 0; i--) s[i] = a[i]+s[i+1];
for (let i = 0; i < a.length; i++) result.push(p[i]+s[i+1]);
console.log(result);
```

At i=1, p[1]=3 and s[2]=2, so 5 is appended. Neither aggregate includes a[1]. For sums alone, total minus current value is simpler; the two-aggregate technique also supports other combinations such as products without division.

## Sliding window

A window is a consecutive range of positions. For [2,1,5,1,3,2], find the largest sum of three consecutive values. The windows have sums 8,7,9,6. Recomputing each sum repeats two additions. Instead, remove the departing value and add the arriving value.

```javascript
const a = [2,1,5,1,3,2], k = 3;
if (Number.isInteger(k) && k > 0 && k <= a.length) {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += a[i];
  let best = sum, bestStart = 0;
  for (let right = k; right < a.length; right++) {
    sum -= a[right-k];
    sum += a[right];
    if (sum > best) { best = sum; bestStart = right-k+1; }
  }
  console.log(best, bestStart);
} else console.log('Invalid window');
```

Output: 9 2. At right=3, right-k=0 identifies the departing 2. The old sum 8 becomes 6, then adding a[3]=1 gives 7. At right=4 the sum becomes 9. The new start is right-k+1 because the window includes its right endpoint. Initialize best from the first actual window, not zero: all-negative windows may have negative sums. Strict > preserves the earliest tied window. Number.isInteger checks whether k is an integer.

Variable windows change length. Find the shortest consecutive range with sum at least 7 in [2,3,1,2,4,3]. Assume nonnegative numbers and positive target. Expand right until the target is met, then shrink left while it remains met. Record every valid candidate before shrinking.

```javascript
const a = [2,3,1,2,4,3], target = 7;
let left = 0, sum = 0, best = a.length+1;
for (let right = 0; right < a.length; right++) {
  sum += a[right];
  while (sum >= target) {
    const length = right-left+1;
    if (length < best) best = length;
    sum -= a[left];
    left++;
  }
}
console.log(best === a.length+1 ? 'No window' : best);
```

Output: 2. The conditional operator condition ? valueIfTrue : valueIfFalse selects one result; here it prints a message if the sentinel was never replaced. The inclusive length is right-left+1. When right=3, sum=8; length 4 is recorded, then removing 2 gives 6. When right=5, the final pair [4,3] gives 7 and length 2. Nonnegative values justify discarding starts: shrinking cannot increase the sum. Negative values break that assumption: [1,-1,5] with target 5 can hide the length-one answer from this algorithm.

### Practice and solution

Find the greatest two-element sum in [-8,-3,-5,-2]. Expected -7, start 2.

```javascript
const a = [-8,-3,-5,-2], k = 2;
let sum = a[0]+a[1], best = sum, start = 0;
for (let r = k; r < a.length; r++) {
  sum = sum-a[r-k]+a[r];
  if (sum > best) { best = sum; start = r-k+1; }
}
console.log(best,start);
```

The sums are -11,-8,-7. Starting best at zero would incorrectly report zero, which is not a window sum.

## Consecutive values

Adjacent comparisons use a[i-1] and a[i], so start at index 1. Find the longest strictly increasing consecutive run in [2,4,6,1,3,5,7,2]. Expected length 4, start 3. A run is contiguous; it is not an arbitrary subsequence.

```javascript
const a = [2,4,6,1,3,5,7,2];
let current = a.length > 0 ? 1 : 0;
let best = current, start = 0;
for (let i = 1; i < a.length; i++) {
  if (a[i] > a[i-1]) current++;
  else current = 1;
  if (current > best) { best = current; start = i-current+1; }
}
console.log(best,start);
```

At i=3, 1 is not greater than 6, so the current run resets to one, not zero: the current element begins a new run. At i=6, current=4, so start=6-4+1=3. Empty input has length zero; start is not meaningful then. Replace > with >= for nondecreasing runs.

### Practice and solution

Compress consecutive equal values in AAABBCCCCA into A3B2C4A1. Nonconsecutive A runs must remain separate.

```javascript
const text = 'AAABBCCCCA';
let result = '', count = 0;
for (let i = 0; i < text.length; i++) {
  count++;
  if (i === text.length-1 || text[i] !== text[i+1]) {
    result += text[i] + count;
    count = 0;
  }
}
console.log(result);
```

The current character is counted before testing the boundary. The OR condition flushes the final run as well as runs followed by a different character. String plus number concatenates, so 'A'+3 yields 'A3'. Empty input produces an empty result.

## Circular iteration

A circular traversal wraps from the last position to the first. For [A,B,C,D,E], start at index 3 and visit five values: D,E,A,B,C. Remainder maps increasing positions back into valid indexes.

```javascript
const a = ['A','B','C','D','E'], start = 3;
for (let step = 0; step < a.length; step++) {
  const index = (start+step)%a.length;
  console.log(a[index]);
}
```

At step=2, (3+2)%5=0. Parentheses ensure addition precedes remainder. The step counter controls termination independently of the wrapped index. For negative offsets, JavaScript remainder can be negative: -1%5 is -1. Normalize using ((offset%n)+n)%n for positive n. Never calculate a wrapped index with n=0.

### Practice and solution

Rotate [1,2,3,4,5] right by two positions into a new array. Expected [4,5,1,2,3].

```javascript
const a = [1,2,3,4,5], shift = 2, result = [];
for (let i = 0; i < a.length; i++) {
  const source = ((i-shift)%a.length+a.length)%a.length;
  result.push(a[source]);
}
console.log(result);
```

At output index zero, source is ((-2)%5+5)%5=3. Reading source index 3 gives 4. The new array prevents overwriting unread source elements. The loop does not execute for empty input.

## Chunks

Divide [1,2,3,4,5,6,7] into consecutive groups of size three. Expected [[1,2,3],[4,5,6],[7]]. Unlike a sliding window, chunks do not overlap.

```javascript
const a = [1,2,3,4,5,6,7], size = 3, chunks = [];
if (Number.isInteger(size) && size > 0) {
  for (let start = 0; start < a.length; start += size) {
    const chunk = [];
    for (let i = start; i < start+size && i < a.length; i++) {
      chunk.push(a[i]);
    }
    chunks.push(chunk);
  }
}
console.log(chunks);
```

The outer update skips exactly one chunk. The inner AND condition prevents crossing either the chunk boundary or the array boundary. At start=6, i=6 is valid but i=7 is not. A zero size would prevent progress, so validation is essential. Initialize chunk inside the outer loop to create independent arrays.

### Practice and solution

Sum each group of two values in [4,1,7,2,9]. Expected [5,9,9].

```javascript
const a = [4,1,7,2,9], result = [];
for (let start = 0; start < a.length; start += 2) {
  let sum = 0;
  for (let i = start; i < start+2 && i < a.length; i++) sum += a[i];
  result.push(sum);
}
console.log(result);
```

Reset sum for each chunk. The final chunk contains only 9; no missing element is added.

## Reducing the search space

A search space is the set of candidates still worth considering. Eliminating candidates must follow from a property of the problem. For example, to test primality, factors occur in pairs: if n=a*b and both factors exceeded the square root of n, their product would exceed n. Therefore testing divisors through the square root is sufficient.

```javascript
const n = 49;
let prime = Number.isInteger(n) && n >= 2;
for (let d = 2; prime && d*d <= n; d++) {
  if (n%d === 0) prime = false;
}
console.log(prime);
```

Output: false. d*d <= n includes the exact square-root boundary. Using strict < would miss divisor 7 for 49. Assume small safe integers so multiplication is exact. A sorted pair search eliminates one candidate at a time; binary search eliminates roughly half of a sorted range at a time.

### Practice and solution

Test 47 and 49 using the same divisor bound. Expected true and false. For 47, divisors 2 through 6 do not divide it. For 49, divisor 7 divides exactly. The inclusive bound is essential.

## Binary search

Find 23 in [3,8,12,17,23,31,45]. The array is sorted. Comparing the middle value 17 with 23 eliminates indexes 0 through 3 because none can equal 23. Maintain an inclusive candidate range [low,high].

```javascript
const a = [3,8,12,17,23,31,45], target = 23;
let low = 0, high = a.length-1, found = -1;
while (low <= high) {
  const mid = low + Math.trunc((high-low)/2);
  if (a[mid] === target) { found = mid; break; }
  if (a[mid] < target) low = mid+1;
  else high = mid-1;
}
console.log(found);
```

Output: 4. At low=4, high=6, high-low=2, division gives 1, truncation gives 1, and adding low gives mid=5. The middle value 31 is too large, so high becomes 4. The next middle is 4. The condition low<=high includes a final singleton candidate. Updating low=mid rather than mid+1 can stall when low=mid. Empty input begins with high=-1 and returns -1. Unsorted input is invalid.

To find the first occurrence among duplicates, record a match but keep searching left. For a sorted array, finding the first value at least target also gives an insertion boundary (possibly length).

```javascript
const a = [1,3,3,3,8], target = 3;
let low = 0, high = a.length;
while (low < high) {
  const mid = low+Math.trunc((high-low)/2);
  if (a[mid] < target) low = mid+1;
  else high = mid;
}
console.log(low);
```

Output: 1. Here high is exclusive. The boundary may be at length. When a[mid] >= target, mid remains a possible answer, so high=mid, not mid-1. The interval shrinks even when only one candidate remains. Check low<a.length && a[low]===target to distinguish a matching value from an insertion position.

### Practice and solution

Find the first occurrence of 4 in [1,4,4,4,9], returning -1 if absent.

```javascript
const a = [1,4,4,4,9], target = 4;
let low = 0, high = a.length-1, answer = -1;
while (low <= high) {
  const mid = low+Math.trunc((high-low)/2);
  if (a[mid] === target) { answer = mid; high = mid-1; }
  else if (a[mid] < target) low = mid+1;
  else high = mid-1;
}
console.log(answer);
```

Output: 1. A match at index 2 is provisional. Searching indexes 0 and 1 discovers the earlier match. The saved answer survives termination.

## Sorting

Sorting rearranges values into a specified order while preserving their multiplicities. In-place sorting modifies the input. A stable sort preserves the original relative order of records with equal keys. The following algorithms use comparisons and assignments, not built-in sorting methods.

### Bubble sort

Adjacent out-of-order values are exchanged. Each left-to-right pass moves a largest remaining value to the end of the unsorted range.

```javascript
const a = [5,2,4,1];
for (let end = a.length-1; end > 0; end--) {
  let swapped = false;
  for (let i = 0; i < end; i++) {
    if (a[i] > a[i+1]) {
      const temp = a[i]; a[i] = a[i+1]; a[i+1] = temp;
      swapped = true;
    }
  }
  if (!swapped) break;
}
console.log(a);
```

Output: [1,2,4,5]. First pass: [2,5,4,1], [2,4,5,1], [2,4,1,5]. The condition i<end ensures i+1<=end. Decrementing end excludes the already settled suffix. Reset swapped each pass; a pass without swaps establishes sorted order. Strict > avoids swapping equal keys, preserving stability.

### Selection sort

Find the smallest value in the remaining suffix and exchange it with the first unsorted position.

```javascript
const a = [5,2,4,1];
for (let start = 0; start < a.length-1; start++) {
  let minIndex = start;
  for (let i = start+1; i < a.length; i++) {
    if (a[i] < a[minIndex]) minIndex = i;
  }
  const temp = a[start]; a[start] = a[minIndex]; a[minIndex] = temp;
}
console.log(a);
```

Output: [1,2,4,5]. First pass finds minIndex=3, then exchanges 5 and 1. Store the index, not merely the minimum value, because the exchange needs its location. The sorted prefix grows by one. Ordinary selection sort is not stable: exchanging 2A with a later 1 in [2A,2B,1] reverses the two equal-key records.

### Insertion sort

Maintain a sorted prefix. Take the next value and shift larger prefix values right until its insertion position is available.

```javascript
const a = [5,2,4,1];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i-1;
  while (j >= 0 && a[j] > key) {
    a[j+1] = a[j];
    j--;
  }
  a[j+1] = key;
}
console.log(a);
```

Output: [1,2,4,5]. At i=1, key=2. Copying a[0] into a[1] temporarily produces [5,5,4,1]. The saved key prevents loss of 2. j becomes -1, so j+1=0 is the insertion position. At i=2, key=4, shift 5 right and insert at index 1. Short-circuit AND prevents reading a[-1]. Strict > keeps equal keys in original order. Insertion sort performs little shifting on nearly sorted input; selection always scans its remaining suffix; bubble can stop after a swap-free pass. All three can require a number of comparisons proportional to the square of input length.

### Practice and solution

Sort [3,1,3,2] in descending order using insertion sort. Expected [3,3,2,1].

```javascript
const a = [3,1,3,2];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i-1;
  while (j >= 0 && a[j] < key) { a[j+1] = a[j]; j--; }
  a[j+1] = key;
}
console.log(a);
```

Reverse the comparison, not the traversal direction. Smaller values now shift right. At i=2, key=3 shifts 1 right but stops at the equal 3. The result preserves equal-key order.

## Integrated exercises

### 1. Sorted intersection

Find distinct common values in [1,2,2,4,7] and [2,2,3,7]. Expected [2,7].

```javascript
const a = [1,2,2,4,7], b = [2,2,3,7], result = [];
let i = 0, j = 0;
while (i < a.length && j < b.length) {
  if (a[i] < b[j]) i++;
  else if (a[i] > b[j]) j++;
  else {
    if (result.length === 0 || result[result.length-1] !== a[i]) result.push(a[i]);
    i++; j++;
  }
}
console.log(result);
```

When values differ, the smaller cannot match any later value in the other sorted array. On equality, advance both. The last-result comparison suppresses duplicates because matches arrive in sorted order.

### 2. Range report

For [5,2,8,4], answer inclusive ranges [0,1], [1,3], [2,2]. Expected 7,14,8.

```javascript
const a = [5,2,8,4], p = [0], queries = [[0,1],[1,3],[2,2]];
for (let i = 0; i < a.length; i++) p.push(p[i]+a[i]);
for (let i = 0; i < queries.length; i++) {
  const start = queries[i][0], end = queries[i][1];
  console.log(p[end+1]-p[start]);
}
```

The first bracket selects a query array, the second selects its boundary. For [1,3], p[4]-p[1]=19-5=14.

### 3. Sort then search

Sort [9,2,7,2,4] and find the first index of 2 in the sorted result. Expected [2,2,4,7,9], index 0.

```javascript
const a = [9,2,7,2,4];
for (let i = 1; i < a.length; i++) {
  const key = a[i]; let j = i-1;
  while (j >= 0 && a[j] > key) { a[j+1] = a[j]; j--; }
  a[j+1] = key;
}
let low = 0, high = a.length;
while (low < high) {
  const mid = low+Math.trunc((high-low)/2);
  if (a[mid] < 2) low = mid+1; else high = mid;
}
console.log(a,low < a.length && a[low] === 2 ? low : -1);
```

Insertion sort establishes the sorted-order prerequisite. The half-open boundary search finds the earliest possible match. The resulting index belongs to the sorted array, not the original order.

## Summary

Choose a technique based on the relationship between candidates: sorted order for elimination, overlapping ranges for sliding windows, repeated range queries for prefixes, adjacent relationships for runs, remainder for circular positions, and disjoint ranges for chunks. Sorting establishes order but has a cost and may modify input. Verify empty inputs, singleton inputs, duplicates, negative values, invalid sizes and termination conditions.

[Previous](20-generating-patterns.md) | [Next (planned)](22-converting-and-refactoring-loops.md)
