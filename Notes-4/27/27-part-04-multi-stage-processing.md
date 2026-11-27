# Lesson 27 - Part 4: Multi-Stage Processing

[Previous](27-part-03-recognizing-and-combining-loop-patterns.md) | [Complete chapter](27-solving-problems-with-loops.md) | Next: Part 5 - Evaluating Solutions (planned)

## Contents

- [Learning objectives](#learning-objectives)
- [Understanding stages and dependencies](#understanding-stages-and-dependencies)
- [Selecting and transforming values](#selecting-and-transforming-values)
- [Preparing data before searching](#preparing-data-before-searching)
- [Ranking eligible students](#ranking-eligible-students)
- [Grouping before calculating averages](#grouping-before-calculating-averages)
- [Combining or separating traversals](#combining-or-separating-traversals)
- [Integrated practice](#integrated-practice)
- [Summary](#summary)

## Learning objectives

By the end of this part, you should be able to divide a problem into stages, identify dependencies between them, design intermediate results, trace each stage independently, combine stages safely and verify the complete solution. We use plain loops, arrays, records, functions and Map as established in earlier chapters. All numeric examples assume finite numbers of ordinary magnitude unless stated otherwise.

## Understanding stages and dependencies

Suppose a shop records daily sales of `[40, 10, 60, 20]`. We want the average of days with sales of at least 30. Manually, we select 40 and 60, add them to obtain 100, and divide by 2 to obtain 50.

A processing stage is a logically distinct operation that produces information needed by another operation. A dependency exists when one operation needs a result produced by another. Here division depends on both the total and the number of qualifying days. Selection must happen before a value contributes to either quantity.

The data moves through these stages:

```text
Original values: 40 10 60 20
                    |
                  select
                    v
Selected values: 40 60
                    |
                  total and count
                    v
Total: 100, count: 2
                    |
                  divide
                    v
Average: 50
```

An intermediate result is information produced along the way, rather than the final answer. It may be an array, a number, a record or another data structure. We do not always need to store every intermediate result in an array. First, however, it is helpful to make each stage visible.

### Stage 1: Select qualifying values

Start with `const selected = [];`. This creates an empty array that will hold only qualifying values. The binding is constant, but the array contents can change. For each index `i`, read `sales[i]` and compare it with 30. The operator `>=` includes equality, so a day with exactly 30 qualifies. `selected.push(sales[i])` reads the current value and appends it to the array.

### Stage 2: Calculate the total

Start `total` at zero, the additive identity. Traverse `selected`, not the original array. The expression `total += selected[i]` means `total = total + selected[i]`. On the second iteration, the previous total is 40 and the current value is 60, so the new total is 100.

### Stage 3: Calculate the average

The number of selected days is `selected.length`. Division must occur after the total is complete. If no day qualifies, division by zero does not represent an average. We choose `null` to represent the absence of a result, rather than zero, which could be a valid average in other problems.

```javascript
const sales = [40, 10, 60, 20];
const selected = [];
for (let i = 0; i < sales.length; i++) {
    if (sales[i] >= 30) {
        selected.push(sales[i]);
    }
}
let total = 0;
for (let i = 0; i < selected.length; i++) {
    total += selected[i];
}
let average = null;
if (selected.length > 0) {
    average = total / selected.length;
}
console.log(selected.join(' '));
console.log(total, selected.length, average);
```

Expected output:

```text
40 60
100 2 50
```

`join(' ')` constructs a string from the array elements with spaces between them, solely for displaying the result. It does not modify the array.

| Original index | Value | Qualifies? | Selected after iteration |
|---|---|---|---|
| 0 | 40 | yes | 40 |
| 1 | 10 | no | 40 |
| 2 | 60 | yes | 40, 60 |
| 3 | 20 | no | 40, 60 |

Notice that each loop starts at zero because it traverses a different array from its beginning. Reusing the original array's length for the second loop would read nonexistent elements and could turn the total into NaN.

### Practice 1

Given `[12, 5, 18, 7, 10]`, select values strictly greater than 10 and calculate their average. Return null if none qualify. Expected selected values: 12 and 18; expected average: 15.

#### Worked answer

The word strictly means use `>` rather than `>=`. In particular, 10 must not be selected. The total is 12 + 18 = 30. There are two qualifying values, so the average is 15.

```javascript
const values = [12, 5, 18, 7, 10];
const selected = [];
for (let i = 0; i < values.length; i++) {
    if (values[i] > 10) {
        selected.push(values[i]);
    }
}
let sum = 0;
for (let i = 0; i < selected.length; i++) {
    sum += selected[i];
}
let average = null;
if (selected.length !== 0) {
    average = sum / selected.length;
}
console.log(selected.join(' '));
console.log(average);
```

Expected output:

```text
12 18
15
```

The first loop establishes the selection requirement. The second accumulates exactly those values. `selected.length !== 0` checks that the denominator is not zero. With `[5, 10]` the selection is empty and average remains null. With `[11]` the average is 11.

## Selecting and transforming values

A transformation changes a value into another representation. Selection decides whether to keep it. These operations may depend on their order.

Suppose prices `[80, 120, 200, 90]` are in whole currency units. Select original prices of at least 100, then reduce each selected price by 20 percent. The expected discounted prices are 96 and 160.

The discount amount for 120 is `120 * 20 / 100 = 24`. The new price is `120 - 24 = 96`. We can calculate it with separate variables to make the arithmetic clear.

```javascript
const prices = [80, 120, 200, 90];
const eligible = [];
for (let i = 0; i < prices.length; i++) {
    if (prices[i] >= 100) {
        eligible.push(prices[i]);
    }
}
const discounted = [];
for (let i = 0; i < eligible.length; i++) {
    const price = eligible[i];
    const discount = price * 20 / 100;
    const finalPrice = price - discount;
    discounted.push(finalPrice);
}
console.log(eligible.join(' '));
console.log(discounted.join(' '));
```

Expected output:

```text
120 200
96 160
```

The expression `price * 20 / 100` evaluates multiplication and division from left to right because they have equal precedence. For 200, it becomes 4000 / 100 = 40. Subtracting 40 gives 160. The temporary variables are declared inside the loop because each belongs to one price. The output array is outside because it must survive every iteration.

If we discount all prices first and then select discounted prices of at least 100, 96 would be excluded. That is a different requirement, not an equivalent optimization. In production financial software, rounding and exact monetary representation also require an explicit policy; this example uses simple values with exact whole-unit results.

### Practice 2

Select temperatures above 20 Celsius from `[10, 25, 30, 20]`, then convert them to Fahrenheit using `C * 9 / 5 + 32`. Expected output: 77 and 86.

#### Worked answer

Selection uses the original Celsius values. For 25, multiplication gives 225, division gives 45, and addition gives 77. For 30, the corresponding intermediate results are 270, 54 and 86.

```javascript
const temperatures = [10, 25, 30, 20];
const result = [];
for (let i = 0; i < temperatures.length; i++) {
    const celsius = temperatures[i];
    if (celsius <= 20) {
        continue;
    }
    const scaled = celsius * 9 / 5;
    const fahrenheit = scaled + 32;
    result.push(fahrenheit);
}
console.log(result.join(' '));
```

Expected output:

```text
77 86
```

Here selection and transformation are performed in one traversal. `continue` skips the remaining body for a nonqualifying temperature; the for-loop update still executes. This is safe because transforming one temperature does not depend on any other temperature. An empty input produces an empty result.

## Preparing data before searching

Binary search depends on sorted data. If the input is unsorted, comparing the middle element with a target cannot reliably eliminate half the candidates. We can prepare the data first, then search it.

Problem: Given `[9, 2, 7, 4]`, find the index of 7 in a sorted copy without changing the original. The sorted copy is `[2, 4, 7, 9]`, so the expected index is 2. This is a sorted index, not the original index.

### Stage 1: Copy the values

Use an empty array and append each original value. Since these are numbers, the copy contains independent numeric values. Sorting the copy does not rearrange the original.

### Stage 2: Insertion sort

Insertion sort maintains a sorted prefix. At index `i`, save the value in `key`. Move larger preceding values one position to the right until the insertion position is found. `j` identifies the preceding candidate, initially `i - 1`.

For `[9, 2, 7, 4]`, the first key is 2. Since 9 > 2, copy 9 from index 0 to index 1. Decrease `j` to -1. Now `j + 1` is 0, the correct insertion position for 2. The intermediate array temporarily contains duplicate 9s, but the saved key prevents losing 2.

| Completed insertion | Array |
|---|---|
| key 2 | 2, 9, 7, 4 |
| key 7 | 2, 7, 9, 4 |
| key 4 | 2, 4, 7, 9 |

### Stage 3: Binary search

Use inclusive boundaries `left = 0` and `right = length - 1`. The expression `left + Math.trunc((right - left) / 2)` calculates the middle index. `Math.trunc` removes the fractional part. With left 0 and right 3, the difference is 3, half is 1.5, truncation gives 1, and adding left gives 1. The value at index 1 is 4, less than 7. Therefore indexes 0 and 1 cannot contain 7. Set left to 2. The next midpoint is 2, where the target is found.

```javascript
const original = [9, 2, 7, 4];
const sorted = [];
for (let i = 0; i < original.length; i++) {
    sorted.push(original[i]);
}
for (let i = 1; i < sorted.length; i++) {
    const key = sorted[i];
    let j = i - 1;
    while (j >= 0 && sorted[j] > key) {
        sorted[j + 1] = sorted[j];
        j--;
    }
    sorted[j + 1] = key;
}
const target = 7;
let left = 0;
let right = sorted.length - 1;
let foundIndex = -1;
while (left <= right) {
    const mid = left + Math.trunc((right - left) / 2);
    if (sorted[mid] === target) {
        foundIndex = mid;
        break;
    }
    if (sorted[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
console.log(original.join(' '));
console.log(sorted.join(' '));
console.log(foundIndex);
```

Expected output:

```text
9 2 7 4
2 4 7 9
2
```

In the sorting condition, `&&` evaluates its right operand only when `j >= 0` is true. This prevents reading an index before the beginning. `sorted[j + 1] = sorted[j]` first reads the value at j, then stores it one position to the right. The decrement moves to the next preceding candidate. In the search, `mid + 1` and `mid - 1` exclude the already examined midpoint and guarantee progress. The sentinel -1 cannot be a valid array index. An empty array sets right to -1, so the search never enters its body.

Sorting for a single search is usually unnecessary: a direct linear search can find a target without sorting. Sorting may be justified for repeated queries or when sorted positions are themselves required. Insertion sort takes quadratic time in the worst case; binary search takes logarithmic time on sorted data. The preparation cost must be included when comparing solutions.

### Practice 3

For `[8, 1, 5, 3]`, find the smallest value greater than or equal to 4, without sorting. Expected answer: 5. Return null if no value qualifies.

#### Worked answer

The requirement does not ask for a sorted index. We can combine qualification and minimum selection. Start with null, meaning no qualifying candidate has been found. For each qualifying value, replace the current best if it is absent or larger than the candidate.

```javascript
const values = [8, 1, 5, 3];
let best = null;
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value >= 4) {
        if (best === null || value < best) {
            best = value;
        }
    }
}
console.log(best);
```

Expected output:

```text
5
```

At index 0, 8 qualifies and best is null, so best becomes 8. Index 1 fails qualification. At index 2, 5 qualifies and 5 < 8 is true, so best becomes 5. Index 3 fails qualification. The `||` operator short-circuits when best is null; no numeric comparison with null is needed. The invariant is that best is the smallest qualifying value among the processed elements, or null if none exists. This solution needs only one traversal and constant auxiliary storage.

## Ranking eligible students

Problem: Given the records below, calculate the average score of the three highest-scoring eligible students. Eligibility means attendance of at least 75. If fewer than three students qualify, average all qualifying students. If none qualify, return null. Equal scores preserve original order. Do not reorder the original array.

| Student | Score | Attendance |
|---|---:|---:|
| A | 80 | 90 |
| B | 99 | 60 |
| C | 70 | 85 |
| D | 95 | 80 |
| E | 85 | 75 |

Manually, B is excluded. The eligible scores are 80, 70, 95, 85. In descending order they are 95, 85, 80, 70. The selected total is 260, and the average is 260 / 3.

### Stage 1: Filter records

Each array element is an object with properties `name`, `score` and `attendance`. `students[i].attendance` first obtains the record at index i and then reads its attendance property. Append the record if attendance is at least 75. The new array contains references to the original records, not deep copies. Rearranging references in the new array is safe here because we do not modify any record properties.

### Stage 2: Sort eligible records

Use insertion sort as above, but compare scores in descending order. The condition `eligible[j].score < key.score` means the preceding record has a lower score than the record being inserted, so it must move right. A strict comparison leaves equal scores in their original order. Changing `<` to `<=` could reverse equal-score records.

### Stage 3: Determine how many to select

Start with `limit = 3`. If `eligible.length < limit`, assign the length to limit. This ensures that the selection loop never reads beyond the array. It is equivalent to `Math.min(3, eligible.length)`, which returns the smaller of its two arguments.

### Stage 4: Calculate the result

Traverse only indexes 0 through limit - 1. Add each score and collect each name for verification. If limit is positive, divide total by limit. We display the average to two decimal places using `toFixed(2)`, which returns a formatted string; it does not change the numeric average.

```javascript
const students = [
    {name: 'A', score: 80, attendance: 90},
    {name: 'B', score: 99, attendance: 60},
    {name: 'C', score: 70, attendance: 85},
    {name: 'D', score: 95, attendance: 80},
    {name: 'E', score: 85, attendance: 75}
];
const eligible = [];
for (let i = 0; i < students.length; i++) {
    if (students[i].attendance >= 75) {
        eligible.push(students[i]);
    }
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
let limit = 3;
if (eligible.length < limit) {
    limit = eligible.length;
}
let total = 0;
const names = [];
for (let i = 0; i < limit; i++) {
    total += eligible[i].score;
    names.push(eligible[i].name);
}
let average = null;
if (limit > 0) {
    average = total / limit;
}
console.log(names.join(' '));
console.log(total);
console.log(average === null ? 'none' : average.toFixed(2));
```

Expected output:

```text
D E A
260
86.67
```

The final conditional expression selects the string 'none' when average is null; otherwise it calls the formatting method on the number. This avoids calling a number method on null. The average itself remains approximately 86.66666666666667.

| Selection index | Student | Score | Total after addition |
|---|---|---|---|
| 0 | D | 95 | 95 |
| 1 | E | 85 | 180 |
| 2 | A | 80 | 260 |

A common mistake is to select the first three eligible records before sorting. That would select A, C and D, excluding E even though E has a higher score than C. The dependency is therefore filter -> rank -> select -> aggregate. Another mistake is dividing by 3 even when only two students qualify. The denominator must match the number of selected records.

## Grouping before calculating averages

Problem: Given sales records for branches A and B, calculate each branch's average sale. Then identify the branch with the highest average, retaining the first encountered branch on a tie.

```text
A 10
B 30
A 50
B 10
```

A has total 60 and count 2, giving average 30. B has total 40 and count 2, giving average 20. A wins.

We need a separate accumulator for each branch. A Map associates each branch name (key) with a record containing total and count. `has(key)` checks whether a key exists, `set(key, value)` stores a value, and `get(key)` retrieves it. We also store branch names in an array in first-encounter order so that we can traverse the groups using a plain indexed loop.

When a branch first appears, create its accumulator with total 0 and count 0. On subsequent appearances retrieve the existing accumulator instead of replacing it. Because the accumulator is an object, updating its properties updates the object held by the Map.

```javascript
const sales = [
    {branch: 'A', amount: 10},
    {branch: 'B', amount: 30},
    {branch: 'A', amount: 50},
    {branch: 'B', amount: 10}
];
const groups = new Map();
const branches = [];
for (let i = 0; i < sales.length; i++) {
    const sale = sales[i];
    if (!groups.has(sale.branch)) {
        groups.set(sale.branch, {total: 0, count: 0});
        branches.push(sale.branch);
    }
    const group = groups.get(sale.branch);
    group.total += sale.amount;
    group.count++;
}
let bestBranch = null;
let bestAverage = null;
for (let i = 0; i < branches.length; i++) {
    const branch = branches[i];
    const group = groups.get(branch);
    const average = group.total / group.count;
    console.log(branch, average);
    if (bestBranch === null || average > bestAverage) {
        bestBranch = branch;
        bestAverage = average;
    }
}
console.log(bestBranch, bestAverage);
```

Expected output:

```text
A 30
B 20
A 30
```

`!groups.has(sale.branch)` negates the Boolean returned by has. It is true only when the branch has not been encountered. For the third sale, `sale.branch` is A; get returns the record whose total is 10 and count is 1. `group.total += sale.amount` becomes 10 + 50 = 60, and count becomes 2. No group has count zero by the time the second loop starts because creation is followed immediately by accumulation.

Do not select the winning branch while processing individual sales unless the requirement concerns intermediate averages. After the first two records B leads, but after all records A wins. Final group statistics depend on all contributing records. An empty input leaves both best variables null.

### Practice 4

Given category values `X: 5, Y: 9, X: 7, Y: 1, Z: 12`, find the category with the greatest total, retaining the first category on a tie. Expected result: X, 12.

#### Worked answer

Totals are X = 12, Y = 10, Z = 12. We only need totals, not counts. Store a number per category rather than a record. Keep the category order separately.

```javascript
const entries = [
    {category: 'X', value: 5}, {category: 'Y', value: 9},
    {category: 'X', value: 7}, {category: 'Y', value: 1},
    {category: 'Z', value: 12}
];
const totals = new Map();
const keys = [];
for (let i = 0; i < entries.length; i++) {
    const entry = entries[i];
    if (!totals.has(entry.category)) {
        totals.set(entry.category, 0);
        keys.push(entry.category);
    }
    const previous = totals.get(entry.category);
    const updated = previous + entry.value;
    totals.set(entry.category, updated);
}
let winner = null;
let maximum = null;
for (let i = 0; i < keys.length; i++) {
    const key = keys[i];
    const total = totals.get(key);
    if (winner === null || total > maximum) {
        winner = key;
        maximum = total;
    }
}
console.log(winner, maximum);
```

Expected output:

```text
X 12
```

Unlike an object accumulator, a numeric value retrieved from a Map is not updated by assigning to a local variable. We must store the updated number with set. When Z is examined, 12 > 12 is false, so X remains the winner. Initializing maximum to zero without a separate absence check could fail if all category totals were negative.

## Combining or separating traversals

Logical stages do not necessarily require separate loops. Selection and accumulation can be combined when each selected value contributes independently. Return to the sales average example. Instead of storing selected values, keep a count and total.

```javascript
const sales = [40, 10, 60, 20];
let total = 0;
let count = 0;
for (let i = 0; i < sales.length; i++) {
    const amount = sales[i];
    if (amount >= 30) {
        total += amount;
        count++;
    }
}
let average = null;
if (count > 0) {
    average = total / count;
}
console.log(total, count, average);
```

Expected output:

```text
100 2 50
```

The count replaces the selected array's length. It increases only when the same value contributes to total. The intermediate array is no longer needed because the final answer does not require the individual selected values. Both versions take linear time; the combined version needs constant auxiliary space rather than storage proportional to the number of selected values.

However, not all stages can be combined this way. Suppose the requirement is to count values above the average of the entire array. Given `[2, 8, 5]`, the average is 5 and only 8 qualifies. Comparing each value against the running average is incorrect: at index 1 the running average is 5, but a different later value could change the final threshold. For example `[2, 8, 100]` has final average 110 / 3, so 8 must not qualify even though it exceeds the earlier running average.

```javascript
const values = [2, 8, 100];
let sum = 0;
for (let i = 0; i < values.length; i++) {
    sum += values[i];
}
let count = 0;
if (values.length > 0) {
    const average = sum / values.length;
    for (let i = 0; i < values.length; i++) {
        if (values[i] > average) {
            count++;
        }
    }
}
console.log(count);
```

Expected output:

```text
1
```

The second traversal uses the completed average, not a changing partial result. The guard avoids division by zero. Two sequential linear loops still take linear time: n + n = 2n, which is O(n). Do not confuse sequential loops with nested loops. Separating stages is often the clearest correct design.

## Integrated practice

Attempt the following problems before reading their solutions.

### Problem 1: Batch quality report

Given `[4, -1, 6, 2, -3, 8, 10]`, discard negative readings, divide the remaining readings into consecutive batches of two, retain the final incomplete batch, and calculate each batch's average. Expected averages: 5, 5, 10. Empty input produces no batches. Explain why batching before filtering changes the result.

#### Worked solution 1

First filter: `[4, 6, 2, 8, 10]`. Then form batches `[4, 6]`, `[2, 8]`, `[10]`. The first batch sum is 10 and count 2. The second sum is also 10 and count 2. The last sum is 10 but count 1. We must divide by the actual count, not the nominal batch size.

Use an outer loop with start indexes 0, 2, 4. The intended exclusive end is `start + size`. If that exceeds the filtered length, reduce it to the length. The inner loop starts at start and stops before end. Each batch gets its own total initialized to zero.

```javascript
const readings = [4, -1, 6, 2, -3, 8, 10];
const valid = [];
for (let i = 0; i < readings.length; i++) {
    if (readings[i] >= 0) {
        valid.push(readings[i]);
    }
}
const size = 2;
const averages = [];
for (let start = 0; start < valid.length; start += size) {
    let end = start + size;
    if (end > valid.length) {
        end = valid.length;
    }
    let sum = 0;
    for (let i = start; i < end; i++) {
        sum += valid[i];
    }
    const count = end - start;
    averages.push(sum / count);
}
console.log(averages.join(' '));
```

Expected output:

```text
5 5 10
```

| start | end | Included indexes | sum | count |
|---|---|---|---|---|
| 0 | 2 | 0, 1 | 10 | 2 |
| 2 | 4 | 2, 3 | 10 | 2 |
| 4 | 5 | 4 | 10 | 1 |

At start 4, start + size is 6. Since 6 > 5, end becomes 5. The inner loop reads index 4 only, then i becomes 5 and the condition fails. The expression end - start gives 5 - 4 = 1. The outer update moves start to 6 and terminates. Every valid reading belongs to exactly one batch. The size is a fixed positive integer; a configurable size would require validation before entering the loop. Filtering after batching would preserve original batch boundaries, giving different groups. The total number of inner iterations is the number of valid readings, not its square.

### Problem 2: Above-average category totals

Given transactions `A: 10, B: 5, A: 20, C: 25`, group by category, calculate the average of category totals, and list categories whose total exceeds that average. Preserve first-encounter order. Expected categories: A and C. Expected average: 20. Empty input should produce null average and no categories.

#### Worked solution 2

The group totals are A = 30, B = 5, C = 25. The sum of totals is 60, and there are three distinct categories, so the average is 20. Dividing by four transactions would calculate a different statistic. Only A and C exceed 20.

We can accumulate the grand total while grouping because each transaction contributes exactly once to both its group and the grand total. But selection must wait until the final number of categories and total are known.

```javascript
const transactions = [
    {category: 'A', amount: 10}, {category: 'B', amount: 5},
    {category: 'A', amount: 20}, {category: 'C', amount: 25}
];
const totals = new Map();
const categories = [];
let grandTotal = 0;
for (let i = 0; i < transactions.length; i++) {
    const transaction = transactions[i];
    const category = transaction.category;
    if (!totals.has(category)) {
        totals.set(category, 0);
        categories.push(category);
    }
    const updated = totals.get(category) + transaction.amount;
    totals.set(category, updated);
    grandTotal += transaction.amount;
}
let average = null;
const selected = [];
if (categories.length > 0) {
    average = grandTotal / categories.length;
    for (let i = 0; i < categories.length; i++) {
        const category = categories[i];
        if (totals.get(category) > average) {
            selected.push(category);
        }
    }
}
console.log(average);
console.log(selected.join(' '));
```

Expected output:

```text
20
A C
```

For the third transaction, totals.get('A') is 10, transaction.amount is 20, and updated is 30. The grand total before that transaction is 15 and becomes 35. After the last transaction it is 60. The categories array contains each key once because push is inside the missing-key condition. Thus its length is the correct denominator. The second loop reads completed totals. Strict `>` excludes categories equal to the average. With one category, its total equals the average and none qualify. With no categories, the guard leaves average null and selected empty.

### Problem 3: Prepare and query cumulative totals

Given `[5, -2, 4, 7, -1, 3]`, retain nonnegative values, then answer inclusive range-sum queries `[0, 1]` and `[1, 3]` on the filtered array. Expected results: 9 and 14. Assume queries are valid integer indexes into the filtered array.

#### Worked solution 3

The filtered array is `[5, 4, 7, 3]`. The first query adds 5 + 4 = 9; the second adds 4 + 7 + 3 = 14. We can answer each by traversing its range, but repeated queries can benefit from prefix sums.

A prefix array starts with zero and stores the total of the first k elements at index k. For the filtered array it is `[0, 5, 9, 16, 19]`. The sum from inclusive left to right is the total of the first right + 1 elements minus the total of the first left elements. This cancels everything before the requested range.

```javascript
const values = [5, -2, 4, 7, -1, 3];
const filtered = [];
for (let i = 0; i < values.length; i++) {
    if (values[i] >= 0) {
        filtered.push(values[i]);
    }
}
const prefix = [0];
for (let i = 0; i < filtered.length; i++) {
    const previousTotal = prefix[i];
    const nextTotal = previousTotal + filtered[i];
    prefix.push(nextTotal);
}
const queries = [[0, 1], [1, 3]];
for (let i = 0; i < queries.length; i++) {
    const left = queries[i][0];
    const right = queries[i][1];
    const endTotal = prefix[right + 1];
    const beforeTotal = prefix[left];
    console.log(endTotal - beforeTotal);
}
```

Expected output:

```text
9
14
```

`queries[i][0]` reads the query array at index i, then reads its first element. For the second query, left is 1 and right is 3. `right + 1` evaluates to 4; prefix[4] is 19. prefix[1] is 5. Subtraction gives 14. Using prefix[right] would omit the final requested element. Constructing prefix sums before filtering would use the wrong index system. If the filtered data changes, the prefix array must be rebuilt or maintained by a different technique. For q queries and n original values, preparation plus queries takes O(n + q) time and O(n) auxiliary storage, under the usual constant-time array access model. For only one short query, direct traversal may be simpler.

## Summary

Multi-stage processing begins with requirements, not with a predetermined number of loops. Identify the intermediate information each operation needs. Establish its meaning and index system. Verify it before depending on it. Combine stages when their dependencies allow it, and separate them when a completed result is required. Preserve input data when required, handle empty intermediate collections, and include preparation costs when evaluating algorithms.

[Previous](27-part-03-recognizing-and-combining-loop-patterns.md) | [Complete chapter](27-solving-problems-with-loops.md) | Next: Part 5 - Evaluating Solutions (planned)
