# Lesson 27 - Part 6: Independent Problem-Solving

[Previous: Part 5](27-part-05-evaluating-solutions.md) | [Complete lesson](27-solving-problems-with-loops.md)

## Contents

- [How to approach the problems](#how-to-approach-the-problems)
- [Problems](#problems)
- [Worked solutions](#worked-solutions)
- [Independent mastery checklist](#independent-mastery-checklist)

## How to approach the problems

Independent problem-solving does not mean inventing a new algorithm every time. It means deciding which established techniques apply, adapting them to the requirements and checking the resulting implementation. Before looking at an answer, write down the input, output, constraints and at least one manual calculation. Decide what information must survive from one iteration to the next. Give each variable a specific responsibility. Then choose boundaries and updates that make progress toward the result.

All numeric examples below assume dense arrays of finite integers, with arithmetic within the safe integer range. Records have the stated properties. We use traditional loops, not array iteration methods. The questions are separated from their answers so you can attempt them independently.

## Problems

### Problem 1 - Positive transaction report

Given `[-4, 10, 0, 6, -2]`, report the number, total and average of strictly positive transactions. If none qualify, return a count and total of zero and an average of `null`. Do not modify the input. Expected: count 2, total 16, average 8. Also test an empty array and `[0, -1]`.

### Problem 2 - Longest increasing run

Given `[2, 4, 6, 3, 5, 7, 9]`, find the starting index and length of the longest contiguous strictly increasing run. Keep the earliest run in a tie. Return start -1 and length 0 for an empty array. Expected: start 3, length 4. For `[1, 2, 1, 2]`, expect start 0, length 2. Equal neighbors do not extend a run.

### Problem 3 - Highest total in a fixed window

Given `[4, -2, 7, 1, 3]`, find the greatest sum of three consecutive values and its starting index. Keep the earliest tie. Assume the window size is an integer between 1 and the array length. Expected: start 2, sum 11. Also consider all-negative inputs. First derive a direct solution, then reuse overlapping totals.

### Problem 4 - Grouped sales report

Given records `{item: 'pen', amount: 3}`, `{item: 'book', amount: 8}`, `{item: 'pen', amount: 5}`, `{item: 'book', amount: 2}`, calculate the total and number of transactions for each item, preserving first appearance order. Expected: pen 8 from 2, book 10 from 2. An empty input produces no groups. Amounts may be negative (refunds).

### Problem 5 - Pair search in sorted data

Given sorted ascending values `[1, 3, 4, 6, 9]` and target 10, find any two distinct indexes whose values sum to the target. Return `null` if none exist. Expected valid result: indexes 0 and 4. Test `[5, 5]` with target 10 and `[5]` with target 10. Do not modify the input.

### Problem 6 - Eligible student ranking

Given records A: 70, B: 90, C: 90, D: 40, E: 80, retain scores at least 50, rank them from highest to lowest, preserve original order for ties, and calculate the average of the first three eligible students (or all eligible students if fewer). If none qualify, the average is null. Expected ranking B, C, E, A; selected average 260 / 3. Preserve the original array and records.

### Problem 7 - Range totals with repeated queries

Given `[5, 2, 8, 1, 4]`, answer inclusive index queries `[1, 3]`, `[0, 4]`, `[2, 2]`. Expected totals 11, 20, 8. Assume valid query indexes. Prepare cumulative totals once rather than traverse the requested range for each query.

### Problem 8 - Circular batches

Given `[10, 20, 30, 40, 50]`, visit exactly five elements starting at index 3, wrapping to index 0 when necessary. Divide the visited values into batches of size two, retaining a shorter final batch. Expected batches `[40, 50]`, `[10, 20]`, `[30]`. Assume a valid start and positive integer batch size. For empty input return no batches. Do not modify the input.

## Worked solutions

### Solution 1 - Positive transaction report

A count answers how many; a total answers how much. The average depends on both, so calculate it only after processing the input. Manually, -4 is rejected, 10 is accepted, 0 is rejected, 6 is accepted, and -2 is rejected. Both variables must be updated under the same condition.

Start with `let count = 0;` and `let total = 0;`. Neither describes any processed transaction yet. The condition `amount > 0` compares the current number with zero and returns a Boolean. When true, `count++` adds one to the count and `total += amount` adds the amount to the accumulated sum. For amount 6 and total 10, this means `total = 10 + 6`, giving 16.

```javascript
function report(values) {
    let count = 0;
    let total = 0;
    for (let i = 0; i < values.length; i++) {
        const amount = values[i];
        if (amount > 0) {
            count++;
            total += amount;
        }
    }
    let average = null;
    if (count > 0) {
        average = total / count;
    }
    return { count: count, total: total, average: average };
}
const result = report([-4, 10, 0, 6, -2]);
console.log(result.count, result.total, result.average);
console.log(report([]).average);
console.log(report([0, -1]).count);
```

Expected output:

```text
2 16 8
null
0
```

The function parameter `values` receives the array. The loop initializes i to 0, tests i against the array length before each iteration and increments it afterward. `values[i]` retrieves the current transaction. The return statement constructs an object with three named properties, making the results available to the caller. `result.count` reads the count property of that object.

| Amount | Count afterward | Total afterward |
|---:|---:|---:|
| -4 | 0 | 0 |
| 10 | 1 | 10 |
| 0 | 1 | 10 |
| 6 | 2 | 16 |
| -2 | 2 | 16 |

Dividing by `values.length` would give 16 / 5, which answers a different question. Dividing by zero would produce NaN when both total and count are zero. The guard avoids that calculation. Each iteration preserves the fact that count and total describe only positive values already visited. Time O(n), auxiliary space O(1).

### Solution 2 - Longest increasing run

A run is a contiguous section of the array. We cannot skip the 3 between 6 and 5 to construct a longer sequence. The comparison must therefore involve adjacent elements.

For a nonempty array, the first value forms a run of length one. Track its starting index and length, and separately remember the best run found. At index i, compare `values[i] > values[i - 1]`. For i = 3, i - 1 is 2; the two values are 3 and 6. The comparison is false, so a new run starts at 3 with length one. The historical best must not be reset.

```javascript
function longestRun(values) {
    if (values.length === 0) {
        return { start: -1, length: 0 };
    }
    let currentStart = 0;
    let currentLength = 1;
    let bestStart = 0;
    let bestLength = 1;
    for (let i = 1; i < values.length; i++) {
        if (values[i] > values[i - 1]) {
            currentLength++;
        } else {
            currentStart = i;
            currentLength = 1;
        }
        if (currentLength > bestLength) {
            bestLength = currentLength;
            bestStart = currentStart;
        }
    }
    return { start: bestStart, length: bestLength };
}
let result = longestRun([2, 4, 6, 3, 5, 7, 9]);
console.log(result.start, result.length);
result = longestRun([1, 2, 1, 2]);
console.log(result.start, result.length);
result = longestRun([]);
console.log(result.start, result.length);
```

Expected output:

```text
3 4
0 2
-1 0
```

| i | Current start | Current length | Best start | Best length |
|---:|---:|---:|---:|---:|
| Initial | 0 | 1 | 0 | 1 |
| 1 | 0 | 2 | 0 | 2 |
| 2 | 0 | 3 | 0 | 3 |
| 3 | 3 | 1 | 0 | 3 |
| 4 | 3 | 2 | 0 | 3 |
| 5 | 3 | 3 | 0 | 3 |
| 6 | 3 | 4 | 3 | 4 |

The first guard returns before reading any element when the input is empty. The loop starts at 1 because index 0 has no predecessor. In the else block, assigning i to currentStart records the new run's first position. Resetting length to 1 counts the current element itself. The second if is independent of the first: after either extension or reset, compare the current run against the best. Strict `>` preserves the earlier run when lengths tie; `>=` would replace it. The best start and length must be updated together. The loop visits every adjacent pair exactly once, and after each iteration the best variables describe the longest run in the processed prefix. Time O(n), space O(1).

### Solution 3 - Highest total in a fixed window

A window is a contiguous range of a specified length. The possible windows are `[4,-2,7]` (9), `[-2,7,1]` (6), and `[7,1,3]` (11). For an array of length n and window size k, the last valid start is n - k. At that start, the final index is (n - k) + k - 1 = n - 1.

The direct implementation uses an outer loop to choose the start and an inner loop to add k values. Initialize the best total from the first actual window rather than zero, because every window may be negative.

```javascript
function direct(values, k) {
    let bestSum = null;
    let bestStart = 0;
    for (let start = 0; start <= values.length - k; start++) {
        let sum = 0;
        for (let offset = 0; offset < k; offset++) {
            sum += values[start + offset];
        }
        if (bestSum === null || sum > bestSum) {
            bestSum = sum;
            bestStart = start;
        }
    }
    return { start: bestStart, sum: bestSum };
}
const result = direct([4, -2, 7, 1, 3], 3);
console.log(result.start, result.sum);
```

Expected output:

```text
2 11
```

At start 2 and offset 1, `start + offset` is 3, so the value read is 1. The inner accumulator is reset for every new window. `bestSum === null || sum > bestSum` first checks whether a candidate has been recorded. If not, the first window is accepted without evaluating the second comparison. Otherwise only a strictly larger sum replaces it.

Now notice that adjacent windows share two values. To move from the first window to the second, remove 4 and add 1: 9 - 4 + 1 = 6. To move again, remove -2 and add 3: 6 - (-2) + 3 = 11. We can maintain the sum rather than recalculate it.

```javascript
function rolling(values, k) {
    let sum = 0;
    for (let i = 0; i < k; i++) {
        sum += values[i];
    }
    let bestSum = sum;
    let bestStart = 0;
    for (let right = k; right < values.length; right++) {
        const outgoingIndex = right - k;
        sum -= values[outgoingIndex];
        sum += values[right];
        const start = right - k + 1;
        if (sum > bestSum) {
            bestSum = sum;
            bestStart = start;
        }
    }
    return { start: bestStart, sum: bestSum };
}
let result = rolling([4, -2, 7, 1, 3], 3);
console.log(result.start, result.sum);
result = rolling([-8, -2, -5], 2);
console.log(result.start, result.sum);
```

Expected output:

```text
2 11
1 -7
```

At right = 3, `right - k` is 0, the index leaving the window. `sum -= values[0]` means sum = 9 - 4. `sum += values[3]` then gives 5 + 1 = 6. The new start is 3 - 3 + 1 = 1. At right = 4, the outgoing index is 1 and the new start is 2. Subtracting a negative outgoing value correctly increases the sum.

The first loop establishes the initial window. The second loop starts at k because indexes 0 through k - 1 have already been included. Its invariant is that after both updates, sum equals the total of the k elements ending at right. For k = n the second loop never executes, which is correct because only one window exists. Direct cost is O((n-k+1)k), rolling cost O(n), both O(1) auxiliary space. Negative values do not invalidate this fixed-size technique.

### Solution 4 - Grouped sales report

Each distinct item needs its own count and total. A single global accumulator cannot distinguish pen from book. A Map associates a key with a value; here the key is an item name and the value is an object containing its count and total. We also keep an array of keys in first appearance order, so traversal uses a plain for loop.

For the first pen transaction, no group exists. Create one with zero count and total. Then apply the same update used for every transaction. For the next pen transaction, reuse the existing group rather than reset it.

```javascript
function groupSales(records) {
    const groups = new Map();
    const keys = [];
    for (let i = 0; i < records.length; i++) {
        const record = records[i];
        const key = record.item;
        if (!groups.has(key)) {
            groups.set(key, { count: 0, total: 0 });
            keys.push(key);
        }
        const group = groups.get(key);
        group.count++;
        group.total += record.amount;
    }
    const result = [];
    for (let i = 0; i < keys.length; i++) {
        const key = keys[i];
        const group = groups.get(key);
        result.push({ item: key, count: group.count, total: group.total });
    }
    return result;
}
const result = groupSales([
    {item: 'pen', amount: 3}, {item: 'book', amount: 8},
    {item: 'pen', amount: 5}, {item: 'book', amount: 2}
]);
for (let i = 0; i < result.length; i++) {
    console.log(result[i].item, result[i].total, result[i].count);
}
console.log(groupSales([]).length);
```

Expected output:

```text
pen 8 2
book 10 2
0
```

`groups.has(key)` returns true when the key exists. The `!` operator reverses that Boolean, so the creation block runs only for new items. `groups.set` stores a new object; `keys.push(key)` appends its name. `groups.get(key)` retrieves the stored object, not an independent copy. Consequently, updating group.total also updates the object held by the Map. The const declaration prevents reassignment of group, not mutation of its properties.

| Transaction | Pen count/total | Book count/total |
|---|---|---|
| pen 3 | 1 / 3 | absent |
| book 8 | 1 / 3 | 1 / 8 |
| pen 5 | 2 / 8 | 1 / 8 |
| book 2 | 2 / 8 | 2 / 10 |

In the second loop, each key is retrieved in insertion order and a new report object is appended. `result[i].item` first retrieves the object at index i and then reads its item property. Creating a group unconditionally would discard earlier totals. Adding keys unconditionally would produce repeated report rows. Refunds require no special branch because adding a negative amount reduces the total. With typical constant-time Map operations, expected time O(n + g) and auxiliary space O(g), where g is the number of distinct items; JavaScript does not guarantee constant-time Map operations.

### Solution 5 - Pair search in sorted data

The sorted order lets us discard candidates without testing every pair. Start with the smallest and largest values. If their sum is too small, the smallest value cannot reach the target with any remaining partner, because the largest remaining partner was already tried. Move the left pointer. If the sum is too large, the largest value cannot reach the target with any remaining partner, because even the smallest remaining partner was too large. Move the right pointer.

```javascript
function findPair(values, target) {
    let left = 0;
    let right = values.length - 1;
    while (left < right) {
        const sum = values[left] + values[right];
        if (sum === target) {
            return [left, right];
        }
        if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return null;
}
console.log(findPair([1, 3, 4, 6, 9], 10).join(' '));
console.log(findPair([5, 5], 10).join(' '));
console.log(findPair([5], 10));
console.log(findPair([1, 3, 4, 6, 9], 7).join(' '));
```

Expected output:

```text
0 4
0 1
null
0 3
```

`values.length - 1` computes the last valid index. The strict condition left < right ensures the indexes differ. At left 0 and right 4, `values[left] + values[right]` evaluates to 1 + 9 = 10. The return statement exits the function immediately with both indexes. The join method converts the returned array into a string with spaces between its elements; it is used only to display a known successful result.

For target 7, the initial sum 10 is too large, so right decreases from 4 to 3. Now 1 + 6 = 7. For target 8, the sequence of pointer decisions would be: 10 too large, 7 too small, 9 too large, 7 too small, then pointers meet. No pair exists. Moving both pointers at once could discard a valid pair. The sorted-order assumption is essential; the elimination argument does not hold for arbitrary order. Each unsuccessful iteration reduces right - left by one, guaranteeing termination. Time O(n), auxiliary space O(1).

### Solution 6 - Eligible student ranking

Separate the requirements into filtering, ranking and averaging. The filtering stage produces A, B, C, E. The ranking stage produces B, C, E, A. The averaging stage selects B, C, E and computes (90 + 90 + 80) / 3. Do not sort the original array. Create a separate array containing references to eligible records, then reorder those references without changing record properties.

Insertion sort maintains a sorted prefix. Take the next record, save it temporarily, shift lower-scoring records right and insert the saved record into the gap. Equal scores must not shift, preserving their original order.

```javascript
function rankStudents(students) {
    const eligible = [];
    for (let i = 0; i < students.length; i++) {
        if (students[i].score >= 50) {
            eligible.push(students[i]);
        }
    }
    for (let i = 1; i < eligible.length; i++) {
        const current = eligible[i];
        let j = i - 1;
        while (j >= 0 && eligible[j].score < current.score) {
            eligible[j + 1] = eligible[j];
            j--;
        }
        eligible[j + 1] = current;
    }
    let count = eligible.length;
    if (count > 3) {
        count = 3;
    }
    let total = 0;
    for (let i = 0; i < count; i++) {
        total += eligible[i].score;
    }
    let average = null;
    if (count > 0) {
        average = total / count;
    }
    return { ranking: eligible, average: average };
}
const students = [
    {name: 'A', score: 70}, {name: 'B', score: 90},
    {name: 'C', score: 90}, {name: 'D', score: 40},
    {name: 'E', score: 80}
];
const result = rankStudents(students);
for (let i = 0; i < result.ranking.length; i++) {
    console.log(result.ranking[i].name);
}
console.log(result.average === 260 / 3);
console.log(students[0].name);
console.log(rankStudents([]).average);
```

Expected output:

```text
B
C
E
A
true
A
null
```

In filtering, `students[i].score` retrieves a record and reads its score. The >= comparison includes exactly 50. In sorting, i starts at 1 because a one-element prefix is already sorted. Saving current before shifting prevents losing the record. j begins one position to its left. The && condition checks j >= 0 first; if false, JavaScript does not evaluate eligible[j].score, avoiding access to a missing record. For i = 1, current is B, j is 0, and 70 < 90 is true. `eligible[j + 1] = eligible[j]` copies A from index 0 to index 1. j becomes -1; the while condition stops. `eligible[j + 1] = current` inserts B at index 0.

| Saved record | Sorted prefix after insertion |
|---|---|
| A (initial) | A |
| B | B A |
| C | B C A |
| E | B C E A |

C shifts A but not B, because 90 < 90 is false. Changing the comparison to <= would break stability. The count variable starts at the eligible length, then is capped at three. This avoids reading nonexistent records if only one or two qualify. The average guard avoids division by zero. The result object exposes the full ranking and average. The final comparison checks the expected average using the same arithmetic, and the original first record remains A. With n students and m eligible records, worst-case time O(n + m^2), auxiliary space O(m). A partial selection algorithm could be preferable if only the top three were required, but the contract also asks for the full ranking.

### Solution 7 - Range totals with repeated queries

A prefix sum stores the total before a boundary. Start with zero, then append the total after each element. For the input, the prefix array is `[0, 5, 7, 15, 16, 20]`. To find the inclusive range from 1 to 3, take the total through index 3 (16) and subtract the total before index 1 (5). The result is 11.

```javascript
const values = [5, 2, 8, 1, 4];
const queries = [[1, 3], [0, 4], [2, 2]];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
    const nextTotal = prefix[i] + values[i];
    prefix.push(nextTotal);
}
for (let i = 0; i < queries.length; i++) {
    const query = queries[i];
    const left = query[0];
    const right = query[1];
    const total = prefix[right + 1] - prefix[left];
    console.log(total);
}
```

Expected output:

```text
11
20
8
```

At i = 2, prefix[2] is 7, values[2] is 8, so nextTotal is 15. Appending places it at index 3. Thus prefix[i] always describes the total of the first i elements, not the total through element i. In the query loop, query[0] and query[1] read the two boundaries. For right = 3, right + 1 is 4, and prefix[4] is 16. For left = 1, prefix[1] is 5. Subtracting cancels the values before the requested range. Using prefix[right] would omit the final element. The single-element query [2,2] yields prefix[3] - prefix[2] = 15 - 7 = 8. Preparation takes O(n), q queries take O(q), and storage is O(n). If the original array changes, the prepared prefix totals may no longer be valid. For one short query, direct traversal may be simpler.

### Solution 8 - Circular batches

The problem has two independent stages: determine visit order and divide the visited sequence into batches. First visit indexes 3, 4, 0, 1, 2. Then group the resulting values in pairs. We can implement both stages separately to make the reasoning clear.

```javascript
function circularBatches(values, start, size) {
    const visited = [];
    const n = values.length;
    if (n === 0) {
        return [];
    }
    for (let offset = 0; offset < n; offset++) {
        const index = (start + offset) % n;
        visited.push(values[index]);
    }
    const batches = [];
    for (let first = 0; first < visited.length; first += size) {
        const batch = [];
        let end = first + size;
        if (end > visited.length) {
            end = visited.length;
        }
        for (let i = first; i < end; i++) {
            batch.push(visited[i]);
        }
        batches.push(batch);
    }
    return batches;
}
const batches = circularBatches([10, 20, 30, 40, 50], 3, 2);
for (let i = 0; i < batches.length; i++) {
    console.log(batches[i].join(' '));
}
console.log(circularBatches([], 0, 2).length);
```

Expected output:

```text
40 50
10 20
30
0
```

The empty guard avoids remainder by zero. In the first loop, offset counts visits rather than physical array indexes. For offset 2, start + offset = 5, and 5 % 5 = 0, wrapping to the beginning. Since start and offset are nonnegative, the remainder is a valid index. Exactly n iterations visit every element once.

| Offset | start + offset | Remainder | Value |
|---:|---:|---:|---:|
| 0 | 3 | 3 | 40 |
| 1 | 4 | 4 | 50 |
| 2 | 5 | 0 | 10 |
| 3 | 6 | 1 | 20 |
| 4 | 7 | 2 | 30 |

The outer batch loop advances first by size, not one, so batches do not overlap. The end variable is exclusive: when first is 4, first + size is 6, but the array length is 5, so end becomes 5. The inner loop visits index 4 only. Creating batch inside the outer loop gives every group its own array; creating it outside would reuse the same array reference. Appending the completed batch preserves its values. A size of zero would prevent progress, so the positive-size precondition matters. Time O(n), auxiliary space O(n) for visited, plus O(n) output storage. An alternative is to compute the circular index directly inside the batch loop, eliminating visited, but the two-stage implementation is easier to inspect and debug.

## Independent mastery checklist

Try each problem again with different inputs without reading the answer. Explain the purpose and initial value of every state variable, evaluate the central expressions using actual indexes, trace at least one complete iteration, and justify the stopping condition. Then test the smallest permitted input, ties, no-match cases and any unusual values allowed by the contract. Compare an alternative implementation and explain its trade-offs. Do not consider a problem mastered merely because you recognize the solution when reading it.

This completes the Plain Loops in JavaScript curriculum. Continue practicing with unfamiliar problems until you can derive and verify solutions independently.

[Previous: Part 5](27-part-05-evaluating-solutions.md) | [Complete lesson](27-solving-problems-with-loops.md)
