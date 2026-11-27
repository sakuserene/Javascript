# Lesson 27 - Part 5: Evaluating Solutions

[Previous: Part 4](27-part-04-multi-stage-processing.md) | [Lesson 27](27-solving-problems-with-loops.md) | Next: Part 6 (planned)

## Table of contents

- [What makes a solution suitable?](#what-makes-a-solution-suitable)
- [Correctness and initialization](#correctness-and-initialization)
- [Termination and progress](#termination-and-progress)
- [Boundary conditions](#boundary-conditions)
- [Comparing computational cost](#comparing-computational-cost)
- [Evaluating memory and mutation](#evaluating-memory-and-mutation)
- [Testing and verification](#testing-and-verification)
- [Readability and maintainability](#readability-and-maintainability)
- [Integrated practice](#integrated-practice)
- [Summary](#summary)

## What makes a solution suitable?

A solution is suitable when it meets the requirements under the stated assumptions, terminates, and uses reasonable resources. Readability matters because a solution must often be modified later. A fast incorrect answer is not a successful optimization.

By the end of this part, you should be able to derive a correctness argument, select boundary tests, compare operation counts and storage requirements, and justify a choice between implementations. We use traditional loops and the functions, arrays, maps and testing techniques established in earlier lessons. Unless stated otherwise, numeric inputs are dense arrays of finite, small integers; arithmetic is assumed to remain within the safe integer range.

## Correctness and initialization

Consider finding the largest value in [-8, -3, -12]. Manually, -3 is the answer because it is greater than both other values. A tempting implementation is:

```javascript
const numbers = [-8, -3, -12];
let largest = 0;
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] > largest) {
        largest = numbers[i];
    }
}
console.log(largest);
```

Expected output:

```text
0
```

The output is wrong. At index 0, numbers[i] is -8. The expression -8 > 0 is false. At indexes 1 and 2, -3 > 0 and -12 > 0 are also false. No assignment to largest occurs. Zero was never a valid candidate.

### Establishing a valid initial state

We need a candidate that belongs to the input. For a nonempty array, numbers[0] retrieves its first element. After selecting it, we only need to examine the remaining elements, beginning at index 1. For an empty array, our contract will return null, meaning no maximum exists.

```javascript
function maximum(numbers) {
    if (numbers.length === 0) {
        return null;
    }
    let largest = numbers[0];
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > largest) {
            largest = numbers[i];
        }
    }
    return largest;
}
console.log(maximum([-8, -3, -12]));
console.log(maximum([]));
console.log(maximum([7]));
```

Expected output:

```text
-3
null
7
```

The function parameter numbers receives the input array. numbers.length retrieves its element count. The strict equality comparison with 0 produces a Boolean. When true, return null ends the function before any indexing occurs.

The declaration let largest = numbers[0] stores the first value, -8. The loop header has three operations: initialize i to 1 once, compare i with the array length before each iteration, and increment i after each completed iteration. For i = 1, numbers[i] retrieves -3; -3 > -8 is true, so the assignment replaces -8 with -3. At i = 2, -12 > -3 is false. The candidate remains -3. At i = 3, 3 < 3 is false and the loop ends.

| i | Candidate before | Examined value | Candidate after |
|---|---:|---:|---:|
| 1 | -8 | -3 | -3 |
| 2 | -3 | -12 | -3 |

### Why the correction works for every valid input

A loop invariant is a statement that remains true at a chosen point in each iteration. Here, before examining index i, largest is the maximum of the already examined elements at indexes 0 through i - 1.

Initialization: before i = 1, only index 0 has been considered, so its value is the maximum of that one-element prefix.

Preservation: the next element is either greater than largest, in which case it replaces the candidate, or it is not, in which case the existing candidate remains correct. Thus the invariant holds for the expanded prefix.

Completion: when i equals numbers.length, every element has been considered. The invariant now describes the maximum of the entire array. The empty case is handled separately. This reasoning is stronger than checking only the three examples above.

### Practice 1 - Minimum qualifying value

Find the smallest strictly positive number in [-5, 0, 8, 3, -2, 3]. Return null if none qualifies. Expected: 3. Also test [-2, 0] and [4].

#### Worked solution

Initializing to the first element would be unsafe because it may not qualify. Use null to represent the absence of a candidate. First reject nonpositive numbers. For a qualifying value, either no candidate exists or the value must be smaller than the existing candidate.

```javascript
function smallestPositive(numbers) {
    let smallest = null;
    for (let i = 0; i < numbers.length; i++) {
        const value = numbers[i];
        if (value <= 0) {
            continue;
        }
        if (smallest === null || value < smallest) {
            smallest = value;
        }
    }
    return smallest;
}
console.log(smallestPositive([-5, 0, 8, 3, -2, 3]));
console.log(smallestPositive([-2, 0]));
console.log(smallestPositive([4]));
```

Expected output:

```text
3
null
4
```

The const value declaration retrieves one element per iteration. continue skips the remaining body and proceeds to the for loop's update. The expression smallest === null || value < smallest uses logical OR: if no candidate exists, the first comparison is true and the second is not evaluated. Otherwise the second comparison decides whether to replace the candidate. For value 8, smallest is null, so 8 is recorded. For value 3, 3 < 8 is true. The final 3 does not replace it because 3 < 3 is false. The invariant is that smallest is null when no examined value qualifies, and otherwise is the minimum qualifying value.

## Termination and progress

Correct results are useful only if execution reaches them. Consider a search for a target in a sorted array. Binary search maintains an inclusive interval [left, right] of possible indexes. If the target is not at the midpoint, we must discard the midpoint as well as the impossible side.

For [2, 4, 6, 8], target 8, initially left = 0 and right = 3. The midpoint is 1, whose value is 4. Since 4 < 8, indexes 0 and 1 cannot contain 8. The next left must be 2.

A faulty update left = mid can fail to make progress. If left = 2 and right = 3, mid is 2. Assigning left = mid leaves the interval unchanged. Repeating the same comparison forever cannot find index 3.

```javascript
function search(numbers, target) {
    let left = 0;
    let right = numbers.length - 1;
    while (left <= right) {
        const mid = left + Math.trunc((right - left) / 2);
        if (numbers[mid] === target) {
            return mid;
        }
        if (numbers[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}
console.log(search([2, 4, 6, 8], 8));
console.log(search([2, 4, 6, 8], 5));
console.log(search([], 8));
```

Expected output:

```text
3
-1
-1
```

The expression numbers.length - 1 computes the last valid index. For an empty array it is -1, so the condition 0 <= -1 is false immediately. Math.trunc removes the fractional part of a number. With left = 2 and right = 3, right - left is 1, division by 2 gives 0.5, truncation gives 0, and adding left gives 2. The midpoint is a valid integer index inside the interval.

If the midpoint value is too small, mid + 1 computes the first remaining candidate. If too large, mid - 1 computes the last remaining candidate. Equality returns the index immediately. The final -1 indicates no matching index exists. This implementation assumes ascending sorted input and permits any matching index when duplicates exist.

| left | right | mid | value | action for target 8 |
|---:|---:|---:|---:|---|
| 0 | 3 | 1 | 4 | left = 2 |
| 2 | 3 | 2 | 6 | left = 3 |
| 3 | 3 | 3 | 8 | return 3 |

The number of candidates is right - left + 1 while the interval is nonempty. Every unsuccessful iteration strictly reduces this positive integer. It cannot decrease indefinitely without reaching zero. This establishes termination. Sortedness establishes that the discarded indexes cannot contain the target.

### Practice 2 - Diagnose a nonterminating loop

A program should repeatedly subtract 3 from a positive balance until it becomes nonpositive. The initial balance is 10. A faulty condition is balance !== 0. Explain the defect and correct it. Expected final balance: -2 after four subtractions.

#### Worked solution

The sequence is 10, 7, 4, 1, -2. Zero is skipped, so a condition requiring exact equality does not represent the requirement. The loop must continue only while the balance is positive.

```javascript
let balance = 10;
let steps = 0;
while (balance > 0) {
    balance -= 3;
    steps++;
}
console.log(balance, steps);
```

Expected output:

```text
-2 4
```

balance -= 3 means balance = balance - 3. steps++ increments the counter by one after each subtraction. At balance 1 the condition is true; subtraction produces -2. At the next condition check, -2 > 0 is false. The positive fixed decrement guarantees progress for the small integer inputs in this exercise. If the requirement instead prohibits negative balances, the update must change, not just the condition.

## Boundary conditions

Suppose we count adjacent equal pairs in [4, 4, 7, 7, 7]. The pairs are (4,4), (4,7), (7,7), (7,7). Three qualify. Adjacent pairs overlap: the middle 7 belongs to two pairs. An array with n elements has n - 1 adjacent pairs when n is positive, and zero when empty.

```javascript
function equalPairs(values) {
    let count = 0;
    for (let i = 1; i < values.length; i++) {
        if (values[i] === values[i - 1]) {
            count++;
        }
    }
    return count;
}
console.log(equalPairs([4, 4, 7, 7, 7]));
console.log(equalPairs([]));
console.log(equalPairs([4]));
console.log(equalPairs([4, 4]));
```

Expected output:

```text
3
0
0
1
```

At i = 3, i - 1 is 2. values[3] and values[2] are both 7, so strict equality is true. The counter increases. Starting at 1 ensures the preceding index is valid. The exclusive upper bound prevents access at values.length. For zero or one element the initial condition is false, so the count remains zero.

A variation starting at index 0 and comparing values[i] with values[i + 1] is also correct if the condition is i < values.length - 1. Mixing the two boundary conventions can access an invalid index. Out-of-range array reads usually return undefined, which may silently hide the error rather than throw an exception.

### Practice 3 - Final possible position

Find the first index where two adjacent values sum to 10. Return -1 if no pair exists. Test [1, 2, 4, 6], [5], and [5, 5].

#### Worked solution

The last possible starting index is length - 2, because its partner is length - 1. Therefore use i < length - 1. The sum must be calculated before comparing it with 10.

```javascript
function findPair(values) {
    for (let i = 0; i < values.length - 1; i++) {
        const sum = values[i] + values[i + 1];
        if (sum === 10) {
            return i;
        }
    }
    return -1;
}
console.log(findPair([1, 2, 4, 6]));
console.log(findPair([5]));
console.log(findPair([5, 5]));
```

Expected output:

```text
2
-1
0
```

For i = 2, i + 1 is 3. The retrieved values are 4 and 6, whose sum is 10. return i ends the function with 2. For a single element, length - 1 is 0 and the loop never executes. Testing a match at the last possible position catches a common off-by-one error that a match at the beginning would not reveal.

## Comparing computational cost

Time complexity describes how the amount of work grows as input size grows. It is not an exact measurement in seconds. We can count a meaningful operation to understand the growth before measuring actual runtime.

Consider finding the largest sum of three consecutive elements in [4, 2, 7, 1, 5]. The possible sums are 13, 10 and 13. The answer is 13. We will compare two implementations for a general window size k, assuming 1 <= k <= numbers.length.

### Recalculating every window

```javascript
function direct(numbers, k) {
    let best = -Infinity;
    let additions = 0;
    for (let start = 0; start <= numbers.length - k; start++) {
        let sum = 0;
        for (let offset = 0; offset < k; offset++) {
            sum += numbers[start + offset];
            additions++;
        }
        if (sum > best) {
            best = sum;
        }
    }
    return [best, additions];
}
console.log(direct([4, 2, 7, 1, 5], 3).join(' '));
```

Expected output:

```text
13 9
```

-Infinity is the numeric value smaller than every finite number. It is a safe initial comparison value under the stated contract. start <= numbers.length - k includes the last complete window. With length 5 and k = 3, the final start is 2. The inner offset runs from 0 through 2. At start 1 and offset 2, start + offset is 3, numbers[3] is 1, and sum += 1 adds it to the current total. Resetting sum inside the outer loop prevents totals from different windows mixing. The return array contains the answer and the counted additions; join(' ') displays its elements separated by spaces.

There are n - k + 1 windows, each requiring k additions. The exact count is k * (n - k + 1). If k is fixed, growth is linear in n. If k can grow with n, the worst case is quadratic, O(n^2). Calling every nested loop quadratic without considering its bounds would be incorrect.

### Reusing the previous window

The first window contains [4, 2, 7], total 13. Moving one position removes 4 and adds 1, producing 10. Moving again removes 2 and adds 5, producing 13. Only the outgoing and incoming elements change.

```javascript
function rolling(numbers, k) {
    let sum = 0;
    let operations = 0;
    for (let i = 0; i < k; i++) {
        sum += numbers[i];
        operations++;
    }
    let best = sum;
    for (let right = k; right < numbers.length; right++) {
        sum -= numbers[right - k];
        sum += numbers[right];
        operations += 2;
        if (sum > best) {
            best = sum;
        }
    }
    return [best, operations];
}
console.log(rolling([4, 2, 7, 1, 5], 3).join(' '));
```

Expected output:

```text
13 7
```

The first loop builds a complete initial window. best starts at its actual total, which also handles all-negative inputs. The second loop begins at right = k, the first element not included in the initial window. At right = 3, right - k is 0, so numbers[0] is the outgoing value 4. Subtracting it gives 9; adding numbers[3], which is 1, gives 10. operations += 2 counts the subtraction and addition. best remains 13 because 10 > 13 is false.

| Incoming index | Outgoing index | Previous sum | After subtraction | After addition |
|---:|---:|---:|---:|---:|
| 3 | 0 | 13 | 9 | 10 |
| 4 | 1 | 10 | 8 | 13 |

The operation count is k + 2 * (n - k), which equals 2 * n - k. This is O(n) for all valid k. Both algorithms use O(1) auxiliary storage because the number of scalar variables does not grow with the input. These counts describe sum updates, not every JavaScript operation. For k = 1, the direct approach actually uses fewer sum updates; rolling is not automatically faster for every input.

### Practice 4 - Compare search costs

An ascending array has 1,024 distinct elements. Compare the worst-case number of examined elements for linear search and binary search. Explain when linear search might still be preferable.

#### Worked answer

Linear search can examine all 1,024 elements. Binary search reduces the remaining interval approximately by half at each unsuccessful comparison: 1024, 512, 256, 128, 64, 32, 16, 8, 4, 2, 1, 0. At most 11 midpoint examinations are needed for the inclusive implementation above. This counts examined elements, not individual comparison operators. Linear search is O(n); binary search is O(log n). If the array is unsorted and only one search is required, sorting it first may cost more than a direct traversal. Linear search may also be simpler for very small arrays or when the first element frequently matches.

## Evaluating memory and mutation

Suppose we must remove negative values from [3, -2, 0, -5, 7], retaining order. The result is [3, 0, 7]. An important requirement is whether the original array must remain unchanged.

### Constructing a separate result

```javascript
const values = [3, -2, 0, -5, 7];
const retained = [];
for (let i = 0; i < values.length; i++) {
    if (values[i] >= 0) {
        retained.push(values[i]);
    }
}
console.log(retained.join(' '));
console.log(values.join(' '));
```

Expected output:

```text
3 0 7
3 -2 0 -5 7
```

The comparison includes zero because the requirement removes negative values, not nonpositive values. push appends the qualifying value to retained. The input is only read, so it remains unchanged. The result requires O(n) storage in the worst case, when every element qualifies. Excluding required output storage, the auxiliary working space is O(1).

### Compacting the original array

If mutation is explicitly allowed, a write index can track the next position for a retained element. The read index examines every original element. The write index advances only when an element qualifies.

```javascript
const values = [3, -2, 0, -5, 7];
let write = 0;
for (let read = 0; read < values.length; read++) {
    const value = values[read];
    if (value >= 0) {
        values[write] = value;
        write++;
    }
}
values.length = write;
console.log(values.join(' '));
```

Expected output:

```text
3 0 7
```

At read = 2, value is 0 and write is 1. values[write] = value assigns 0 to index 1, replacing -2. write++ changes write to 2. Because write never exceeds read before the assignment, no unread element is overwritten. At read = 4, 7 is written to index 2. The final write value is 3, the number of retained elements. Setting values.length to 3 removes the trailing elements. Doing this inside the loop would shorten its boundary prematurely.

| read | value | write before | action | write after |
|---:|---:|---:|---|---:|
| 0 | 3 | 0 | write index 0 | 1 |
| 1 | -2 | 1 | skip | 1 |
| 2 | 0 | 1 | write index 1 | 2 |
| 3 | -5 | 2 | skip | 2 |
| 4 | 7 | 2 | write index 2 | 3 |

Both approaches traverse n elements. Compaction uses constant auxiliary storage and does not allocate a separate result array. However, any other variable referencing the original array observes the changes. If the contract requires preservation, compaction is incorrect regardless of its memory advantage.

### Practice 5 - Choose an implementation

A reporting function receives a shared array of daily temperatures. It must return the nonnegative readings without changing the original data. Which approach is suitable? What if the array contains objects rather than numbers?

#### Worked answer

Use the separate result array. The contract explicitly prohibits mutation. With objects, pushing an object into a new array copies its reference, not the object itself. Selecting references does not modify the original objects, but changing their properties later can affect both arrays. If independent objects are required, the copying contract must specify which properties and nested data need to be copied. Do not assume a new array provides a deep copy.

## Testing and verification

A test compares actual behavior with independently determined expected behavior. A useful collection of tests covers distinct requirements, not merely many similar inputs.

Consider a function counting values in the inclusive interval [low, high]. Assume low <= high. For [1, 3, 5, 7], low 3 and high 5, the expected count is 2. Both endpoints qualify.

```javascript
function countRange(values, low, high) {
    let count = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value >= low && value <= high) {
            count++;
        }
    }
    return count;
}
const cases = [
    { values: [1, 3, 5, 7], low: 3, high: 5, expected: 2 },
    { values: [], low: 3, high: 5, expected: 0 },
    { values: [3, 3, 3], low: 3, high: 3, expected: 3 },
    { values: [-4, -2, 0], low: -4, high: -2, expected: 2 },
    { values: [1, 9], low: 3, high: 5, expected: 0 }
];
for (let i = 0; i < cases.length; i++) {
    const test = cases[i];
    const actual = countRange(test.values, test.low, test.high);
    if (actual !== test.expected) {
        throw new Error('Failed case ' + i);
    }
}
console.log('All 5 tests passed');
```

Expected output:

```text
All 5 tests passed
```

The condition has two comparisons connected by logical AND. For value 3, low 3 and high 5, 3 >= 3 is true and 3 <= 5 is true, so the counter increases. For value 7 the second comparison is false. Both comparisons must be true.

Each test object stores the input array, lower bound, upper bound and independently calculated expected result. cases[i] retrieves one test object. The property accesses test.values, test.low and test.high supply arguments to countRange. actual !== test.expected compares the result with the expected number. new Error constructs an error object with a message; throw interrupts normal execution. The message concatenates a string and the index so a failing case can be located. The success message is reached only if all five comparisons pass.

The equal-bound test detects accidental exclusive comparisons. The negative test detects assumptions that all values are positive. The empty test verifies initialization. The no-match test verifies that nonqualifying values do not increase the counter.

### Independent reference implementations

For an optimized algorithm, a simpler implementation can serve as a reference. For example, compare rolling window totals with direct window totals for many small arrays. This is called differential testing. It is useful because the implementations use different reasoning, but agreement alone is not proof: both may share a mistaken requirement. Include manually calculated expected results too.

### Practice 6 - Regression test

A maximum function once returned zero for all-negative arrays. Choose a regression test that exposes the defect, and explain why testing [2, 4, 6] alone would not help.

#### Worked answer

Use [-8, -3, -12] with expected -3. The faulty initialization to zero prevents all updates. Positive values would replace zero and hide the defect. Preserve this test after correcting the function so later modifications cannot silently reintroduce the same error. Also test empty and single-element arrays under the stated contract.

## Readability and maintainability

Consider counting passing scores, where a passing score is at least 50. The following two implementations are equivalent for numeric scores.

```javascript
const scores = [40, 50, 80, 49];
let c = 0;
for (let i = 0; i < scores.length; i++) {
    c += scores[i] >= 50 ? 1 : 0;
}
console.log(c);
let passingCount = 0;
const passMark = 50;
for (let i = 0; i < scores.length; i++) {
    if (scores[i] >= passMark) {
        passingCount++;
    }
}
console.log(passingCount);
```

Expected output:

```text
2
2
```

The conditional operator has the form condition ? valueIfTrue : valueIfFalse. At i = 1, scores[1] is 50, 50 >= 50 is true, and the selected value is 1. c += 1 increases the counter. At i = 0 the selected value is 0. The second version expresses the same decision using an if statement. passingCount communicates what is counted, and passMark names the threshold. Both perform one traversal and use constant auxiliary space. The clearer version is preferable when the rule may change or when the audience is still learning. Concision is not inherently wrong; it must not conceal the intended behavior.

## Integrated practice

Attempt these problems before reading their worked solutions.

### Problem 1 - Evaluate a longest-run solution

Find the length of the longest strictly increasing contiguous run. Return 0 for an empty array. For [2, 4, 6, 3, 5, 7, 9], expect 4. Also test [5, 5], [9, 8, 7], and []. Explain correctness and complexity.

#### Worked solution

Manually, the first run is [2, 4, 6] and the second is [3, 5, 7, 9]. A run must be contiguous, so we cannot skip a value to extend it. Store the length of the current run and the greatest length observed. For a nonempty input both begin at 1. Compare each element with its predecessor. A greater value extends the current run; any other value starts a new run of length 1.

```javascript
function longestRun(numbers) {
    if (numbers.length === 0) {
        return 0;
    }
    let currentLength = 1;
    let longestLength = 1;
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > numbers[i - 1]) {
            currentLength++;
        } else {
            currentLength = 1;
        }
        if (currentLength > longestLength) {
            longestLength = currentLength;
        }
    }
    return longestLength;
}
console.log(longestRun([2, 4, 6, 3, 5, 7, 9]));
console.log(longestRun([5, 5]));
console.log(longestRun([9, 8, 7]));
console.log(longestRun([]));
```

Expected output:

```text
4
1
1
0
```

At i = 2, i - 1 evaluates to 1. numbers[2] is 6 and numbers[1] is 4. Since 6 > 4, currentLength increases from 2 to 3. The second comparison 3 > 2 updates longestLength to 3. At i = 3, 3 > 6 is false, so currentLength becomes 1; the historical maximum stays 3. The reset is to 1 because the current element already belongs to a new run. Resetting to zero would undercount that run.

| i | currentLength | longestLength |
|---:|---:|---:|
| 1 | 2 | 2 |
| 2 | 3 | 3 |
| 3 | 1 | 3 |
| 4 | 2 | 3 |
| 5 | 3 | 3 |
| 6 | 4 | 4 |

After each iteration, currentLength describes the increasing run ending at i and longestLength describes the greatest run in the examined prefix. The comparison and reset preserve these statements. At termination the prefix is the whole array. The loop makes n - 1 comparisons of adjacent values for nonempty input, so time is O(n) and auxiliary space O(1). Equality must break the run because the requirement is strictly increasing.

### Problem 2 - Evaluate duplicate detection

Return true if any two distinct indexes contain equal numbers. Compare a nested loop and a Set-based implementation. Test [4, 1, 4], [4, 1, 2] and [].

#### Worked solution

The direct approach compares every unordered pair until a match is found. For index i, start j at i + 1 to avoid comparing an element with itself and to avoid repeating earlier pairs.

```javascript
function directDuplicate(values) {
    for (let i = 0; i < values.length; i++) {
        for (let j = i + 1; j < values.length; j++) {
            if (values[i] === values[j]) {
                return true;
            }
        }
    }
    return false;
}
function indexedDuplicate(values) {
    const seen = new Set();
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (seen.has(value)) {
            return true;
        }
        seen.add(value);
    }
    return false;
}
const cases = [[4, 1, 4], [4, 1, 2], []];
for (let i = 0; i < cases.length; i++) {
    console.log(directDuplicate(cases[i]), indexedDuplicate(cases[i]));
}
```

Expected output:

```text
true true
false false
false false
```

In the direct function, at i = 0 and j = 1, 4 === 1 is false. At j = 2, 4 === 4 is true and return ends both loops and the function. If no pair matches, false is returned only after all pairs have been examined. The maximum comparison count is n * (n - 1) / 2, giving O(n^2) time and O(1) auxiliary space.

The second function creates an empty Set, a collection of unique values. has(value) returns whether a value is already present; add(value) records it. At index 0, seen.has(4) is false, so 4 is added. At index 1, 1 is added. At index 2, seen.has(4) is true. Checking before adding is essential: adding first would make every subsequent membership check true even for the first element.

Before each iteration, seen contains the distinct values already examined. A successful membership check therefore proves an earlier index contains the same value. With ordinary hash-based Set implementations, membership and insertion are typically expected constant time, yielding expected O(n) time and O(n) auxiliary space. JavaScript does not guarantee constant-time operations for every implementation. The stated finite-number contract also avoids the difference between strict equality and Set's treatment of NaN.

For small inputs or strict memory limits, the direct approach may be reasonable. For large inputs with available memory, Set is generally preferable. Neither approach modifies the array.

### Problem 3 - Evaluate a multi-stage report

Given [10, -2, 30, 0, 20], calculate the average of strictly positive values without modifying the input. Return null if none qualify. Compare collecting values first with a single traversal. Expected average: 20.

#### Worked solution

The positive values are 10, 30 and 20. Their sum is 60 and their count is 3. Dividing gives 20. A separate collection is useful if later processing needs the individual positive values, but this requirement only needs the sum and count. We can maintain those two variables directly.

```javascript
function positiveAverage(values) {
    let total = 0;
    let count = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value > 0) {
            total += value;
            count++;
        }
    }
    if (count === 0) {
        return null;
    }
    return total / count;
}
console.log(positiveAverage([10, -2, 30, 0, 20]));
console.log(positiveAverage([-2, 0]));
console.log(positiveAverage([8]));
```

Expected output:

```text
20
null
8
```

Both state variables begin at zero because no values have been processed. At index 0, value > 0 is true. total += value is equivalent to total = total + value, giving 10, and count++ gives 1. At index 1, -2 > 0 is false, so neither variable changes. At index 2, total becomes 40 and count becomes 2. Zero is skipped. At index 4, total becomes 60 and count becomes 3. The final guard checks for zero qualifying values before division. total / count then computes 60 / 3. Dividing by values.length instead would incorrectly include rejected values in the denominator.

The invariant is that total and count describe precisely the positive values in the processed prefix. Both updates occur under the same condition, preserving their relationship. Time is O(n) and auxiliary space is O(1). Collecting first also takes O(n) time but requires O(n) worst-case storage for the intermediate array. The one-pass approach is appropriate here because no later stage needs the selected values themselves.

## Summary

Evaluate solutions against their actual contracts. Establish valid initial state, preserve the intended invariant, ensure progress, and test boundaries. Compare time and storage using explicit assumptions. Consider mutation and readability before selecting an optimization. A useful solution is one you can explain, verify and adapt, not merely one that produces an expected result once.

[Previous: Part 4](27-part-04-multi-stage-processing.md) | [Lesson 27](27-solving-problems-with-loops.md) | Next: Part 6 (planned)
