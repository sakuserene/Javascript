# Lesson 27 - Part 3: Recognizing and Combining Loop Patterns

[Previous: Part 2](27-part-02-designing-a-loop-based-solution.md) | [Complete lesson](27-solving-problems-with-loops.md) | Next: Part 4 (planned)

## Table of contents

- [1. Recognizing the underlying operation](#1-recognizing-the-underlying-operation)
- [2. Counting and accumulation](#2-counting-and-accumulation)
- [3. Searching and selecting](#3-searching-and-selecting)
- [4. Filtering and transforming](#4-filtering-and-transforming)
- [5. Consecutive values and changing state](#5-consecutive-values-and-changing-state)
- [6. Frequency and grouping](#6-frequency-and-grouping)
- [7. Choosing a range-processing pattern](#7-choosing-a-range-processing-pattern)
- [8. Combining patterns](#8-combining-patterns)
- [9. Integrated practice and worked answers](#9-integrated-practice-and-worked-answers)
- [10. Summary](#10-summary)

## 1. Recognizing the underlying operation

A loop pattern is a recurring way of organizing iteration and state to solve a particular kind of problem. It is not a new JavaScript statement. Counting, for example, is a pattern that can be implemented with a `for` or `while` loop. The choice of loop syntax is separate from the choice of algorithm.

Consider three requests: count students scoring at least 50, count products costing less than 100, and count negative temperatures. The data and conditions differ, but the operation is identical: examine each value, decide whether it qualifies, and increase a counter when it does.

Do not choose a pattern solely from a keyword. "Find the first passing score" needs a search; "find how many scores pass" needs counting; "find the highest passing score" needs selection. Ask what information the result must contain and what information must survive between iterations.

| Requirement | Likely pattern | Persistent state |
|---|---|---|
| How many qualify? | Counting | Counter |
| What is their total? | Accumulation | Running total |
| Does any qualify? | Search | Boolean or result index |
| Which is greatest? | Selection | Best value or index |
| Return all qualifying items | Filtering | Result array |
| Convert each item | Transformation | Result array |
| How long is a consecutive run? | State tracking | Current and best lengths |
| How often does each value occur? | Frequency counting | Table of counts |
| Best total over a fixed-length range | Sliding window | Current and best totals |

These are starting points, not rigid rules. A request can require several patterns. Start with the simplest correct design; improve it only after understanding its behavior.

Learning objectives: identify the underlying operation, derive its state and boundaries, distinguish similar patterns, combine compatible operations, and verify the result on ordinary and boundary inputs. Examples assume dense arrays of finite numbers unless stated otherwise. Record examples assume the documented properties exist.

## 2. Counting and accumulation

Suppose a shop records sales amounts `[20, 0, 35, -10, 15]`. Positive amounts represent completed sales; zero and negative amounts must be ignored for this report. Find the number of completed sales and their total. Manually, the qualifying amounts are 20, 35 and 15. The count is 3 and the total is 70.

Counting answers how many, whereas accumulation answers how much. Both can begin at zero because before processing any element we have found no sales and added no money.

First isolate the decision: `amount > 0`. If `amount` is -10, the comparison is false. If it is 20, it is true. For a qualifying amount, `count++` increases the count by one, while `total += amount` adds the actual amount. These operations are different: adding the amount to the count would produce a monetary total, not a count.

```javascript
const amounts = [20, 0, 35, -10, 15];
let count = 0;
let total = 0;
for (let i = 0; i < amounts.length; i++) {
    const amount = amounts[i];
    if (amount > 0) {
        count++;
        total += amount;
    }
}
console.log(count, total);
```

Expected output:

```text
3 70
```

The loop initializes `i` to zero, the first index. `amounts.length` is 5, so `i < amounts.length` allows indexes 0 through 4. The update `i++` advances to the next element after each iteration. `amounts[i]` retrieves the value at that index; `const amount` gives it a meaningful local name. The `if` block executes only for positive amounts. `total += amount` is equivalent here to `total = total + amount`. At index 2, the old total is 20, the amount is 35, and the new total is 55.

| Index | Amount | Qualifies? | Count after | Total after |
|---|---:|---|---:|---:|
| 0 | 20 | yes | 1 | 20 |
| 1 | 0 | no | 1 | 20 |
| 2 | 35 | yes | 2 | 55 |
| 3 | -10 | no | 2 | 55 |
| 4 | 15 | yes | 3 | 70 |

The two updates share a condition but maintain independent information. An empty array leaves both variables at zero. A common mistake is placing `let total = 0` inside the loop: each iteration then discards the preceding total. Another is using `amount >= 0`, which counts zero contrary to the requirements.

### Extending the report to an average

An average divides the total by the number of values contributing to that total, not necessarily the array length. Here 70 / 3 is approximately 23.33. Dividing by 5 would incorrectly include ignored entries in the denominator. When count is zero, the average is undefined by this report's contract; use `null` to represent no average.

```javascript
const amounts = [-5, 0];
let count = 0;
let total = 0;
for (let i = 0; i < amounts.length; i++) {
    if (amounts[i] > 0) {
        count++;
        total += amounts[i];
    }
}
let average = null;
if (count > 0) {
    average = total / count;
}
console.log(average);
```

Expected output:

```text
null
```

`count > 0` protects the division. `null` is a deliberate absence marker, not a numerical zero. A valid average of zero and an unavailable average are different outcomes. The average is calculated after traversal because it depends on the final total and count.

### Practice 1

For `[12, 8, 15, 10, 4]`, count and sum values at least 10. Expected count 3, sum 37. Also consider an empty array and a value exactly equal to 10.

#### Worked answer

The phrase "at least" includes equality, so use `>=`, not `>`. Both variables begin at zero. Each qualifying value contributes one to the count and its magnitude to the sum.

```javascript
const values = [12, 8, 15, 10, 4];
let count = 0;
let sum = 0;
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value >= 10) {
        count += 1;
        sum += value;
    }
}
console.log(count, sum);
```

Expected output:

```text
3 37
```

At index 3, `value >= 10` becomes `10 >= 10`, which is true. Count changes from 2 to 3; sum changes from 27 to 37. The empty case performs no iterations and returns zero for both quantities. The loop visits each element once, so its time grows linearly with input length; it uses a fixed number of additional variables.

## 3. Searching and selecting

A search asks whether or where a qualifying element exists. Selection asks which qualifying element is best according to a comparison rule. These are related but their stopping conditions differ.

Find the first score at least 50 in `[35, 48, 62, 90]`. The answer is index 2, not index 3. Once 62 is encountered, later scores cannot change which qualifying score appeared first.

```javascript
const scores = [35, 48, 62, 90];
let foundIndex = -1;
for (let i = 0; i < scores.length; i++) {
    if (scores[i] >= 50) {
        foundIndex = i;
        break;
    }
}
console.log(foundIndex);
```

Expected output:

```text
2
```

`-1` is a failure marker because valid array indexes are nonnegative. At index 2, `scores[i] >= 50` becomes `62 >= 50`, which is true. Assignment copies the index, not the score, into `foundIndex`. `break` exits the loop immediately. If no element qualifies, the marker remains -1. Never access `scores[foundIndex]` as a successful result without first checking that a match exists.

Now change the requirement to finding the greatest score that is at least 50. Stopping at 62 would be wrong. We must compare later candidates with the best already found. We can use an index as both the absence marker and the location of the best value.

```javascript
const scores = [35, 48, 62, 90, 90];
let bestIndex = -1;
for (let i = 0; i < scores.length; i++) {
    const score = scores[i];
    if (score < 50) {
        continue;
    }
    if (bestIndex === -1 || score > scores[bestIndex]) {
        bestIndex = i;
    }
}
console.log(bestIndex, scores[bestIndex]);
```

Expected output:

```text
3 90
```

The first `if` excludes nonqualifying scores. `continue` skips the remainder of this iteration and proceeds to the `for` update. The second condition has two alternatives joined by `||` (logical OR). If `bestIndex === -1` is true, JavaScript does not evaluate the right operand; this is short-circuit evaluation. Otherwise `scores[bestIndex]` retrieves the previous best and `score > scores[bestIndex]` compares it with the current score. At index 3, the comparison is `90 > 62`, true. At index 4 it is `90 > 90`, false. Strict greater-than preserves the first occurrence in a tie; `>=` would retain the last. The displayed input guarantees a match; for general inputs guard the final lookup and report no match when the index remains -1.

### Practice 2

Find the last occurrence of the smallest value in `[7, -2, 4, -2]`. Return -1 for an empty array. Expected index 3.

#### Worked answer

We need selection, not a first-match search. Start with no selected index. Replace the selected index whenever a smaller or equal value appears. Equality must replace the previous candidate because the requirement specifies the last occurrence.

```javascript
const values = [7, -2, 4, -2];
let bestIndex = -1;
for (let i = 0; i < values.length; i++) {
    if (bestIndex === -1 || values[i] <= values[bestIndex]) {
        bestIndex = i;
    }
}
console.log(bestIndex);
```

Expected output:

```text
3
```

At index 0 the absence check is true, so the first element becomes the candidate. At index 1, `-2 <= 7` is true. At index 2, `4 <= -2` is false. At index 3, `-2 <= -2` is true, so the tie replaces the old index. An empty array never enters the loop and preserves -1. Initializing a minimum to zero would fail for arrays containing only positive values.

## 4. Filtering and transforming

Filtering changes which elements are retained. Transformation changes the representation of an element. A problem may require both.

Given temperatures in Celsius `[0, 10, -5, 20]`, retain only nonnegative readings and convert them to Fahrenheit. The conversion is `celsius * 9 / 5 + 32`. The expected array is `[32, 50, 68]`.

First decide whether a reading qualifies using `celsius >= 0`. Next convert only qualifying values. Finally append each converted value to a new array. The original array must remain unchanged.

```javascript
const readings = [0, 10, -5, 20];
const converted = [];
for (let i = 0; i < readings.length; i++) {
    const celsius = readings[i];
    if (celsius >= 0) {
        const fahrenheit = celsius * 9 / 5 + 32;
        converted.push(fahrenheit);
    }
}
console.log(converted.join(' '));
```

Expected output:

```text
32 50 68
```

`[]` creates an empty array. `const` prevents reassignment of the array variable but does not prevent adding elements. `push` appends its argument to the end of the array. `join(' ')` produces a display string with a space between elements; it does not change the array.

For Celsius 10, multiplication gives 90, division gives 18, and addition gives 50. Multiplication and division have equal precedence and are evaluated left to right here, before addition. The expression could be expanded into `const scaled = celsius * 9; const divided = scaled / 5; const fahrenheit = divided + 32;`. This is useful when learning or debugging, although the original formula is clear once understood.

Why not use `converted[i] = fahrenheit`? When an input is skipped, its index would remain missing in the output. `push` instead places the next qualifying value immediately after the previous one. The result preserves the relative order of qualifying inputs. Empty input or no qualifying readings produces an empty array.

### Practice 3

Given `[3, 4, -2, 7, 0]`, retain even values and square each retained value. Expected `[16, 4, 0]`. Do not modify the input.

#### Worked answer

An integer is even if its remainder on division by two is zero. This includes negative even integers and zero. Squaring multiplies the value by itself.

```javascript
const values = [3, 4, -2, 7, 0];
const result = [];
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value % 2 === 0) {
        const square = value * value;
        result.push(square);
    }
}
console.log(result.join(' '));
```

Expected output:

```text
16 4 0
```

For -2, `-2 % 2` is negative zero, which compares equal to zero with `===`. The condition is true. `-2 * -2` gives 4. The append operation places 4 after 16, not at the original input index. The input is only read, never assigned to. This design takes linear time and storage proportional to the number of retained values.

## 5. Consecutive values and changing state

A frequency count does not distinguish adjacent occurrences from separated ones. For example `[1, 1, 0, 1]` contains three ones, but its longest consecutive run of ones has length two. The word consecutive means uninterrupted in the original order.

Find the longest run of successful days in `[1, 1, 0, 1, 1, 1, 0]`, where 1 means success and 0 means failure. Expected length 3. We need the current run length and the best historical length. Before reading any day, both are zero. A success extends the current run by one; a failure resets it to zero because the current day is not a success. After each update compare the current length with the best.

```javascript
const days = [1, 1, 0, 1, 1, 1, 0];
let current = 0;
let best = 0;
for (let i = 0; i < days.length; i++) {
    if (days[i] === 1) {
        current++;
    } else {
        current = 0;
    }
    if (current > best) {
        best = current;
    }
}
console.log(best);
```

Expected output:

```text
3
```

| Index | Day | Current | Best |
|---|---:|---:|---:|
| 0 | 1 | 1 | 1 |
| 1 | 1 | 2 | 2 |
| 2 | 0 | 0 | 2 |
| 3 | 1 | 1 | 2 |
| 4 | 1 | 2 | 2 |
| 5 | 1 | 3 | 3 |
| 6 | 0 | 0 | 3 |

`current` represents a run ending at the current index; `best` represents the greatest length anywhere in the processed portion. Returning `current` instead of `best` would return zero for this input. Resetting `best` on failure would lose the historical record. The best value is updated after extending the run, so a run ending at the final element is recorded without special finalization.

### Finding the location as well as the length

If a run of length 3 ends at index 5, its start is index 3. Subtracting the length alone gives 2, one index too early. The correct formula is `i - current + 1`. This is because the inclusive range from start to end contains `end - start + 1` elements. Store the start when a new best is found.

```javascript
const days = [0, 1, 1, 0, 1, 1, 1];
let current = 0;
let best = 0;
let bestStart = -1;
for (let i = 0; i < days.length; i++) {
    if (days[i] === 1) {
        current++;
    } else {
        current = 0;
    }
    if (current > best) {
        best = current;
        bestStart = i - current + 1;
    }
}
console.log(bestStart, best);
```

Expected output:

```text
4 3
```

At index 6, `current` is 3. Evaluate `6 - 3` to get 3, then add 1 to get 4. The best run occupies indexes 4, 5 and 6. Strict comparison retains the earliest run when lengths tie. With no successes, best remains zero and start remains -1.

### Practice 4

Find the longest strictly decreasing contiguous sequence in `[9, 7, 7, 5, 3, 8]`. Expected length 3. Return zero for empty input.

#### Worked answer

Unlike the success-run problem, every individual number forms a valid decreasing sequence of length one. We compare adjacent values, so start at index 1. Equality breaks a strictly decreasing sequence. The second 7 begins the run `[7, 5, 3]`.

```javascript
const values = [9, 7, 7, 5, 3, 8];
let current = 0;
if (values.length > 0) {
    current = 1;
}
let best = current;
for (let i = 1; i < values.length; i++) {
    if (values[i] < values[i - 1]) {
        current++;
    } else {
        current = 1;
    }
    if (current > best) {
        best = current;
    }
}
console.log(best);
```

Expected output:

```text
3
```

At index 2, `i - 1` is 1, so the comparison is `7 < 7`, false. Reset to one because the current number begins a new sequence. At indexes 3 and 4, the comparisons `5 < 7` and `3 < 5` are true, giving lengths two and three. Index 5 resets the current length but preserves the best. Empty input initializes both lengths to zero; a single element initializes them to one and requires no comparisons.

## 6. Frequency and grouping

Suppose we need to know how many times each category appears in `['A', 'B', 'A', 'C', 'B', 'A']`. A single counter cannot retain separate totals for A, B and C. We need a collection associating each distinct category with its count.

A `Map` stores key-value associations. `new Map()` creates an empty map; `has(key)` checks whether a key exists; `get(key)` retrieves its value; `set(key, value)` creates or replaces an association. Here the key is a category and the value is a count. We will use explicit initialization rather than a compact expression.

```javascript
const categories = ['A', 'B', 'A', 'C', 'B', 'A'];
const counts = new Map();
for (let i = 0; i < categories.length; i++) {
    const category = categories[i];
    if (!counts.has(category)) {
        counts.set(category, 0);
    }
    const oldCount = counts.get(category);
    const newCount = oldCount + 1;
    counts.set(category, newCount);
}
console.log(counts.get('A'), counts.get('B'), counts.get('C'));
```

Expected output:

```text
3 2 1
```

`!` negates the Boolean result of `has`. When A first appears, `has('A')` is false, so `!false` is true and the initial count becomes zero. The next statements read zero, add one, and store one. When A appears again, initialization is skipped, the old count is one and the new count is two. Without initialization, `get` on an absent key returns `undefined`; adding one to it produces `NaN`, not one.

Grouping retains the actual elements rather than just their counts. Suppose orders have a category and amount. A map can associate each category with an array of amounts. The first occurrence creates an empty array; subsequent occurrences retrieve the existing array and append to it.

```javascript
const orders = [
    { category: 'A', amount: 20 },
    { category: 'B', amount: 5 },
    { category: 'A', amount: 30 }
];
const groups = new Map();
for (let i = 0; i < orders.length; i++) {
    const order = orders[i];
    const category = order.category;
    if (!groups.has(category)) {
        groups.set(category, []);
    }
    const group = groups.get(category);
    group.push(order.amount);
}
console.log(groups.get('A').join(' '));
console.log(groups.get('B').join(' '));
```

Expected output:

```text
20 30
5
```

Each object literal contains named properties. `order.category` reads the category and `order.amount` reads the amount. `groups.get(category)` returns the stored array itself, not a copy. Appending to `group` therefore updates the array associated with the key. The empty array is created separately for each new category; sharing one array between all categories would mix their data. If only totals are required, storing every amount is unnecessary; use a map of running totals instead.

### Practice 5

Calculate total quantities per product for records `[{id:'P', quantity:2}, {id:'Q', quantity:4}, {id:'P', quantity:3}]`. Expected P=5, Q=4.

#### Worked answer

This is grouped accumulation, not frequency counting. Each record contributes its quantity rather than one. Initialize a new product total to zero, then add the quantity.

```javascript
const records = [
    { id: 'P', quantity: 2 },
    { id: 'Q', quantity: 4 },
    { id: 'P', quantity: 3 }
];
const totals = new Map();
for (let i = 0; i < records.length; i++) {
    const record = records[i];
    const id = record.id;
    if (!totals.has(id)) {
        totals.set(id, 0);
    }
    const oldTotal = totals.get(id);
    totals.set(id, oldTotal + record.quantity);
}
console.log(totals.get('P'), totals.get('Q'));
```

Expected output:

```text
5 4
```

On the third record, `id` is P and `oldTotal` is 2. `record.quantity` is 3, so the second argument to `set` evaluates to 5. The Q association is unaffected. Empty input creates an empty map. A product with quantity zero is still recorded because it appeared in the input. Storage grows with the number of distinct products, not necessarily the number of records.

## 7. Choosing a range-processing pattern

A contiguous range consists of elements occupying consecutive indexes. Three common requests are: calculate one range total, answer many range-total queries, and find the best range of a fixed length. They suggest different patterns.

For one range, a direct loop is simple. For many arbitrary range queries over unchanged data, prefix sums avoid repeated summation. For examining all ranges of the same length in order, a sliding window can reuse the previous range total without storing a prefix array.

Consider `[4, 2, 7, 1, 5]`. Find the greatest total of three consecutive values. The ranges are `[4,2,7]` (13), `[2,7,1]` (10), and `[7,1,5]` (13). Return the earliest start when tied: index 0, total 13.

### Direct solution

```javascript
const values = [4, 2, 7, 1, 5];
const size = 3;
let bestStart = -1;
let bestTotal = 0;
for (let start = 0; start + size <= values.length; start++) {
    let total = 0;
    for (let i = start; i < start + size; i++) {
        total += values[i];
    }
    if (bestStart === -1 || total > bestTotal) {
        bestStart = start;
        bestTotal = total;
    }
}
console.log(bestStart, bestTotal);
```

Expected output:

```text
0 13
```

The outer loop chooses the starting index. `start + size` is the exclusive end. At start 2, `2 + 3 <= 5` is true, and the inner loop visits indexes 2, 3 and 4. At start 3, `3 + 3 <= 5` is false, preventing an incomplete range. Reset total inside the outer loop because each range needs its own sum. The absence marker ensures that a negative first total can still become the best. Assume size is a positive integer no larger than the array length.

### Reusing the previous total

Moving from `[4,2,7]` to `[2,7,1]` removes 4 and adds 1. The shared elements 2 and 7 need not be added again. Starting from total 13, subtract 4 to get 9, then add 1 to get 10.

```javascript
const values = [4, 2, 7, 1, 5];
const size = 3;
let total = 0;
for (let i = 0; i < size; i++) {
    total += values[i];
}
let bestTotal = total;
let bestStart = 0;
for (let right = size; right < values.length; right++) {
    const outgoingIndex = right - size;
    total -= values[outgoingIndex];
    total += values[right];
    const start = right - size + 1;
    if (total > bestTotal) {
        bestTotal = total;
        bestStart = start;
    }
}
console.log(bestStart, bestTotal);
```

Expected output:

```text
0 13
```

The first loop prepares the initial window. The second begins at index 3, the first incoming element. `right - size` gives the outgoing index: 3 - 3 = 0. `total -= values[outgoingIndex]` subtracts 4 from the old total. `total += values[right]` adds 1. The new start is `3 - 3 + 1 = 1`. The extra one distinguishes the new start from the outgoing index. At right 4, subtract 2 and add 5, producing 13 again; strict comparison keeps start 0.

The direct solution performs (n - size + 1) * size additions. The rolling solution performs size initial additions and two arithmetic updates per subsequent window. For valid size, the latter grows linearly with n and needs only a fixed number of variables. Both require validation of size before use in general-purpose code. Fixed-size rolling sums work with negative numbers too; variable-size windows that discard candidates based on sum thresholds often require nonnegative values. Floating-point rounding can also cause small differences between the two approaches.

### Practice 6

Find the largest sum of two adjacent elements in `[-8, -3, -6, -2]`. Expected -8 at start 2. Explain why initializing best to zero is wrong.

#### Worked answer

All candidate totals are negative: -11, -9 and -8. Zero is not a valid candidate. Initialize best from the first actual window. With size two, the outgoing index is `right - 2` and the new start is `right - 1`.

```javascript
const values = [-8, -3, -6, -2];
let total = values[0] + values[1];
let best = total;
let bestStart = 0;
for (let right = 2; right < values.length; right++) {
    total -= values[right - 2];
    total += values[right];
    if (total > best) {
        best = total;
        bestStart = right - 1;
    }
}
console.log(bestStart, best);
```

Expected output:

```text
2 -8
```

At right 2, subtracting -8 from -11 gives -3; adding -6 gives -9. At right 3, subtracting -3 from -9 gives -6; adding -2 gives -8. Both updates improve the best. This specialized implementation assumes at least two values. A shorter array should be rejected or return an explicit no-window result according to the contract.

## 8. Combining patterns

When several requirements refer to the same traversal, it may be possible to combine their state updates. But combining patterns is not simply placing unrelated code inside one loop. Each variable must have a clearly defined responsibility and each operation must occur at the correct time.

Consider a report for `[12, 3, 18, 10, 5]`. Qualifying values are at least 10. Return their count, total, maximum, and the qualifying values in original order. Expected count 3, total 40, maximum 18, retained values `[12,18,10]`.

Start with the shared condition `value >= 10`. Counting adds one; accumulation adds the value; filtering appends the value; selection compares with the previous maximum. Initialize maximum to `null` because no qualifying value has yet been seen. Use a guard to avoid comparing a number with the absence marker.

```javascript
const values = [12, 3, 18, 10, 5];
let count = 0;
let total = 0;
let maximum = null;
const retained = [];
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value < 10) {
        continue;
    }
    count++;
    total += value;
    retained.push(value);
    if (maximum === null || value > maximum) {
        maximum = value;
    }
}
console.log(count, total, maximum);
console.log(retained.join(' '));
```

Expected output:

```text
3 40 18
12 18 10
```

The guard condition is the opposite of the qualifying condition for finite numbers. When value is 3, `3 < 10` is true, so none of the report updates execute. When value is 18, count becomes 2, total becomes 30, the array becomes `[12,18]`, and `18 > 12` replaces the maximum. At value 10, the count and total change but maximum does not. Each variable represents a different fact about the same qualifying prefix of the input.

| Value | Count | Total | Maximum | Retained |
|---:|---:|---:|---:|---|
| 12 | 1 | 12 | 12 | 12 |
| 3 | 1 | 12 | 12 | 12 |
| 18 | 2 | 30 | 18 | 12,18 |
| 10 | 3 | 40 | 18 | 12,18,10 |
| 5 | 3 | 40 | 18 | 12,18,10 |

If no value qualifies, count and total are zero, maximum is null and retained is empty. If only count and total are required, remove the retained array and maximum state. Do not store information merely because another pattern makes it possible.

### When patterns should remain separate

Suppose the requirement changes to retaining values greater than the average of all values. The final average is unknown until the total and count are complete. Comparing each value against the running average changes the problem. For `[2, 10, 6]`, the final average is 6, so only 10 qualifies. A running average comparison could accept a value that does not exceed the final average. Use two stages: calculate the final average, then filter against it. Part 4 develops these dependencies in more detail.

## 9. Integrated practice and worked answers

Attempt the following problems before reading their solutions. Use ordinary loops, not array iteration methods. All inputs are dense and valid under the stated contracts.

### Problem A - Sales report with a first match

Given `[5, 30, 12, 30, 8]`, count and sum sales of at least 20, and report the index of the first qualifying sale. Expected count 2, total 60, first index 1. If none qualifies, return count and total zero and index -1.

### Problem B - Group totals and best product

Given product records A:3, B:5, A:4, C:2, B:1, find the product with the greatest combined quantity. Expected A with 7. On ties choose the product appearing earliest in the input. Empty input has no winning product.

### Problem C - Longest run and total successes

Given `[1,0,1,1,0,1,1,1]`, return total successes, longest consecutive success run and its starting index. Expected 6, 3, 5. If no successes, return 0, 0, -1. Preserve earliest run on ties.

### Problem D - Best qualifying adjacent pair

Given `[4, 9, 2, 8, 7]`, find the greatest sum of adjacent pairs whose two elements are both at least 4. Expected sum 15 at index 3. If no pair qualifies, return index -1 and total null.

### Worked answer A

Counting and accumulation must examine all qualifying values. The first-match search normally uses break, but here break would prevent later sales from contributing. Instead record the first index only while it is still -1.

```javascript
const sales = [5, 30, 12, 30, 8];
let count = 0;
let total = 0;
let firstIndex = -1;
for (let i = 0; i < sales.length; i++) {
    const sale = sales[i];
    if (sale >= 20) {
        count++;
        total += sale;
        if (firstIndex === -1) {
            firstIndex = i;
        }
    }
}
console.log(count, total, firstIndex);
```

Expected output:

```text
2 60 1
```

At index 1, sale is 30, so count becomes one and total becomes 30. `firstIndex === -1` is true, and assignment stores 1. At index 3, another 30 qualifies, giving count two and total 60, but the absence check is now false. This is how we preserve the first match without terminating traversal. Unqualified sales do not modify any state. With no qualifying values the initial state already satisfies the contract.

### Worked answer B

The greatest individual quantity is 5, but the greatest combined quantity is 7. We must first accumulate totals by product and then select the best final total. To preserve first appearance, keep an array of product IDs in the order they are first discovered. A map stores their totals.

```javascript
const records = [
    { id: 'A', quantity: 3 },
    { id: 'B', quantity: 5 },
    { id: 'A', quantity: 4 },
    { id: 'C', quantity: 2 },
    { id: 'B', quantity: 1 }
];
const totals = new Map();
const ids = [];
for (let i = 0; i < records.length; i++) {
    const record = records[i];
    const id = record.id;
    if (!totals.has(id)) {
        totals.set(id, 0);
        ids.push(id);
    }
    totals.set(id, totals.get(id) + record.quantity);
}
let bestId = null;
let bestTotal = null;
for (let i = 0; i < ids.length; i++) {
    const id = ids[i];
    const total = totals.get(id);
    if (bestId === null || total > bestTotal) {
        bestId = id;
        bestTotal = total;
    }
}
console.log(bestId, bestTotal);
```

Expected output:

```text
A 7
```

The first stage creates ids `[A,B,C]` and totals A=7, B=6, C=2. For the second A record, `totals.get(id) + record.quantity` evaluates as 3 + 4, so `set` replaces A's total with 7. The second stage starts with no best ID. A becomes the first candidate; neither 6 nor 2 exceeds 7. The strict comparison keeps the earliest ID if final totals tie. The IDs are assumed to be strings, so null is an unambiguous absence marker. Empty input leaves both result variables null. Two traversals here express a real dependency and remain linear in the number of records plus distinct IDs, assuming typical efficient map operations.

### Worked answer C

A total count and a current run length are different counters. A failure resets only the current run. The historical best and total successes must survive it.

```javascript
const days = [1, 0, 1, 1, 0, 1, 1, 1];
let successes = 0;
let current = 0;
let best = 0;
let bestStart = -1;
for (let i = 0; i < days.length; i++) {
    if (days[i] === 1) {
        successes++;
        current++;
    } else {
        current = 0;
    }
    if (current > best) {
        best = current;
        bestStart = i - current + 1;
    }
}
console.log(successes, best, bestStart);
```

Expected output:

```text
6 3 5
```

At index 3, successes is 3 and current is 2. The best becomes 2 and start is `3 - 2 + 1 = 2`. At index 4, current becomes zero but successes remains 3. At index 7, successes is 6, current is 3 and best becomes 3. The new start is `7 - 3 + 1 = 5`. No successes means the best update never executes, so the absence marker remains -1. The implementation uses one traversal and constant additional state.

### Worked answer D

We must examine overlapping adjacent pairs. The first pair starts at index zero and the final pair starts at length minus two. Both values must meet the threshold before computing a candidate total. The best total begins as null because no pair has qualified yet.

```javascript
const values = [4, 9, 2, 8, 7];
let bestIndex = -1;
let bestTotal = null;
for (let i = 0; i + 1 < values.length; i++) {
    const first = values[i];
    const second = values[i + 1];
    if (first >= 4 && second >= 4) {
        const total = first + second;
        if (bestIndex === -1 || total > bestTotal) {
            bestIndex = i;
            bestTotal = total;
        }
    }
}
console.log(bestIndex, bestTotal);
```

Expected output:

```text
3 15
```

`i + 1 < values.length` ensures the second index exists. At index 1, first is 9 and second is 2. `9 >= 4` is true but `2 >= 4` is false. Logical AND requires both to be true, so the pair is skipped. At index 3, 8 and 7 both qualify, and their sum 15 exceeds the previous best 13. The update stores index 3. Advancing by two instead of one would miss overlapping pairs, including the winning pair. Empty and single-element arrays never enter the loop; the initial absence markers remain unchanged.

## 10. Summary

Recognize patterns by the information a problem requires, not by superficial similarities in its wording. Identify what must persist, what resets, what qualifies, and whether later values can change the answer. Combine patterns when their requirements and update order are compatible. Separate stages when a later operation depends on a completed earlier result. Always check empty inputs, absence markers, equality boundaries, ties and the final iteration.

[Previous: Part 2](27-part-02-designing-a-loop-based-solution.md) | [Complete lesson](27-solving-problems-with-loops.md) | Next: Part 4 - Multi-Stage Processing (planned)
