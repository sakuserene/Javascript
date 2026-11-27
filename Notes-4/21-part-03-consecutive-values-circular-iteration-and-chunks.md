# Lesson 21 - Part 3: Consecutive Values, Circular Iteration and Chunks

[Previous](21-part-02-prefix-suffix-aggregates-and-sliding-window.md) | [Next (planned)](21-part-04-reducing-search-space-and-binary-search.md)

## Table of contents

- [Processing consecutive values](#processing-consecutive-values)
- [Circular iteration](#circular-iteration)
- [Processing values in chunks](#processing-values-in-chunks)
- [Integrated practice](#integrated-practice)
- [Summary](#summary)

## Processing consecutive values

A single element does not always provide enough information. To detect a price increase, for example, we must compare a price with its predecessor. To identify a repeated run, we must also remember where the run began. Assume dense arrays of finite numbers unless stated otherwise.

### Adjacent differences

Given daily readings [12, 15, 11, 18], calculate the signed change between each pair of consecutive days. The first reading has no predecessor, so start at index 1. At index i, the preceding reading occupies index i - 1.

```javascript
const readings = [12, 15, 11, 18];
const changes = [];
for (let i = 1; i < readings.length; i++) {
  changes.push(readings[i] - readings[i - 1]);
}
console.log(changes.join(','));
```

Expected output: `3,-4,7`.

Here join(',') returns a string containing the array elements separated by commas; it is used only to display results consistently. At i = 2, readings[i - 1] retrieves readings[1], or 15. Subtracting it from readings[2], or 11, gives -4. The sign distinguishes a decrease from an increase.

Starting at zero would access readings[-1], which is undefined for this ordinary array. Subtracting undefined produces NaN. Empty and single-element arrays correctly produce no changes.

| i | Previous | Current | Change |
|---|---|---|---|
| 1 | 12 | 15 | 3 |
| 2 | 15 | 11 | -4 |
| 3 | 11 | 18 | 7 |

### Tracking increasing runs

Find the longest contiguous strictly increasing run in [4, 7, 9, 2, 3, 8, 10, 1]. A contiguous run uses neighboring positions without skipping elements. The desired run starts at index 3 and has length 4.

We need a current start and a best start and length. If a value is not greater than its predecessor, the old run ends and a new run starts at the current index. The current length is i - start + 1 because both endpoints are included.

```javascript
const values = [4, 7, 9, 2, 3, 8, 10, 1];
let start = 0;
let bestStart = 0;
let bestLength = values.length === 0 ? 0 : 1;
for (let i = 1; i < values.length; i++) {
  if (values[i] <= values[i - 1]) {
    start = i;
  }
  const length = i - start + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = start;
  }
}
console.log(bestStart, bestLength);
```

Expected output: `3 4`.

The conditional expression `condition ? a : b` selects a when the condition is true and b otherwise. Here it initializes the best length to zero for empty input and one for nonempty input. It could instead be written as an if/else assignment.

At i = 3, 2 <= 9 is true, so start becomes 3. The new length is 3 - 3 + 1 = 1. At i = 6, the run extends to length 6 - 3 + 1 = 4, exceeding the earlier best of three.

| i | start | length | bestLength |
|---|---|---|---|
| 1 | 0 | 2 | 2 |
| 2 | 0 | 3 | 3 |
| 3 | 3 | 1 | 3 |
| 4 | 3 | 2 | 3 |
| 5 | 3 | 3 | 3 |
| 6 | 3 | 4 | 4 |
| 7 | 7 | 1 | 4 |

Using >= instead of > in the best-length comparison selects the latest tied run rather than the earliest. Changing the reset condition to < permits equal adjacent values, producing nondecreasing runs instead of strictly increasing ones.

### Grouping equal consecutive values

Given [4, 4, 2, 2, 2, 4], report each consecutive run as a value and count. The two runs of 4 must remain separate. A frequency table would combine them, which is not the requirement.

Start a run at index zero. Continue while the next value equals the run value. When a different value arrives, save the completed run and begin a new one. The final run must also be saved even though no different value follows it.

```javascript
const values = [4, 4, 2, 2, 2, 4];
const runs = [];
if (values.length > 0) {
  let value = values[0];
  let count = 1;
  for (let i = 1; i < values.length; i++) {
    if (values[i] === value) {
      count++;
    } else {
      runs.push({ value: value, count: count });
      value = values[i];
      count = 1;
    }
  }
  runs.push({ value: value, count: count });
}
for (let i = 0; i < runs.length; i++) {
  console.log(runs[i].value, runs[i].count);
}
```

Expected output:

```text
4 2
2 3
4 1
```

The object literal stores a snapshot of the current numeric value and count. Resetting the variables does not change the numbers stored in previously created objects. At index 2, the first run is saved before value changes to 2 and count resets to one. Omitting the final push loses the last run. Initializing count to zero while starting at index one undercounts the first run.

### Practice: consecutive values

**Question 1.** Count decreases in [8, 6, 6, 3, 9, 2]. Equal values are not decreases. Expected: 3.

**Solution.** Compare each element with its predecessor, starting at one. Increment only when the current value is smaller.

```javascript
const values = [8, 6, 6, 3, 9, 2];
let count = 0;
for (let i = 1; i < values.length; i++) {
  if (values[i] < values[i - 1]) count++;
}
console.log(count);
```

Expected output: `3`. The decreases are 8 to 6, 6 to 3, and 9 to 2. Using <= would incorrectly count the equal pair.

**Question 2.** Find the longest run of equal values in [2, 2, 5, 5, 5, 2]. Expected length: 3.

**Solution.** Start with one element in the current run for nonempty input. Increment on equality and reset to one on a mismatch. Update the best after either operation.

```javascript
const values = [2, 2, 5, 5, 5, 2];
let count = values.length === 0 ? 0 : 1;
let best = count;
for (let i = 1; i < values.length; i++) {
  if (values[i] === values[i - 1]) count++;
  else count = 1;
  if (count > best) best = count;
}
console.log(best);
```

Expected output: `3`. The counts are 1, 2, 1, 2, 3, 1. Updating best during traversal also handles a longest run at the end. Empty input returns zero.

## Circular iteration

Ordinary traversal ends at the final index. Circular traversal treats the first position as the successor of the last position. Examples include rotating schedules, repeating playlists and round-robin assignments.

### Wrapping an index

For four positions, valid indexes are 0, 1, 2 and 3. After index 3 we want index 0. We could increment and explicitly reset the index when it equals the length. For nonnegative integer positions, remainder provides another approach: position % length.

```javascript
const values = ['A', 'B', 'C', 'D'];
let index = 2;
for (let step = 0; step < 7; step++) {
  console.log(values[index]);
  index = (index + 1) % values.length;
}
```

Expected output:

```text
C
D
A
B
C
D
A
```

At index 3, index + 1 evaluates to 4, and 4 % 4 evaluates to 0. Parentheses ensure the addition occurs before remainder. Without them, index + 1 % 4 would evaluate to index + 1 and fail to wrap. The step counter controls termination independently of the circular index, which repeatedly returns to earlier positions.

For empty input, remainder by zero produces NaN. Check that the collection is nonempty before attempting circular traversal. For a one-element collection, every wrapped index is zero.

### Backward movement and negative offsets

JavaScript's % operator is remainder, not always a nonnegative mathematical modulo. For example, -1 % 4 is -1. That is not a valid ordinary array index. To normalize an integer offset into the range 0 through n - 1, use ((offset % n) + n) % n for positive n.

```javascript
const values = ['A', 'B', 'C', 'D'];
let index = 1;
for (let step = 0; step < 6; step++) {
  console.log(values[index]);
  index = ((index - 1) % values.length + values.length) % values.length;
}
```

Expected output:

```text
B
A
D
C
B
A
```

At index zero, index - 1 is -1. The first remainder is -1. Adding four produces three, and the final remainder is three. The second remainder is necessary for nonnegative offsets too: if the first remainder is two, adding four produces six, which must be reduced to two.

### Rotating an array

Rotate [10, 20, 30, 40, 50] left by two positions, preserving the original. The new first element is the original element at index two. Each following result position advances one source position, wrapping at the end.

```javascript
const values = [10, 20, 30, 40, 50];
const offset = 2;
const rotated = [];
if (values.length > 0) {
  for (let i = 0; i < values.length; i++) {
    const source = ((i + offset) % values.length + values.length) % values.length;
    rotated.push(values[source]);
  }
}
console.log(rotated.join(','));
```

Expected output: `30,40,50,10,20`.

At result index three, i + offset is five. Five % five is zero, so the source is index zero. Negative offsets rotate in the opposite direction. Offsets greater than the length are equivalent to their normalized remainder. The loop runs exactly once per original element, preserving every occurrence. Assume integer offsets small enough for exact JavaScript integer arithmetic.

### Circular adjacent pairs

For [3, 8, 5], report each value and its successor, including the last-to-first pair. Unlike ordinary adjacent traversal, the loop includes the last index.

```javascript
const values = [3, 8, 5];
for (let i = 0; i < values.length; i++) {
  const next = (i + 1) % values.length;
  console.log(values[i], values[next]);
}
```

Expected output:

```text
3 8
8 5
5 3
```

At i = 2, next becomes zero. Empty input never enters the loop. A singleton produces a self-pair; if the problem requires distinct positions, add a length check requiring at least two elements.

### Practice: circular iteration

**Question 1.** Assign seven tasks to workers A, B and C in repeating order, beginning with B.

**Solution.** The worker index starts at one. Each task advances the index, wrapping after C. The task counter, not the worker index, determines when to stop.

```javascript
const workers = ['A', 'B', 'C'];
let index = 1;
for (let task = 1; task <= 7; task++) {
  console.log(task, workers[index]);
  index = (index + 1) % workers.length;
}
```

Expected output:

```text
1 B
2 C
3 A
4 B
5 C
6 A
7 B
```

**Question 2.** Rotate [1, 2, 3, 4] right by one. Expected: [4, 1, 2, 3].

**Solution.** Right rotation by one corresponds to a left offset of -1. At result index zero, the source index must normalize -1 to 3.

```javascript
const values = [1, 2, 3, 4];
const result = [];
for (let i = 0; i < values.length; i++) {
  const source = ((i - 1) % values.length + values.length) % values.length;
  result.push(values[source]);
}
console.log(result.join(','));
```

Expected output: `4,1,2,3`. The source indexes are 3, 0, 1, 2. The original array is unchanged.

## Processing values in chunks

A chunk is a contiguous group of elements. Unlike overlapping sliding windows, ordinary chunks do not overlap. For [1, 2, 3, 4, 5, 6, 7] and chunk size three, the groups are [1, 2, 3], [4, 5, 6], and [7].

### Deriving chunk boundaries

The first chunk starts at zero. Each subsequent chunk starts size positions later. Within a chunk, the exclusive end is start + size, but it must not exceed the array length. We can enforce both limits in the inner loop.

```javascript
const values = [1, 2, 3, 4, 5, 6, 7];
const size = 3;
const chunks = [];
for (let start = 0; start < values.length; start += size) {
  const chunk = [];
  for (let i = start; i < start + size && i < values.length; i++) {
    chunk.push(values[i]);
  }
  chunks.push(chunk);
}
for (let i = 0; i < chunks.length; i++) {
  console.log(chunks[i].join(','));
}
```

Expected output:

```text
1,2,3
4,5,6
7
```

At start = 6, start + size equals nine. The inner loop first visits index six, then stops at index seven because seven < values.length is false. Creating chunk inside the outer loop gives each group its own array. Creating it outside and repeatedly pushing the same array would share one mutable object among all groups.

| start | Exclusive requested end | Actual indexes |
|---|---|---|
| 0 | 3 | 0, 1, 2 |
| 3 | 6 | 3, 4, 5 |
| 6 | 9 | 6 |

### Validating the chunk size

A zero size makes start += size leave start unchanged, causing nontermination for nonempty input. A negative size moves away from the end. A fractional size eventually produces fractional indexes. Require a positive integer. Number.isInteger(value) returns true for integer-valued numbers and false for fractions, strings, NaN and infinities.

```javascript
const values = [1, 2, 3];
const size = 0;
if (!Number.isInteger(size) || size <= 0) {
  console.log('Invalid chunk size');
} else {
  for (let start = 0; start < values.length; start += size) {
    console.log(start);
  }
}
```

Expected output: `Invalid chunk size`. The logical OR rejects either a noninteger or a nonpositive size. Subsequent examples assume a valid size.

### Aggregating chunks without storing them

Suppose we need the total and average of each group of three sales, including the final partial group. There is no need to construct separate chunk arrays. Reset the sum and count for each group.

```javascript
const sales = [6, 9, 3, 12, 6, 3, 8];
const size = 3;
for (let start = 0; start < sales.length; start += size) {
  let sum = 0;
  let count = 0;
  for (let i = start; i < start + size && i < sales.length; i++) {
    sum += sales[i];
    count++;
  }
  console.log(sum, sum / count);
}
```

Expected output:

```text
18 6
21 7
8 8
```

The final group contains one value. Dividing by size would incorrectly give 8 / 3 rather than 8 / 1. The outer condition ensures each processed group contains at least one element, so count cannot be zero. Keeping sum outside the outer loop would accumulate across groups instead of reporting independent totals.

### Full chunks only

Sometimes incomplete groups must be ignored, such as when each record requires exactly three fields. Change the outer condition to start + size <= values.length. Equality is necessary because a chunk ending exactly at the array boundary is complete.

```javascript
const values = [1, 2, 3, 4, 5, 6, 7];
const size = 3;
for (let start = 0; start + size <= values.length; start += size) {
  let sum = 0;
  for (let i = start; i < start + size; i++) {
    sum += values[i];
  }
  console.log(sum);
}
```

Expected output:

```text
6
15
```

At start six, 6 + 3 <= 7 is false. The partial group is ignored. If the size exceeds the array length, no full chunks are processed. An alternative requirement could retain the partial group or pad it with a specified value; those are different policies, not interchangeable fixes.

### Practice: chunks

**Question 1.** Find the maximum of each group of two values in [-8, -3, -9, -4, -7]. Retain the partial group. Expected: [-3, -4, -7].

**Solution.** Initialize each maximum to the first actual element, not zero, because all values may be negative. Start comparisons at the following position.

```javascript
const values = [-8, -3, -9, -4, -7];
const result = [];
const size = 2;
for (let start = 0; start < values.length; start += size) {
  let max = values[start];
  for (let i = start + 1; i < start + size && i < values.length; i++) {
    if (values[i] > max) max = values[i];
  }
  result.push(max);
}
console.log(result.join(','));
```

Expected output: `-3,-4,-7`. For the final chunk, max starts at -7 and the inner loop never runs. The singleton maximum is therefore correct.

**Question 2.** Pad the final group of [1, 2, 3, 4, 5] to size three using zeros. Expected groups: [1, 2, 3] and [4, 5, 0].

**Solution.** Run the inner loop exactly size times. Calculate the source index as start + offset. Append a real value only if the source index is valid; otherwise append zero.

```javascript
const values = [1, 2, 3, 4, 5];
const size = 3;
for (let start = 0; start < values.length; start += size) {
  const chunk = [];
  for (let offset = 0; offset < size; offset++) {
    const index = start + offset;
    if (index < values.length) chunk.push(values[index]);
    else chunk.push(0);
  }
  console.log(chunk.join(','));
}
```

Expected output:

```text
1,2,3
4,5,0
```

At start three and offset two, index equals five, which is outside the array. The padding branch executes. Padding is appropriate only when the requirements explicitly permit synthetic values.

## Integrated practice

### Circular batches

Process eight readings from a repeating sequence [2, 5, 7], starting at index one, in batches of three. Report the sum of each batch, including the partial final batch. The visited sequence is 5, 7, 2, 5, 7, 2, 5, 7. Expected totals: 14, 14, 12.

The circular position and the batch position serve different purposes. Use a global step number to calculate the source index, and use chunk boundaries to reset the sum. The total number of steps is eight, not the length of the source array.

```javascript
const values = [2, 5, 7];
const offset = 1;
const total = 8;
const size = 3;
for (let start = 0; start < total; start += size) {
  let sum = 0;
  for (let step = start; step < start + size && step < total; step++) {
    const index = (offset + step) % values.length;
    sum += values[index];
  }
  console.log(sum);
}
```

Expected output:

```text
14
14
12
```

At step five, offset + step equals six. Six % three equals zero, so the selected reading is two. At step seven the selected reading is seven. The final batch stops when step reaches eight. The source must be nonempty, total a nonnegative integer, and size a positive integer.

### Detect changes within batches

Given [3, 5, 2, 8, 9, 4, 7], count increases within each group of three. Do not compare across group boundaries. Expected counts: 1, 1, 0.

```javascript
const values = [3, 5, 2, 8, 9, 4, 7];
const size = 3;
for (let start = 0; start < values.length; start += size) {
  let increases = 0;
  for (let i = start + 1; i < start + size && i < values.length; i++) {
    if (values[i] > values[i - 1]) increases++;
  }
  console.log(increases);
}
```

Expected output:

```text
1
1
0
```

The inner loop begins at start + 1 rather than start. At the second group, the first comparison is 9 versus 8, not 8 versus 2. The final singleton contains no adjacent pair. This combines chunk boundaries with adjacent comparison without requiring intermediate arrays.

## Summary

Consecutive processing uses relationships between neighboring elements and may track run boundaries. Circular iteration normalizes positions into a repeating valid index range and needs an independent termination condition. Chunk processing advances by group size and requires an explicit policy for incomplete groups. For each technique, derive the valid index range, identify the state that persists or resets, and test empty, singleton and boundary inputs.

[Previous](21-part-02-prefix-suffix-aggregates-and-sliding-window.md) | [Next (planned)](21-part-04-reducing-search-space-and-binary-search.md)
