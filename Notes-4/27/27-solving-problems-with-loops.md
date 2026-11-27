<a id="lesson-27"></a>
# Lesson 27 - Solving Problems With Loops

[Previous lesson](26-writing-clean-and-maintainable-loops.md)

## Master table of contents

- [Lesson 27 - Part 1: Understanding the Problem](#part-1)
  - [Contents](#part-1-contents)
  - [From words to requirements](#part-1-from-words-to-requirements)
  - [Inputs, outputs and contracts](#part-1-inputs-outputs-and-contracts)
  - [Resolving ambiguous requirements](#part-1-resolving-ambiguous-requirements)
  - [Boundaries and special cases](#part-1-boundaries-and-special-cases)
  - [Constraints and choosing an approach](#part-1-constraints-and-choosing-an-approach)
  - [Multi-stage requirements](#part-1-multi-stage-requirements)
  - [Independent practice](#part-1-independent-practice)
  - [Worked answers](#part-1-worked-answers)
  - [Summary](#part-1-summary)
- [Lesson 27 - Part 2: Designing a Loop-Based Solution](#part-2)
  - [Table of contents](#part-2-table-of-contents)
  - [From requirements to a design](#part-2-from-requirements-to-a-design)
  - [Choosing and initializing state](#part-2-choosing-and-initializing-state)
  - [Selecting boundaries and update rules](#part-2-selecting-boundaries-and-update-rules)
  - [Designing a search](#part-2-designing-a-search)
  - [Tracking changing state](#part-2-tracking-changing-state)
  - [Designing a terminating loop](#part-2-designing-a-terminating-loop)
  - [Combining state variables](#part-2-combining-state-variables)
  - [Independent practice](#part-2-independent-practice)
  - [Worked solutions](#part-2-worked-solutions)
  - [Summary](#part-2-summary)
- [Lesson 27 - Part 3: Recognizing and Combining Loop Patterns](#part-3)
  - [Table of contents](#part-3-table-of-contents)
  - [1. Recognizing the underlying operation](#part-3-1-recognizing-the-underlying-operation)
  - [2. Counting and accumulation](#part-3-2-counting-and-accumulation)
  - [3. Searching and selecting](#part-3-3-searching-and-selecting)
  - [4. Filtering and transforming](#part-3-4-filtering-and-transforming)
  - [5. Consecutive values and changing state](#part-3-5-consecutive-values-and-changing-state)
  - [6. Frequency and grouping](#part-3-6-frequency-and-grouping)
  - [7. Choosing a range-processing pattern](#part-3-7-choosing-a-range-processing-pattern)
  - [8. Combining patterns](#part-3-8-combining-patterns)
  - [9. Integrated practice and worked answers](#part-3-9-integrated-practice-and-worked-answers)
  - [10. Summary](#part-3-10-summary)
- [Lesson 27 - Part 4: Multi-Stage Processing](#part-4)
  - [Contents](#part-4-contents)
  - [Learning objectives](#part-4-learning-objectives)
  - [Understanding stages and dependencies](#part-4-understanding-stages-and-dependencies)
  - [Selecting and transforming values](#part-4-selecting-and-transforming-values)
  - [Preparing data before searching](#part-4-preparing-data-before-searching)
  - [Ranking eligible students](#part-4-ranking-eligible-students)
  - [Grouping before calculating averages](#part-4-grouping-before-calculating-averages)
  - [Combining or separating traversals](#part-4-combining-or-separating-traversals)
  - [Integrated practice](#part-4-integrated-practice)
  - [Summary](#part-4-summary)
- [Lesson 27 - Part 5: Evaluating Solutions](#part-5)
  - [Table of contents](#part-5-table-of-contents)
  - [What makes a solution suitable?](#part-5-what-makes-a-solution-suitable)
  - [Correctness and initialization](#part-5-correctness-and-initialization)
  - [Termination and progress](#part-5-termination-and-progress)
  - [Boundary conditions](#part-5-boundary-conditions)
  - [Comparing computational cost](#part-5-comparing-computational-cost)
  - [Evaluating memory and mutation](#part-5-evaluating-memory-and-mutation)
  - [Testing and verification](#part-5-testing-and-verification)
  - [Readability and maintainability](#part-5-readability-and-maintainability)
  - [Integrated practice](#part-5-integrated-practice)
  - [Summary](#part-5-summary)
- [Lesson 27 - Part 6: Independent Problem-Solving](#part-6)
  - [Contents](#part-6-contents)
  - [How to approach the problems](#part-6-how-to-approach-the-problems)
  - [Problems](#part-6-problems)
  - [Worked solutions](#part-6-worked-solutions)
  - [Independent mastery checklist](#part-6-independent-mastery-checklist)

<a id="part-1"></a>
## Lesson 27 - Part 1: Understanding the Problem

[Previous lesson](26-writing-clean-and-maintainable-loops.md) | [Next: Part 2](#part-2)

<a id="part-1-contents"></a>
### Contents

- [From words to requirements](#part-1-from-words-to-requirements)
- [Inputs, outputs and contracts](#part-1-inputs-outputs-and-contracts)
- [Resolving ambiguous requirements](#part-1-resolving-ambiguous-requirements)
- [Boundaries and special cases](#part-1-boundaries-and-special-cases)
- [Constraints and choosing an approach](#part-1-constraints-and-choosing-an-approach)
- [Multi-stage requirements](#part-1-multi-stage-requirements)
- [Independent practice](#part-1-independent-practice)
- [Worked answers](#part-1-worked-answers)

<a id="part-1-from-words-to-requirements"></a>
### From words to requirements

Before writing a loop, understand what it must accomplish. Consider the instruction: given an array of numbers, calculate the sum of all positive numbers.

Positive means strictly greater than zero. Zero is neither positive nor negative. The result is a number, not an array of selected values. For now assume a dense array of finite integers with sums within JavaScript's safe integer range. These assumptions limit the problem so that we can concentrate on its logic.

Take the input `[-3, 5, 0, 2, -1]`. Solve it manually first:

| Value | Include? | Total afterward |
|---:|---|---:|
| -3 | No | 0 |
| 5 | Yes | 5 |
| 0 | No | 5 |
| 2 | Yes | 7 |
| -1 | No | 7 |

The expected result is 7. Notice that we need to remember only the running total, not every positive value. This suggests an accumulator.

<a id="part-1-constructing-the-solution-gradually"></a>
#### Constructing the solution gradually

First establish the initial state:

```js
let total = 0;
```

`let` declares a variable whose value may change. `total` names the accumulated sum. Zero is appropriate because adding zero to a number leaves that number unchanged. Before processing any elements, the sum is zero.

Next visit each element:

```js
for (let i = 0; i < numbers.length; i++) {
    const value = numbers[i];
}
```

This is a fragment, not a standalone program. `numbers` will be defined in the complete implementation.

The header has three operations. `let i = 0` starts at the first index. `i < numbers.length` compares the current index with the number of elements; for five elements the valid indexes are 0 through 4. `i++` advances the index by one after the body completes normally. `numbers[i]` reads the element at that index. `const value` gives that element a meaningful name within the current iteration.

Now decide whether to include it:

```js
if (value > 0) {
    total += value;
}
```

The comparison `value > 0` produces a Boolean. For `value = -3`, it is false and the body is skipped. For `value = 5`, it is true. The statement `total += value` is equivalent here to `total = total + value`. JavaScript reads the old total, adds the current value and assigns the result back to total. If the old total is 5 and value is 2, the new total is 7.

Combine the stages:

```javascript
const numbers = [-3, 5, 0, 2, -1];
let total = 0;
for (let i = 0; i < numbers.length; i++) {
    const value = numbers[i];
    if (value > 0) {
        total += value;
    }
}
console.log(total);
```

Expected output:

```text
7
```

`console.log(total)` runs after the loop, so it displays the final total once. Placing it inside the loop would display intermediate totals instead. That would change the observable behavior.

<a id="part-1-similar-words-different-requirements"></a>
#### Similar words, different requirements

Counting positive numbers is not the same as summing them. The selection condition stays the same, but the update changes from `total += value` to `count++`. The latter adds one for every qualifying element, regardless of its magnitude.

```javascript
const numbers = [-3, 5, 0, 2, -1];
let count = 0;
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] > 0) {
        count++;
    }
}
console.log(count);
```

Expected output:

```text
2
```

Collecting positive numbers is a third requirement. Here we need an array, not a numeric accumulator. The established `push` method appends a value to the end of an array. Start with an empty array so that no values are included before they are examined.

```javascript
const numbers = [-3, 5, 0, 2, -1];
const positives = [];
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] > 0) {
        positives.push(numbers[i]);
    }
}
console.log(positives.join(", "));
```

Expected output:

```text
5, 2
```

`positives.push(numbers[i])` first reads the current input value, then appends it to the result. `const` prevents reassignment of the variable, not mutation of its array. `join(", ")` creates a display string with a comma and space between elements; it does not change the array.

All three implementations traverse the same input and use the same condition. Their state and updates differ because their required outputs differ.

<a id="part-1-inputs-outputs-and-contracts"></a>
### Inputs, outputs and contracts

A contract describes what a solution expects and what it promises. A precondition is a requirement that must hold before processing. A postcondition describes the result after processing. For positive summation, the precondition is a dense array of finite integers with safe intermediate sums. The postcondition is the sum of exactly those elements greater than zero. The input remains unchanged.

A contract may specify invalid input behavior too. There is a difference between assuming valid input and checking it. Neither is automatically correct: the problem must tell us which behavior is required.

<a id="part-1-example-accepting-only-valid-scores"></a>
#### Example: accepting only valid scores

Suppose a report must count integer scores between 0 and 100 inclusive. Invalid values are skipped. Given `[80, -1, 100, 101, 0, 75.5, "90"]`, the expected count is 3.

`Number.isInteger(value)` returns true for integer Number values, including zero, and false for strings, fractions, NaN and infinities. It does not convert a numeric string into a number. The range check must be separate because an integer can still be outside the permitted range.

```js
Number.isInteger(value) && value >= 0 && value <= 100
```

The `&&` operator requires every condition to be truthy. Evaluation proceeds from left to right and stops at the first falsy operand. For `"90"`, the integer check fails, so the remaining comparisons are not evaluated. For `101`, the integer check and lower bound succeed but the upper bound fails. For `0`, all three succeed.

```javascript
const scores = [80, -1, 100, 101, 0, 75.5, "90"];
let validCount = 0;
for (let i = 0; i < scores.length; i++) {
    const score = scores[i];
    const isValid = Number.isInteger(score) && score >= 0 && score <= 100;
    if (isValid) {
        validCount++;
    }
}
console.log(validCount);
```

Expected output:

```text
3
```

The Boolean variable `isValid` separates the decision from the action. It is recreated for each score, so a previous valid score cannot accidentally determine the next score's classification. `validCount` is outside the loop because it must survive across iterations.

If the requirement instead says to reject the entire input when any invalid score appears, skipping invalid values is incorrect. One possible contract returns `null` to signal rejection. A function is useful because `return` ends the entire function, unlike `break`, which only exits the loop.

```javascript
function countValidScores(scores) {
    let count = 0;
    for (let i = 0; i < scores.length; i++) {
        const score = scores[i];
        if (!Number.isInteger(score) || score < 0 || score > 100) {
            return null;
        }
        count++;
    }
    return count;
}
console.log(countValidScores([80, -1, 100]));
console.log(countValidScores([80, 100, 0]));
```

Expected output:

```text
null
3
```

`!Number.isInteger(score)` reverses the integer test. `||` means any one invalid condition is sufficient. When score is -1, the second condition is true and `return null` immediately ends the function. The partially accumulated count is not returned. If all elements are valid, execution reaches `return count` after the loop.

<a id="part-1-resolving-ambiguous-requirements"></a>
### Resolving ambiguous requirements

A problem may say "find the largest number" without specifying what to return when several elements share the largest value. Returning the value alone makes ties irrelevant. Returning an index makes ties important.

Given `[4, 9, 2, 9]`, the largest value is 9, the first largest index is 1 and the last largest index is 3. These are distinct outputs. We will assume a nonempty dense numeric array for the first implementation.

We need to store the index of the best candidate. Start with index 0 because the first element is the best among the elements examined so far. Compare each later element with the value at the stored index.

```javascript
const numbers = [4, 9, 2, 9];
let bestIndex = 0;
for (let i = 1; i < numbers.length; i++) {
    if (numbers[i] > numbers[bestIndex]) {
        bestIndex = i;
    }
}
console.log(bestIndex);
```

Expected output:

```text
1
```

Decompose `numbers[i] > numbers[bestIndex]` at index 3: `i` is 3, `bestIndex` is 1, `numbers[3]` is 9 and `numbers[1]` is 9. The comparison `9 > 9` is false. The stored index stays 1. To select the last maximum, change the comparison to `>=`, which accepts equal values too.

```javascript
const numbers = [4, 9, 2, 9];
let bestIndex = 0;
for (let i = 1; i < numbers.length; i++) {
    if (numbers[i] >= numbers[bestIndex]) {
        bestIndex = i;
    }
}
console.log(bestIndex);
```

Expected output:

```text
3
```

| Index | Value | First-maximum index | Last-maximum index |
|---:|---:|---:|---:|
| 0 | 4 | 0 | 0 |
| 1 | 9 | 1 | 1 |
| 2 | 2 | 1 | 1 |
| 3 | 9 | 1 | 3 |

Neither comparison is inherently better. The tie rule determines the correct choice. Other ambiguities include whether matching is case-sensitive, whether duplicates are retained, whether order matters, and whether modifying the input is allowed.

<a id="part-1-boundaries-and-special-cases"></a>
### Boundaries and special cases

An edge case is an input near a boundary or with an unusual structure that can expose incorrect assumptions. Consider finding the maximum value. Initializing the maximum to zero is tempting, but fails for an array containing only negative values.

```javascript
const numbers = [-8, -3, -5];
let maximum = 0;
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] > maximum) {
        maximum = numbers[i];
    }
}
console.log(maximum);
```

Expected output:

```text
0
```

This is intentionally incorrect: the required result is -3. Zero was never in the input. Every comparison is false, so the incorrect initial candidate survives. Initialize from the first actual value instead. For an empty array, explicitly return `null` because no maximum exists.

```javascript
function findMaximum(numbers) {
    if (numbers.length === 0) {
        return null;
    }
    let maximum = numbers[0];
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > maximum) {
            maximum = numbers[i];
        }
    }
    return maximum;
}
console.log(findMaximum([-8, -3, -5]));
console.log(findMaximum([]));
console.log(findMaximum([7]));
```

Expected output:

```text
-3
null
7
```

`numbers.length === 0` checks exact equality with zero. If true, return before attempting to read `numbers[0]`. Otherwise that first element is a valid candidate. Starting at index 1 avoids comparing the candidate with itself. For a singleton, `1 < 1` is false and the function returns the only value.

| Input | Expected | Reason |
|---|---|---|
| [] | null | No candidate exists |
| [7] | 7 | Only one candidate |
| [-8,-3,-5] | -3 | Negative values are valid |
| [2,9,1] | 9 | Maximum in the middle |
| [9,2,1] | 9 | Initial candidate remains |
| [1,2,9] | 9 | Final element must be examined |

A useful test set is derived from the requirements, not chosen randomly. The last case detects an accidental loop condition that excludes the last element.

<a id="part-1-constraints-and-choosing-an-approach"></a>
### Constraints and choosing an approach

Constraints describe limits or guarantees, such as sorted input, permitted memory, input size or whether mutation is allowed. They can determine which loop pattern is suitable.

Consider finding two distinct elements whose sum is 10. Given `[2, 4, 6, 8]`, both 2 + 8 and 4 + 6 qualify. Suppose the contract asks only whether any pair exists.

Without a sorting guarantee, a direct solution examines each unordered pair once. The outer index selects the first element. The inner index begins one position later so that an element cannot pair with itself and reversed duplicates are avoided.

```javascript
function hasPair(numbers, target) {
    for (let i = 0; i < numbers.length; i++) {
        for (let j = i + 1; j < numbers.length; j++) {
            if (numbers[i] + numbers[j] === target) {
                return true;
            }
        }
    }
    return false;
}
console.log(hasPair([6, 2, 8, 4], 10));
console.log(hasPair([5], 10));
console.log(hasPair([1, 2, 3], 10));
```

Expected output:

```text
true
false
false
```

At `i = 0`, `j = i + 1` evaluates to 1. The expression `numbers[i] + numbers[j] === target` first reads the two elements, adds them, then compares the sum with the target. For the first pair, 6 + 2 equals 8, so the comparison is false. At the next pair, 6 + 8 equals 14. At the third, 6 + 4 equals 10 and the function returns true. Returning false inside the inner loop would incorrectly reject the problem after the first unsuccessful pair.

If the array is guaranteed sorted in ascending order, two pointers can eliminate candidates safely. For `[2,4,6,8]` and target 12, start with the smallest and largest values. Their sum is 10. Every pair involving 2 has sum at most 10, so discard 2. The next sum is 4 + 8 = 12.

```javascript
function hasSortedPair(numbers, target) {
    let left = 0;
    let right = numbers.length - 1;
    while (left < right) {
        const sum = numbers[left] + numbers[right];
        if (sum === target) {
            return true;
        }
        if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return false;
}
console.log(hasSortedPair([2, 4, 6, 8], 12));
console.log(hasSortedPair([5], 10));
console.log(hasSortedPair([1, 2, 3], 10));
```

Expected output:

```text
true
false
false
```

`numbers.length - 1` computes the last index. `left < right` ensures distinct positions. If the sum is too small, advancing left selects a value at least as large. If too large, decreasing right selects a value at most as large. Each update eliminates one candidate and shrinks the remaining interval. The direct approach takes O(n^2) time in the worst case; the sorted approach takes O(n) time because at most n - 1 pointer movements occur. Both use constant auxiliary space. Sorting an unsorted input first has its own cost and may violate an input-preservation requirement. Do not use the sorted algorithm merely because it is faster on a different contract.

<a id="part-1-multi-stage-requirements"></a>
### Multi-stage requirements

Some tasks request several related outputs. Consider a report for integer scores from 0 through 100. Skip invalid scores, count valid scores, compute their total, and return their average or null if none qualify. Do not change the input.

For `[80, -1, 100, 0, 101]`, the valid scores are 80, 100 and 0. Count is 3, total is 180, average is 60.

Separate the responsibilities mentally: validate each score, update the count and total only when valid, then calculate the average after traversal. There is no need to build a separate filtered array because only aggregate results are required.

```javascript
function reportScores(scores) {
    let count = 0;
    let total = 0;
    for (let i = 0; i < scores.length; i++) {
        const score = scores[i];
        if (!Number.isInteger(score) || score < 0 || score > 100) {
            continue;
        }
        count++;
        total += score;
    }
    let average = null;
    if (count > 0) {
        average = total / count;
    }
    return { count: count, total: total, average: average };
}
const report = reportScores([80, -1, 100, 0, 101]);
console.log(report.count, report.total, report.average);
const empty = reportScores([-1, 101]);
console.log(empty.count, empty.total, empty.average);
```

Expected output:

```text
3 180 60
0 0 null
```

`continue` skips the remaining body and proceeds to the for-loop update. Thus neither accumulator changes for invalid scores. For a valid zero, count increases by one but total stays the same. This distinction matters: zero contributes to the denominator even though it adds nothing to the numerator.

| Score | Valid? | count | total |
|---:|---|---:|---:|
| 80 | Yes | 1 | 80 |
| -1 | No | 1 | 80 |
| 100 | Yes | 2 | 180 |
| 0 | Yes | 3 | 180 |
| 101 | No | 3 | 180 |

`total / count` divides 180 by 3. The guard `count > 0` prevents division by zero. The returned object groups the three results: each property name before a colon identifies a field, and the expression after it supplies its value. `report.count` reads the count property. The input is never assigned to or mutated.

<a id="part-1-independent-practice"></a>
### Independent practice

Attempt these before reading the answers. Assume dense arrays of finite safe integers unless specified otherwise.

1. Given `[4, -2, 0, 6, -3]`, sum only negative values. Expected: -5. Explain why the initial total is zero. Test an empty array and an array with no negative values.
2. Given `[7, 2, 2, 5]`, return the last index of the minimum, or -1 for an empty array. Expected: 2. Test `[8]` and `[3,3,3]`.
3. Count integer temperatures from -10 through 40 inclusive, skipping all invalid entries. Input: `[-11,-10,0,40,41,2.5,"20"]`. Expected: 3. Explain why checking only the range is insufficient.
4. Given `[3, 8, 8, 2, 8]`, return the number of occurrences of the maximum value. Expected: 3. Empty input returns zero. Derive a one-pass solution.
5. A report asks for the average of positive values in `[0, -2, 4, 8]`. Expected: 6. Return null if there are no positive values. Identify the error in dividing by the full array length.

<a id="part-1-worked-answers"></a>
### Worked answers

<a id="part-1-exercise-1-sum-negative-values"></a>
#### Exercise 1: sum negative values

The required output is a sum, so use an accumulator, not a counter. The qualifying condition is strictly less than zero. Zero is the identity for addition and also the specified result when no values qualify.

```javascript
function sumNegatives(numbers) {
    let total = 0;
    for (let i = 0; i < numbers.length; i++) {
        if (numbers[i] < 0) {
            total += numbers[i];
        }
    }
    return total;
}
console.log(sumNegatives([4, -2, 0, 6, -3]));
console.log(sumNegatives([]));
console.log(sumNegatives([1, 2]));
```

Expected output:

```text
-5
0
0
```

At index 1, `numbers[1] < 0` becomes `-2 < 0`, which is true. `total += numbers[1]` becomes `total = 0 + (-2)`. At index 4 the total becomes -2 + (-3) = -5. The other values do not change it. For empty input the loop never executes, so the initial total is returned.

<a id="part-1-exercise-2-last-minimum-index"></a>
#### Exercise 2: last minimum index

The output is an index, not the minimum itself. The empty case requires a sentinel of -1, which is not a valid array index. For nonempty input, start with the first element as the best candidate. Use `<=` to replace the candidate even when an equal minimum appears later.

```javascript
function lastMinimumIndex(numbers) {
    if (numbers.length === 0) {
        return -1;
    }
    let best = 0;
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] <= numbers[best]) {
            best = i;
        }
    }
    return best;
}
console.log(lastMinimumIndex([7, 2, 2, 5]));
console.log(lastMinimumIndex([]));
console.log(lastMinimumIndex([8]));
console.log(lastMinimumIndex([3, 3, 3]));
```

Expected output:

```text
2
-1
0
2
```

At index 1, `2 <= 7` is true and best becomes 1. At index 2, `2 <= 2` is true and best becomes 2. At index 3, `5 <= 2` is false. Using `<` instead would preserve the first minimum, violating the tie requirement. The guard ensures best is never initialized to a nonexistent candidate.

<a id="part-1-exercise-3-validate-temperatures"></a>
#### Exercise 3: validate temperatures

The output counts qualifying entries. The inclusive bounds permit -10 and 40. A range check alone would accept 2.5 and could coerce the string "20" during relational comparison. The integer check prevents both.

```javascript
const temperatures = [-11, -10, 0, 40, 41, 2.5, "20"];
let count = 0;
for (let i = 0; i < temperatures.length; i++) {
    const value = temperatures[i];
    if (Number.isInteger(value) && value >= -10 && value <= 40) {
        count++;
    }
}
console.log(count);
```

Expected output:

```text
3
```

For -10, the integer test is true, `-10 >= -10` is true and `-10 <= 40` is true. The count increases. For 41, the last comparison fails. For 2.5, the first condition fails and the remaining conditions are skipped. The qualifying values are -10, 0 and 40.

<a id="part-1-exercise-4-count-occurrences-of-the-maximum"></a>
#### Exercise 4: count occurrences of the maximum

We need two pieces of state: the greatest value seen and how many times that value has appeared. If a new greater value arrives, the previous count no longer describes the maximum and must be reset to one. If the value equals the current maximum, increase the count. Smaller values change neither variable.

```javascript
function countMaximum(numbers) {
    if (numbers.length === 0) {
        return 0;
    }
    let maximum = numbers[0];
    let count = 1;
    for (let i = 1; i < numbers.length; i++) {
        const value = numbers[i];
        if (value > maximum) {
            maximum = value;
            count = 1;
        } else if (value === maximum) {
            count++;
        }
    }
    return count;
}
console.log(countMaximum([3, 8, 8, 2, 8]));
console.log(countMaximum([]));
console.log(countMaximum([-2, -2, -5]));
```

Expected output:

```text
3
0
2
```

| Value | maximum | count |
|---:|---:|---:|
| 3 (initial) | 3 | 1 |
| 8 | 8 | 1 |
| 8 | 8 | 2 |
| 2 | 8 | 2 |
| 8 | 8 | 3 |

The `else if` matters: if we used two independent if statements, a newly assigned maximum would also satisfy the equality condition, counting the new value twice. A two-pass alternative first finds the maximum and then counts its occurrences. It is also linear in input size and may be easier to read, but traverses the input twice. The one-pass version maintains both facts together.

<a id="part-1-exercise-5-average-positive-values"></a>
#### Exercise 5: average positive values

The denominator must count exactly the values included in the numerator. For the given input, total is 4 + 8 = 12 and count is 2. Dividing by the array length 4 would give 3, not the requested average.

```javascript
function averagePositive(numbers) {
    let total = 0;
    let count = 0;
    for (let i = 0; i < numbers.length; i++) {
        const value = numbers[i];
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
console.log(averagePositive([0, -2, 4, 8]));
console.log(averagePositive([-2, 0]));
console.log(averagePositive([5]));
```

Expected output:

```text
6
null
5
```

Both accumulators begin at zero. At value 4, the addition produces total 4 and the increment produces count 1. At value 8, total becomes 12 and count becomes 2. The guard checks whether any qualifying value was encountered. Only after that check is division performed. This is conditional accumulation combined with conditional counting.

<a id="part-1-summary"></a>
### Summary

Understand the required output before choosing variables or loop patterns. Clarify input guarantees, tie rules, invalid-input behavior, mutation permissions and empty cases. Derive expected results manually, then use them to check the implementation. A faster algorithm is appropriate only when its assumptions match the contract. In Part 2 we will develop the design process in greater depth, including selecting state, deriving update rules and proving termination.

[Previous lesson](26-writing-clean-and-maintainable-loops.md) | [Contents](#part-1-contents) | [Next: Part 2](#part-2)

---

<a id="part-2"></a>
## Lesson 27 - Part 2: Designing a Loop-Based Solution

[Previous: Understanding the Problem](#part-1) | [[Next: Part 3](#part-3)](#part-3)

<a id="part-2-table-of-contents"></a>
### Table of contents

- [From requirements to a design](#part-2-from-requirements-to-a-design)
- [Choosing and initializing state](#part-2-choosing-and-initializing-state)
- [Selecting boundaries and update rules](#part-2-selecting-boundaries-and-update-rules)
- [Designing a search](#part-2-designing-a-search)
- [Tracking changing state](#part-2-tracking-changing-state)
- [Designing a terminating loop](#part-2-designing-a-terminating-loop)
- [Combining state variables](#part-2-combining-state-variables)
- [Independent practice](#part-2-independent-practice)
- [Worked solutions](#part-2-worked-solutions)
- [Summary](#part-2-summary)

<a id="part-2-from-requirements-to-a-design"></a>
### From requirements to a design

A problem statement describes a desired result. An algorithm describes the steps that produce that result. A loop is one way of implementing repeated steps of an algorithm.

Do not begin by asking whether to use `for` or `while`. First ask what a person would do to solve the problem manually. Then identify which actions repeat and which information must survive from one repetition to the next.

Consider a shop with sales of 12, 5, 18 and 7 units. Calculate the total number of units sold. Assume the input is a dense array of finite nonnegative integers and the total is within JavaScript's safe integer range. The empty array should produce zero.

Manually, start with nothing counted. Add 12, then 5, then 18, then 7. The intermediate totals are 12, 17, 35 and 42.

We do not need four separate variables for the intermediate totals. Once the next total is calculated, the previous total is no longer needed. One variable can hold the changing total.

```javascript
const sales = [12, 5, 18, 7];
let total = 0;
for (let i = 0; i < sales.length; i++) {
    total = total + sales[i];
}
console.log(total);
```

Expected output:

```text
42
```

Read the program in stages. `const sales` gives the input a descriptive name. `let total = 0` creates a variable whose value can change. Zero is the starting value because no sales have been counted yet. The loop header selects the indexes, while its body performs the repeated addition.

The expression `total + sales[i]` combines two operations: reading an array element and adding it to the previous total. At index 1, `sales[i]` becomes `sales[1]`, which is 5. The old total is 12. The right side becomes `12 + 5`, or 17. Assignment then stores 17 in `total`. The old value is replaced, not stored alongside the new value.

| Index | Element | Total before | Total after |
|---|---|---|---|
| 0 | 12 | 0 | 12 |
| 1 | 5 | 12 | 17 |
| 2 | 18 | 17 | 35 |
| 3 | 7 | 35 | 42 |

The shorthand `total += sales[i]` has the same effect here. Use the expanded form while learning if it helps you see the old state and new state.

The result is correct because after each iteration, total contains the sum of exactly the elements already visited. Before the loop this is the sum of zero elements. Each iteration adds one previously unvisited element. When the loop ends, all elements have been visited. This is an example of a loop invariant: a statement that remains true at a chosen point in every iteration.

<a id="part-2-choosing-and-initializing-state"></a>
### Choosing and initializing state

State is information retained by a program as it executes. A counter retains how many events occurred. An accumulator retains a combined value. A flag retains whether a condition has occurred. A pointer retains a position. Choosing state is about identifying the information needed for the next decision or the final result.

<a id="part-2-counting-is-not-summation"></a>
#### Counting is not summation

Problem: count sales of at least 10 units in `[12, 5, 18, 7]`. The expected result is 2, not 30. We need a counter, not a sum of the qualifying values.

```javascript
const sales = [12, 5, 18, 7];
let count = 0;
for (let i = 0; i < sales.length; i++) {
    const sale = sales[i];
    if (sale >= 10) {
        count = count + 1;
    }
}
console.log(count);
```

Expected output:

```text
2
```

`sale` is a temporary variable created for the current iteration. At index 2 it holds 18. `sale >= 10` evaluates to true because 18 is greater than 10. The comparison includes equality: a sale of exactly 10 also qualifies. The assignment adds one to the old count because one additional qualifying event was found. If the comparison is false, the body of the `if` is skipped and count retains its previous value.

A common error is `count += sale`. That calculates the sum of qualifying values rather than their number. Another is declaring `let count = 0` inside the loop: the counter would restart on every iteration and would not be available outside its block.

<a id="part-2-choosing-a-starting-value-for-an-extremum"></a>
#### Choosing a starting value for an extremum

Problem: find the greatest number in `[-8, -3, -12]`. Return `undefined` for an empty array. Starting the maximum at zero is wrong: zero is not in the input and no negative value can replace it.

Instead, use the first element as the first candidate. The remaining elements compete with that candidate.

```javascript
function greatest(numbers) {
    if (numbers.length === 0) {
        return undefined;
    }
    let best = numbers[0];
    for (let i = 1; i < numbers.length; i++) {
        if (numbers[i] > best) {
            best = numbers[i];
        }
    }
    return best;
}
console.log(greatest([-8, -3, -12]));
console.log(greatest([]));
console.log(greatest([4]));
```

Expected output:

```text
-3
undefined
4
```

The function parameter `numbers` receives the input array. The initial condition `numbers.length === 0` compares its length with zero using strict equality. `return undefined` ends the function before any element is read. For nonempty input, `numbers[0]` is a valid candidate. The loop starts at 1 because index 0 has already been considered.

At index 1, `numbers[i] > best` becomes `-3 > -8`, which is true. Assignment replaces -8 with -3. At index 2 the comparison is `-12 > -3`, false, so the record is preserved. With a single element the loop condition is immediately false; the initial candidate is returned.

If the problem asks for the position rather than the value, store `bestIndex` instead. Compare `numbers[i]` with `numbers[bestIndex]`, and update the index when a better value appears. This avoids maintaining two variables that could become inconsistent. Strict `>` preserves the first tied maximum; `>=` selects the last. Choose according to the requirements.

<a id="part-2-selecting-boundaries-and-update-rules"></a>
### Selecting boundaries and update rules

A boundary determines which candidates are visited. An update determines how the loop advances. Both must follow the problem, not habit.

<a id="part-2-comparing-adjacent-elements"></a>
#### Comparing adjacent elements

Problem: count how many times a temperature increases from one reading to the next. Input `[20, 22, 22, 19, 24]` should produce 2. Equal readings do not qualify. Empty and single-element inputs produce zero.

The pairs are (20,22), (22,22), (22,19), (19,24). There are four pairs, not five. Each pair has a current element and a previous element. Start the current index at 1.

```javascript
const readings = [20, 22, 22, 19, 24];
let increases = 0;
for (let i = 1; i < readings.length; i++) {
    const previous = readings[i - 1];
    const current = readings[i];
    if (current > previous) {
        increases++;
    }
}
console.log(increases);
```

Expected output:

```text
2
```

At `i = 3`, `i - 1` is 2, so `readings[i - 1]` reads 22. `readings[i]` reads 19. The condition becomes `19 > 22`, false. The count remains 1. The update `i++` advances the current index by one. It does not change any array element.

Starting at zero would read `readings[-1]`, normally undefined for an ordinary array. Using `i <= readings.length` would read one position beyond the end. Both violate the intended pair boundaries even if a particular comparison happens to produce the expected count.

An alternative design uses `i` for the left member and `i + 1` for the right member. The condition must then be `i < readings.length - 1`. For length 5, the rightmost valid left index is 3. Both designs visit the same pairs.

<a id="part-2-visiting-disjoint-pairs"></a>
#### Visiting disjoint pairs

Suppose instead that the readings are organized into nonoverlapping pairs. Calculate the sum of each complete pair in `[3, 8, 2, 4, 9]`, ignoring the unmatched final element. Expected result: `[11, 6]`.

```javascript
const values = [3, 8, 2, 4, 9];
const totals = [];
for (let i = 0; i + 1 < values.length; i += 2) {
    const pairTotal = values[i] + values[i + 1];
    totals.push(pairTotal);
}
console.log(totals.join(' '));
```

Expected output:

```text
11 6
```

The condition `i + 1 < values.length` first calculates the right member's index and then checks that it exists. At i = 4, the right index is 5; `5 < 5` is false. The body does not execute. The update `i += 2` means `i = i + 2`, selecting the next disjoint pair. An increment of one would produce overlapping pairs instead. `push` appends the calculated total to the result array; `join(' ')` produces a display string with spaces between values without modifying the array.

<a id="part-2-designing-a-search"></a>
### Designing a search

Searching requires a clear success condition and a representation of failure. Suppose we need the index of the first price greater than a budget of 50 in `[20, 50, 65, 80]`. The expected answer is 2. Return -1 if none qualifies. Since valid indexes are nonnegative, -1 is an unambiguous failure marker.

```javascript
function firstOverBudget(prices, budget) {
    for (let i = 0; i < prices.length; i++) {
        if (prices[i] > budget) {
            return i;
        }
    }
    return -1;
}
console.log(firstOverBudget([20, 50, 65, 80], 50));
console.log(firstOverBudget([20, 50], 50));
console.log(firstOverBudget([], 50));
```

Expected output:

```text
2
-1
-1
```

At index 0, 20 > 50 is false. At index 1, 50 > 50 is false because the requirement is strictly greater. At index 2, 65 > 50 is true. `return i` ends the entire function, not merely the current iteration. Index 3 is never examined. The final `return -1` is reached only if every candidate fails or there are no candidates.

If we wanted the last qualifying index, we could traverse backwards and return the first match encountered in that direction. Alternatively, scan forward and replace a stored index on each match. The forward version is useful when other information must also be collected. Early termination is appropriate only when later elements cannot affect the required result.

<a id="part-2-tracking-changing-state"></a>
### Tracking changing state

Some problems require remembering information about a sequence, not just combining independent elements. Consider daily attendance `[1, 1, 0, 1, 1, 1, 0]`, where 1 means present and 0 absent. Find the longest uninterrupted attendance streak. The answer is 3. Empty input should return zero.

Manually, the first two days form a streak of two. The absence ends that streak. The next three present days form a streak of three. We need both the current streak and the best streak ever seen. These are different: resetting the current streak must not erase the record.

```javascript
const attendance = [1, 1, 0, 1, 1, 1, 0];
let currentStreak = 0;
let bestStreak = 0;
for (let i = 0; i < attendance.length; i++) {
    if (attendance[i] === 1) {
        currentStreak++;
    } else {
        currentStreak = 0;
    }
    if (currentStreak > bestStreak) {
        bestStreak = currentStreak;
    }
}
console.log(bestStreak);
```

Expected output:

```text
3
```

Both variables begin at zero because no days have been visited. The strict comparison `attendance[i] === 1` identifies a present day. If true, increment the current streak. Otherwise, reset it to zero because an absent day is not part of a present streak. This differs from increasing-number runs, where the element that breaks a run starts a new run of length one.

| Day index | Attendance | Current streak | Best streak |
|---|---|---|---|
| 0 | 1 | 1 | 1 |
| 1 | 1 | 2 | 2 |
| 2 | 0 | 0 | 2 |
| 3 | 1 | 1 | 2 |
| 4 | 1 | 2 | 2 |
| 5 | 1 | 3 | 3 |
| 6 | 0 | 0 | 3 |

The second condition must be evaluated after the current streak is updated. Checking before the update can miss a record on the final day. Resetting `bestStreak` on an absence would forget earlier records. Updating the best after each day also handles a streak that reaches the end without requiring a special finalization step.

<a id="part-2-designing-a-terminating-loop"></a>
### Designing a terminating loop

When the number of iterations depends on changing state, a while loop can express the stopping rule directly.

Problem: a tank contains 3 liters. Add 4 liters per operation until it contains at least 14 liters. Report the number of operations and final volume. Assume exact small integer quantities and a positive addition. The final volume may exceed the target.

```javascript
let volume = 3;
const target = 14;
const addition = 4;
let operations = 0;
while (volume < target) {
    volume += addition;
    operations++;
}
console.log(operations, volume);
```

Expected output:

```text
3 15
```

`volume < target` checks whether another operation is needed. Initially `3 < 14` is true. `volume += addition` calculates `3 + 4` and stores 7. `operations++` records the completed operation. The next volumes are 11 and 15. When the condition is checked again, `15 < 14` is false. The loop ends.

| Operations | Volume | Another operation needed? |
|---|---|---|
| 0 | 3 | Yes |
| 1 | 7 | Yes |
| 2 | 11 | Yes |
| 3 | 15 | No |

Using `volume !== target` would not terminate for these values: the sequence passes 14 without reaching it. The requirement is at least, not exactly. A zero or negative addition cannot guarantee progress when the starting volume is below the target. With general numeric inputs, validate finiteness and consider precision as well; the example assumes small integers.

A termination argument identifies what changes toward the stopping condition. Here, the remaining distance `target - volume` decreases by four each iteration until it is zero or negative. This argument relies on the positive step and exact arithmetic assumptions.

<a id="part-2-combining-state-variables"></a>
### Combining state variables

Problem: for `[8, -2, 12, 0, 4]`, report the number, sum and average of strictly positive values. If none qualifies, the average should be undefined. The expected count is 3, sum 24, average 8.

First decide which values qualify. Then count them and accumulate their sum. The average is derived from these two quantities after traversal. There is no need to repeatedly calculate an average that will be replaced.

```javascript
function positiveReport(values) {
    let count = 0;
    let sum = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value > 0) {
            count++;
            sum += value;
        }
    }
    let average;
    if (count > 0) {
        average = sum / count;
    }
    return { count: count, sum: sum, average: average };
}
const report = positiveReport([8, -2, 12, 0, 4]);
console.log(report.count, report.sum, report.average);
const emptyReport = positiveReport([-2, 0]);
console.log(emptyReport.count, emptyReport.sum, emptyReport.average);
```

Expected output:

```text
3 24 8
0 0 undefined
```

`let average;` declares a variable initially holding undefined. `count > 0` ensures the division has a nonzero denominator. `sum / count` becomes `24 / 3`, or 8. Dividing by `values.length` would incorrectly include excluded values in the denominator. The returned object groups three related results: the property `count` receives the current value of the variable count, and similarly for sum and average. `report.count` reads that property.

The count and sum are updated within the same condition because they describe the same subset. If count were incremented outside the condition, the sum would still be correct but the average would be wrong. After each iteration, count and sum describe exactly the positive elements in the processed prefix. This shared invariant helps us verify that the two variables remain consistent.

<a id="part-2-independent-practice"></a>
### Independent practice

Attempt these before reading the solutions. Assume dense arrays of finite small integers unless otherwise specified.

1. Count values between 10 and 20 inclusive. Input `[9, 10, 15, 20, 21]` should return 3; an empty array should return 0. Explain the condition and initialization.
2. Find the index of the last smallest value. Input `[4, -2, 7, -2]` should return 3; empty input should return -1. Explain why the comparison includes equality.
3. Find the greatest absolute difference between adjacent values. Input `[4, 9, 2, 6]` should return 7. Arrays shorter than two elements should return 0. Explain the boundaries and absolute value operation.
4. Find how many deposits of 5 units are needed to reach at least 23 from 8. Expected answer: 3. A starting balance of 25 should require zero deposits. Explain why equality is not the stopping rule.
5. Find the longest uninterrupted sequence of values greater than 10. Input `[12, 15, 8, 11, 13, 20, 2]` should return 3. Empty input and `[10, 8]` should return zero. Explain the difference between current and best state.
6. Report the count and sum of even numbers, and the count and sum of odd numbers. Input `[2, 3, -4, 7, 0]` should give even count 3, even sum -2, odd count 2, odd sum 10. Include zero and negative integers correctly.

<a id="part-2-worked-solutions"></a>
### Worked solutions

<a id="part-2-exercise-1---inclusive-range-counting"></a>
#### Exercise 1 - Inclusive range counting

The two boundaries must both hold for the same element. Initialize a counter to zero because no qualifying elements have been seen.

```javascript
function countInRange(values) {
    let count = 0;
    for (let i = 0; i < values.length; i++) {
        const value = values[i];
        if (value >= 10 && value <= 20) {
            count++;
        }
    }
    return count;
}
console.log(countInRange([9, 10, 15, 20, 21]));
console.log(countInRange([]));
```

Expected output:

```text
3
0
```

For value 20, the left comparison `20 >= 10` is true, and the right comparison `20 <= 20` is true. Logical AND (`&&`) requires both, so count increases. For 21 the right comparison is false. For 9 the left comparison is false and JavaScript short-circuits without evaluating the right comparison. Using OR instead would accept every finite number because any number is either at least 10 or at most 20. The loop visits all elements because every qualifying element must be counted.

<a id="part-2-exercise-2---last-smallest-index"></a>
#### Exercise 2 - Last smallest index

Store an index rather than a value because the required output is a position. Start with index zero after guarding against empty input.

```javascript
function lastSmallestIndex(values) {
    if (values.length === 0) {
        return -1;
    }
    let bestIndex = 0;
    for (let i = 1; i < values.length; i++) {
        if (values[i] <= values[bestIndex]) {
            bestIndex = i;
        }
    }
    return bestIndex;
}
console.log(lastSmallestIndex([4, -2, 7, -2]));
console.log(lastSmallestIndex([]));
console.log(lastSmallestIndex([6]));
```

Expected output:

```text
3
-1
0
```

At index 1, `values[1] <= values[0]` becomes `-2 <= 4`, true. The stored index becomes 1. At index 2, `7 <= -2` is false. At index 3, `-2 <= -2` is true, so index 3 replaces index 1. The assignment stores the index, not the number -2. Using strict `<` would preserve the first minimum instead. The single-element case returns zero without entering the loop.

<a id="part-2-exercise-3---greatest-adjacent-difference"></a>
#### Exercise 3 - Greatest adjacent difference

The differences in the input are 5, -7 and 4. Their magnitudes are 5, 7 and 4. We need the greatest magnitude, not the greatest signed difference. `Math.abs(x)` returns the absolute value of x, removing a negative sign if present.

```javascript
function greatestChange(values) {
    let best = 0;
    for (let i = 1; i < values.length; i++) {
        const difference = values[i] - values[i - 1];
        const magnitude = Math.abs(difference);
        if (magnitude > best) {
            best = magnitude;
        }
    }
    return best;
}
console.log(greatestChange([4, 9, 2, 6]));
console.log(greatestChange([4]));
console.log(greatestChange([]));
```

Expected output:

```text
7
0
0
```

At index 2, the previous index is `2 - 1 = 1`. The current value is 2 and previous value is 9. Subtraction gives -7. `Math.abs(-7)` gives 7. Since 7 exceeds the previous best of 5, best becomes 7. Zero is a valid initial maximum here because magnitudes cannot be negative and the contract defines zero for no pairs. This is unlike a general maximum of signed numbers. The loop starts at 1 to guarantee a valid previous index.

<a id="part-2-exercise-4---deposits-to-a-target"></a>
#### Exercise 4 - Deposits to a target

Use a condition-controlled loop because the stopping rule depends on the balance. Count each completed deposit.

```javascript
function depositsNeeded(start) {
    let balance = start;
    let count = 0;
    while (balance < 23) {
        balance += 5;
        count++;
    }
    return count;
}
console.log(depositsNeeded(8));
console.log(depositsNeeded(25));
console.log(depositsNeeded(23));
```

Expected output:

```text
3
0
0
```

The balances are 8, 13, 18 and 23. The condition becomes false at 23. Starting at 25 or 23 makes the first condition false, so no deposit is made. A `do...while` without an initial guard would incorrectly make one deposit. Equality alone is not sufficient: a start of 9 would reach 24 after three deposits, never exactly 23. The fixed positive step guarantees progress under the stated small-integer assumptions.

<a id="part-2-exercise-5---longest-qualifying-run"></a>
#### Exercise 5 - Longest qualifying run

A qualifying value extends the current run. A nonqualifying value ends it. The best run must survive the reset.

```javascript
function longestHighRun(values) {
    let current = 0;
    let best = 0;
    for (let i = 0; i < values.length; i++) {
        if (values[i] > 10) {
            current++;
        } else {
            current = 0;
        }
        if (current > best) {
            best = current;
        }
    }
    return best;
}
console.log(longestHighRun([12, 15, 8, 11, 13, 20, 2]));
console.log(longestHighRun([]));
console.log(longestHighRun([10, 8]));
console.log(longestHighRun([11, 12]));
```

Expected output:

```text
3
0
0
2
```

| Value | Current | Best |
|---|---|---|
| 12 | 1 | 1 |
| 15 | 2 | 2 |
| 8 | 0 | 2 |
| 11 | 1 | 2 |
| 13 | 2 | 2 |
| 20 | 3 | 3 |
| 2 | 0 | 3 |

The expression `values[i] > 10` excludes exactly 10. At 8, the false branch resets current to zero; the best remains two. At 20, current increments from two to three. The second comparison becomes `3 > 2`, so best is updated. Checking after each update ensures a run ending at the last element is included. Counting all qualifying values without resetting would produce five, which is not the longest uninterrupted run.

<a id="part-2-exercise-6---partitioned-statistics"></a>
#### Exercise 6 - Partitioned statistics

Every integer belongs to exactly one of the even and odd categories. Maintain a count and sum for each category. Remainder `%` returns the remainder of division; an integer is even if its remainder on division by two is zero. Negative even integers can produce negative zero, which compares equal to zero using strict equality.

```javascript
const values = [2, 3, -4, 7, 0];
let evenCount = 0;
let evenSum = 0;
let oddCount = 0;
let oddSum = 0;
for (let i = 0; i < values.length; i++) {
    const value = values[i];
    if (value % 2 === 0) {
        evenCount++;
        evenSum += value;
    } else {
        oddCount++;
        oddSum += value;
    }
}
console.log(evenCount, evenSum);
console.log(oddCount, oddSum);
```

Expected output:

```text
3 -2
2 10
```

At value -4, the expression first calculates `-4 % 2`, which is negative zero. Comparing it with zero gives true. The even count becomes two and the even sum becomes `2 + (-4)`, or -2. At zero, the even count increases but the even sum remains unchanged. This demonstrates why count and sum are distinct. At 7, the remainder is 1, so the else branch adds one to oddCount and seven to oddSum. Avoid testing oddness with `value % 2 === 1` because negative odd integers have remainder -1. The else branch is valid here because the inputs are guaranteed integers.

As a consistency check, evenCount + oddCount equals the input length (5). The two sums add to the total of the input (8). These checks are useful but do not alone prove that every value was classified correctly.

<a id="part-2-summary"></a>
### Summary

A loop design follows from the requirements. Determine the repeated action, the information that must persist, the starting state, the candidates to visit, the update rules and the stopping condition. Trace a small input before implementing a complex solution. Check that every update preserves the intended meaning of the state and that every iteration makes appropriate progress. Select initial values according to the contract, not a memorized pattern. Separate current state from historical records. Use a clear failure representation for searches. Test empty inputs, single elements, ties and exact boundaries where applicable.

[Previous: Understanding the Problem](#part-1) | [[Next: Part 3](#part-3)](#part-3)

---

<a id="part-3"></a>
## Lesson 27 - Part 3: Recognizing and Combining Loop Patterns

[Previous: Part 2](#part-2) | [Complete lesson](#lesson-27) | [Next: Part 4](#part-4)

<a id="part-3-table-of-contents"></a>
### Table of contents

- [1. Recognizing the underlying operation](#part-3-1-recognizing-the-underlying-operation)
- [2. Counting and accumulation](#part-3-2-counting-and-accumulation)
- [3. Searching and selecting](#part-3-3-searching-and-selecting)
- [4. Filtering and transforming](#part-3-4-filtering-and-transforming)
- [5. Consecutive values and changing state](#part-3-5-consecutive-values-and-changing-state)
- [6. Frequency and grouping](#part-3-6-frequency-and-grouping)
- [7. Choosing a range-processing pattern](#part-3-7-choosing-a-range-processing-pattern)
- [8. Combining patterns](#part-3-8-combining-patterns)
- [9. Integrated practice and worked answers](#part-3-9-integrated-practice-and-worked-answers)
- [10. Summary](#part-3-10-summary)

<a id="part-3-1-recognizing-the-underlying-operation"></a>
### 1. Recognizing the underlying operation

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

<a id="part-3-2-counting-and-accumulation"></a>
### 2. Counting and accumulation

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

<a id="part-3-extending-the-report-to-an-average"></a>
#### Extending the report to an average

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

<a id="part-3-practice-1"></a>
#### Practice 1

For `[12, 8, 15, 10, 4]`, count and sum values at least 10. Expected count 3, sum 37. Also consider an empty array and a value exactly equal to 10.

<a id="part-3-worked-answer"></a>
##### Worked answer

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

<a id="part-3-3-searching-and-selecting"></a>
### 3. Searching and selecting

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

<a id="part-3-practice-2"></a>
#### Practice 2

Find the last occurrence of the smallest value in `[7, -2, 4, -2]`. Return -1 for an empty array. Expected index 3.

<a id="part-3-worked-answer-1"></a>
##### Worked answer

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

<a id="part-3-4-filtering-and-transforming"></a>
### 4. Filtering and transforming

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

<a id="part-3-practice-3"></a>
#### Practice 3

Given `[3, 4, -2, 7, 0]`, retain even values and square each retained value. Expected `[16, 4, 0]`. Do not modify the input.

<a id="part-3-worked-answer-2"></a>
##### Worked answer

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

<a id="part-3-5-consecutive-values-and-changing-state"></a>
### 5. Consecutive values and changing state

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

<a id="part-3-finding-the-location-as-well-as-the-length"></a>
#### Finding the location as well as the length

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

<a id="part-3-practice-4"></a>
#### Practice 4

Find the longest strictly decreasing contiguous sequence in `[9, 7, 7, 5, 3, 8]`. Expected length 3. Return zero for empty input.

<a id="part-3-worked-answer-3"></a>
##### Worked answer

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

<a id="part-3-6-frequency-and-grouping"></a>
### 6. Frequency and grouping

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

<a id="part-3-practice-5"></a>
#### Practice 5

Calculate total quantities per product for records `[{id:'P', quantity:2}, {id:'Q', quantity:4}, {id:'P', quantity:3}]`. Expected P=5, Q=4.

<a id="part-3-worked-answer-4"></a>
##### Worked answer

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

<a id="part-3-7-choosing-a-range-processing-pattern"></a>
### 7. Choosing a range-processing pattern

A contiguous range consists of elements occupying consecutive indexes. Three common requests are: calculate one range total, answer many range-total queries, and find the best range of a fixed length. They suggest different patterns.

For one range, a direct loop is simple. For many arbitrary range queries over unchanged data, prefix sums avoid repeated summation. For examining all ranges of the same length in order, a sliding window can reuse the previous range total without storing a prefix array.

Consider `[4, 2, 7, 1, 5]`. Find the greatest total of three consecutive values. The ranges are `[4,2,7]` (13), `[2,7,1]` (10), and `[7,1,5]` (13). Return the earliest start when tied: index 0, total 13.

<a id="part-3-direct-solution"></a>
#### Direct solution

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

<a id="part-3-reusing-the-previous-total"></a>
#### Reusing the previous total

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

<a id="part-3-practice-6"></a>
#### Practice 6

Find the largest sum of two adjacent elements in `[-8, -3, -6, -2]`. Expected -8 at start 2. Explain why initializing best to zero is wrong.

<a id="part-3-worked-answer-5"></a>
##### Worked answer

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

<a id="part-3-8-combining-patterns"></a>
### 8. Combining patterns

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

<a id="part-3-when-patterns-should-remain-separate"></a>
#### When patterns should remain separate

Suppose the requirement changes to retaining values greater than the average of all values. The final average is unknown until the total and count are complete. Comparing each value against the running average changes the problem. For `[2, 10, 6]`, the final average is 6, so only 10 qualifies. A running average comparison could accept a value that does not exceed the final average. Use two stages: calculate the final average, then filter against it. Part 4 develops these dependencies in more detail.

<a id="part-3-9-integrated-practice-and-worked-answers"></a>
### 9. Integrated practice and worked answers

Attempt the following problems before reading their solutions. Use ordinary loops, not array iteration methods. All inputs are dense and valid under the stated contracts.

<a id="part-3-problem-a---sales-report-with-a-first-match"></a>
#### Problem A - Sales report with a first match

Given `[5, 30, 12, 30, 8]`, count and sum sales of at least 20, and report the index of the first qualifying sale. Expected count 2, total 60, first index 1. If none qualifies, return count and total zero and index -1.

<a id="part-3-problem-b---group-totals-and-best-product"></a>
#### Problem B - Group totals and best product

Given product records A:3, B:5, A:4, C:2, B:1, find the product with the greatest combined quantity. Expected A with 7. On ties choose the product appearing earliest in the input. Empty input has no winning product.

<a id="part-3-problem-c---longest-run-and-total-successes"></a>
#### Problem C - Longest run and total successes

Given `[1,0,1,1,0,1,1,1]`, return total successes, longest consecutive success run and its starting index. Expected 6, 3, 5. If no successes, return 0, 0, -1. Preserve earliest run on ties.

<a id="part-3-problem-d---best-qualifying-adjacent-pair"></a>
#### Problem D - Best qualifying adjacent pair

Given `[4, 9, 2, 8, 7]`, find the greatest sum of adjacent pairs whose two elements are both at least 4. Expected sum 15 at index 3. If no pair qualifies, return index -1 and total null.

<a id="part-3-worked-answer-a"></a>
#### Worked answer A

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

<a id="part-3-worked-answer-b"></a>
#### Worked answer B

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

<a id="part-3-worked-answer-c"></a>
#### Worked answer C

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

<a id="part-3-worked-answer-d"></a>
#### Worked answer D

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

<a id="part-3-10-summary"></a>
### 10. Summary

Recognize patterns by the information a problem requires, not by superficial similarities in its wording. Identify what must persist, what resets, what qualifies, and whether later values can change the answer. Combine patterns when their requirements and update order are compatible. Separate stages when a later operation depends on a completed earlier result. Always check empty inputs, absence markers, equality boundaries, ties and the final iteration.

[Previous: Part 2](#part-2) | [Complete lesson](#lesson-27) | [Next: Part 4](#part-4)

---

<a id="part-4"></a>
## Lesson 27 - Part 4: Multi-Stage Processing

[Previous](#part-3) | [Complete chapter](#lesson-27) | [Next: Part 5](#part-5)

<a id="part-4-contents"></a>
### Contents

- [Learning objectives](#part-4-learning-objectives)
- [Understanding stages and dependencies](#part-4-understanding-stages-and-dependencies)
- [Selecting and transforming values](#part-4-selecting-and-transforming-values)
- [Preparing data before searching](#part-4-preparing-data-before-searching)
- [Ranking eligible students](#part-4-ranking-eligible-students)
- [Grouping before calculating averages](#part-4-grouping-before-calculating-averages)
- [Combining or separating traversals](#part-4-combining-or-separating-traversals)
- [Integrated practice](#part-4-integrated-practice)
- [Summary](#part-4-summary)

<a id="part-4-learning-objectives"></a>
### Learning objectives

By the end of this part, you should be able to divide a problem into stages, identify dependencies between them, design intermediate results, trace each stage independently, combine stages safely and verify the complete solution. We use plain loops, arrays, records, functions and Map as established in earlier chapters. All numeric examples assume finite numbers of ordinary magnitude unless stated otherwise.

<a id="part-4-understanding-stages-and-dependencies"></a>
### Understanding stages and dependencies

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

<a id="part-4-stage-1-select-qualifying-values"></a>
#### Stage 1: Select qualifying values

Start with `const selected = [];`. This creates an empty array that will hold only qualifying values. The binding is constant, but the array contents can change. For each index `i`, read `sales[i]` and compare it with 30. The operator `>=` includes equality, so a day with exactly 30 qualifies. `selected.push(sales[i])` reads the current value and appends it to the array.

<a id="part-4-stage-2-calculate-the-total"></a>
#### Stage 2: Calculate the total

Start `total` at zero, the additive identity. Traverse `selected`, not the original array. The expression `total += selected[i]` means `total = total + selected[i]`. On the second iteration, the previous total is 40 and the current value is 60, so the new total is 100.

<a id="part-4-stage-3-calculate-the-average"></a>
#### Stage 3: Calculate the average

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

<a id="part-4-practice-1"></a>
#### Practice 1

Given `[12, 5, 18, 7, 10]`, select values strictly greater than 10 and calculate their average. Return null if none qualify. Expected selected values: 12 and 18; expected average: 15.

<a id="part-4-worked-answer"></a>
##### Worked answer

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

<a id="part-4-selecting-and-transforming-values"></a>
### Selecting and transforming values

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

<a id="part-4-practice-2"></a>
#### Practice 2

Select temperatures above 20 Celsius from `[10, 25, 30, 20]`, then convert them to Fahrenheit using `C * 9 / 5 + 32`. Expected output: 77 and 86.

<a id="part-4-worked-answer-1"></a>
##### Worked answer

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

<a id="part-4-preparing-data-before-searching"></a>
### Preparing data before searching

Binary search depends on sorted data. If the input is unsorted, comparing the middle element with a target cannot reliably eliminate half the candidates. We can prepare the data first, then search it.

Problem: Given `[9, 2, 7, 4]`, find the index of 7 in a sorted copy without changing the original. The sorted copy is `[2, 4, 7, 9]`, so the expected index is 2. This is a sorted index, not the original index.

<a id="part-4-stage-1-copy-the-values"></a>
#### Stage 1: Copy the values

Use an empty array and append each original value. Since these are numbers, the copy contains independent numeric values. Sorting the copy does not rearrange the original.

<a id="part-4-stage-2-insertion-sort"></a>
#### Stage 2: Insertion sort

Insertion sort maintains a sorted prefix. At index `i`, save the value in `key`. Move larger preceding values one position to the right until the insertion position is found. `j` identifies the preceding candidate, initially `i - 1`.

For `[9, 2, 7, 4]`, the first key is 2. Since 9 > 2, copy 9 from index 0 to index 1. Decrease `j` to -1. Now `j + 1` is 0, the correct insertion position for 2. The intermediate array temporarily contains duplicate 9s, but the saved key prevents losing 2.

| Completed insertion | Array |
|---|---|
| key 2 | 2, 9, 7, 4 |
| key 7 | 2, 7, 9, 4 |
| key 4 | 2, 4, 7, 9 |

<a id="part-4-stage-3-binary-search"></a>
#### Stage 3: Binary search

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

<a id="part-4-practice-3"></a>
#### Practice 3

For `[8, 1, 5, 3]`, find the smallest value greater than or equal to 4, without sorting. Expected answer: 5. Return null if no value qualifies.

<a id="part-4-worked-answer-2"></a>
##### Worked answer

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

<a id="part-4-ranking-eligible-students"></a>
### Ranking eligible students

Problem: Given the records below, calculate the average score of the three highest-scoring eligible students. Eligibility means attendance of at least 75. If fewer than three students qualify, average all qualifying students. If none qualify, return null. Equal scores preserve original order. Do not reorder the original array.

| Student | Score | Attendance |
|---|---:|---:|
| A | 80 | 90 |
| B | 99 | 60 |
| C | 70 | 85 |
| D | 95 | 80 |
| E | 85 | 75 |

Manually, B is excluded. The eligible scores are 80, 70, 95, 85. In descending order they are 95, 85, 80, 70. The selected total is 260, and the average is 260 / 3.

<a id="part-4-stage-1-filter-records"></a>
#### Stage 1: Filter records

Each array element is an object with properties `name`, `score` and `attendance`. `students[i].attendance` first obtains the record at index i and then reads its attendance property. Append the record if attendance is at least 75. The new array contains references to the original records, not deep copies. Rearranging references in the new array is safe here because we do not modify any record properties.

<a id="part-4-stage-2-sort-eligible-records"></a>
#### Stage 2: Sort eligible records

Use insertion sort as above, but compare scores in descending order. The condition `eligible[j].score < key.score` means the preceding record has a lower score than the record being inserted, so it must move right. A strict comparison leaves equal scores in their original order. Changing `<` to `<=` could reverse equal-score records.

<a id="part-4-stage-3-determine-how-many-to-select"></a>
#### Stage 3: Determine how many to select

Start with `limit = 3`. If `eligible.length < limit`, assign the length to limit. This ensures that the selection loop never reads beyond the array. It is equivalent to `Math.min(3, eligible.length)`, which returns the smaller of its two arguments.

<a id="part-4-stage-4-calculate-the-result"></a>
#### Stage 4: Calculate the result

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

<a id="part-4-grouping-before-calculating-averages"></a>
### Grouping before calculating averages

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

<a id="part-4-practice-4"></a>
#### Practice 4

Given category values `X: 5, Y: 9, X: 7, Y: 1, Z: 12`, find the category with the greatest total, retaining the first category on a tie. Expected result: X, 12.

<a id="part-4-worked-answer-3"></a>
##### Worked answer

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

<a id="part-4-combining-or-separating-traversals"></a>
### Combining or separating traversals

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

<a id="part-4-integrated-practice"></a>
### Integrated practice

Attempt the following problems before reading their solutions.

<a id="part-4-problem-1-batch-quality-report"></a>
#### Problem 1: Batch quality report

Given `[4, -1, 6, 2, -3, 8, 10]`, discard negative readings, divide the remaining readings into consecutive batches of two, retain the final incomplete batch, and calculate each batch's average. Expected averages: 5, 5, 10. Empty input produces no batches. Explain why batching before filtering changes the result.

<a id="part-4-worked-solution-1"></a>
##### Worked solution 1

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

<a id="part-4-problem-2-above-average-category-totals"></a>
#### Problem 2: Above-average category totals

Given transactions `A: 10, B: 5, A: 20, C: 25`, group by category, calculate the average of category totals, and list categories whose total exceeds that average. Preserve first-encounter order. Expected categories: A and C. Expected average: 20. Empty input should produce null average and no categories.

<a id="part-4-worked-solution-2"></a>
##### Worked solution 2

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

<a id="part-4-problem-3-prepare-and-query-cumulative-totals"></a>
#### Problem 3: Prepare and query cumulative totals

Given `[5, -2, 4, 7, -1, 3]`, retain nonnegative values, then answer inclusive range-sum queries `[0, 1]` and `[1, 3]` on the filtered array. Expected results: 9 and 14. Assume queries are valid integer indexes into the filtered array.

<a id="part-4-worked-solution-3"></a>
##### Worked solution 3

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

<a id="part-4-summary"></a>
### Summary

Multi-stage processing begins with requirements, not with a predetermined number of loops. Identify the intermediate information each operation needs. Establish its meaning and index system. Verify it before depending on it. Combine stages when their dependencies allow it, and separate them when a completed result is required. Preserve input data when required, handle empty intermediate collections, and include preparation costs when evaluating algorithms.

[Previous](#part-3) | [Complete chapter](#lesson-27) | [Next: Part 5](#part-5)

---

<a id="part-5"></a>
## Lesson 27 - Part 5: Evaluating Solutions

[Previous: Part 4](#part-4) | [Lesson 27](#lesson-27) | [Next: Part 6](#part-6)

<a id="part-5-table-of-contents"></a>
### Table of contents

- [What makes a solution suitable?](#part-5-what-makes-a-solution-suitable)
- [Correctness and initialization](#part-5-correctness-and-initialization)
- [Termination and progress](#part-5-termination-and-progress)
- [Boundary conditions](#part-5-boundary-conditions)
- [Comparing computational cost](#part-5-comparing-computational-cost)
- [Evaluating memory and mutation](#part-5-evaluating-memory-and-mutation)
- [Testing and verification](#part-5-testing-and-verification)
- [Readability and maintainability](#part-5-readability-and-maintainability)
- [Integrated practice](#part-5-integrated-practice)
- [Summary](#part-5-summary)

<a id="part-5-what-makes-a-solution-suitable"></a>
### What makes a solution suitable?

A solution is suitable when it meets the requirements under the stated assumptions, terminates, and uses reasonable resources. Readability matters because a solution must often be modified later. A fast incorrect answer is not a successful optimization.

By the end of this part, you should be able to derive a correctness argument, select boundary tests, compare operation counts and storage requirements, and justify a choice between implementations. We use traditional loops and the functions, arrays, maps and testing techniques established in earlier lessons. Unless stated otherwise, numeric inputs are dense arrays of finite, small integers; arithmetic is assumed to remain within the safe integer range.

<a id="part-5-correctness-and-initialization"></a>
### Correctness and initialization

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

<a id="part-5-establishing-a-valid-initial-state"></a>
#### Establishing a valid initial state

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

<a id="part-5-why-the-correction-works-for-every-valid-input"></a>
#### Why the correction works for every valid input

A loop invariant is a statement that remains true at a chosen point in each iteration. Here, before examining index i, largest is the maximum of the already examined elements at indexes 0 through i - 1.

Initialization: before i = 1, only index 0 has been considered, so its value is the maximum of that one-element prefix.

Preservation: the next element is either greater than largest, in which case it replaces the candidate, or it is not, in which case the existing candidate remains correct. Thus the invariant holds for the expanded prefix.

Completion: when i equals numbers.length, every element has been considered. The invariant now describes the maximum of the entire array. The empty case is handled separately. This reasoning is stronger than checking only the three examples above.

<a id="part-5-practice-1---minimum-qualifying-value"></a>
#### Practice 1 - Minimum qualifying value

Find the smallest strictly positive number in [-5, 0, 8, 3, -2, 3]. Return null if none qualifies. Expected: 3. Also test [-2, 0] and [4].

<a id="part-5-worked-solution"></a>
##### Worked solution

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

<a id="part-5-termination-and-progress"></a>
### Termination and progress

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

<a id="part-5-practice-2---diagnose-a-nonterminating-loop"></a>
#### Practice 2 - Diagnose a nonterminating loop

A program should repeatedly subtract 3 from a positive balance until it becomes nonpositive. The initial balance is 10. A faulty condition is balance !== 0. Explain the defect and correct it. Expected final balance: -2 after four subtractions.

<a id="part-5-worked-solution-1"></a>
##### Worked solution

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

<a id="part-5-boundary-conditions"></a>
### Boundary conditions

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

<a id="part-5-practice-3---final-possible-position"></a>
#### Practice 3 - Final possible position

Find the first index where two adjacent values sum to 10. Return -1 if no pair exists. Test [1, 2, 4, 6], [5], and [5, 5].

<a id="part-5-worked-solution-2"></a>
##### Worked solution

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

<a id="part-5-comparing-computational-cost"></a>
### Comparing computational cost

Time complexity describes how the amount of work grows as input size grows. It is not an exact measurement in seconds. We can count a meaningful operation to understand the growth before measuring actual runtime.

Consider finding the largest sum of three consecutive elements in [4, 2, 7, 1, 5]. The possible sums are 13, 10 and 13. The answer is 13. We will compare two implementations for a general window size k, assuming 1 <= k <= numbers.length.

<a id="part-5-recalculating-every-window"></a>
#### Recalculating every window

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

<a id="part-5-reusing-the-previous-window"></a>
#### Reusing the previous window

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

<a id="part-5-practice-4---compare-search-costs"></a>
#### Practice 4 - Compare search costs

An ascending array has 1,024 distinct elements. Compare the worst-case number of examined elements for linear search and binary search. Explain when linear search might still be preferable.

<a id="part-5-worked-answer"></a>
##### Worked answer

Linear search can examine all 1,024 elements. Binary search reduces the remaining interval approximately by half at each unsuccessful comparison: 1024, 512, 256, 128, 64, 32, 16, 8, 4, 2, 1, 0. At most 11 midpoint examinations are needed for the inclusive implementation above. This counts examined elements, not individual comparison operators. Linear search is O(n); binary search is O(log n). If the array is unsorted and only one search is required, sorting it first may cost more than a direct traversal. Linear search may also be simpler for very small arrays or when the first element frequently matches.

<a id="part-5-evaluating-memory-and-mutation"></a>
### Evaluating memory and mutation

Suppose we must remove negative values from [3, -2, 0, -5, 7], retaining order. The result is [3, 0, 7]. An important requirement is whether the original array must remain unchanged.

<a id="part-5-constructing-a-separate-result"></a>
#### Constructing a separate result

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

<a id="part-5-compacting-the-original-array"></a>
#### Compacting the original array

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

<a id="part-5-practice-5---choose-an-implementation"></a>
#### Practice 5 - Choose an implementation

A reporting function receives a shared array of daily temperatures. It must return the nonnegative readings without changing the original data. Which approach is suitable? What if the array contains objects rather than numbers?

<a id="part-5-worked-answer-1"></a>
##### Worked answer

Use the separate result array. The contract explicitly prohibits mutation. With objects, pushing an object into a new array copies its reference, not the object itself. Selecting references does not modify the original objects, but changing their properties later can affect both arrays. If independent objects are required, the copying contract must specify which properties and nested data need to be copied. Do not assume a new array provides a deep copy.

<a id="part-5-testing-and-verification"></a>
### Testing and verification

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

<a id="part-5-independent-reference-implementations"></a>
#### Independent reference implementations

For an optimized algorithm, a simpler implementation can serve as a reference. For example, compare rolling window totals with direct window totals for many small arrays. This is called differential testing. It is useful because the implementations use different reasoning, but agreement alone is not proof: both may share a mistaken requirement. Include manually calculated expected results too.

<a id="part-5-practice-6---regression-test"></a>
#### Practice 6 - Regression test

A maximum function once returned zero for all-negative arrays. Choose a regression test that exposes the defect, and explain why testing [2, 4, 6] alone would not help.

<a id="part-5-worked-answer-2"></a>
##### Worked answer

Use [-8, -3, -12] with expected -3. The faulty initialization to zero prevents all updates. Positive values would replace zero and hide the defect. Preserve this test after correcting the function so later modifications cannot silently reintroduce the same error. Also test empty and single-element arrays under the stated contract.

<a id="part-5-readability-and-maintainability"></a>
### Readability and maintainability

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

<a id="part-5-integrated-practice"></a>
### Integrated practice

Attempt these problems before reading their worked solutions.

<a id="part-5-problem-1---evaluate-a-longest-run-solution"></a>
#### Problem 1 - Evaluate a longest-run solution

Find the length of the longest strictly increasing contiguous run. Return 0 for an empty array. For [2, 4, 6, 3, 5, 7, 9], expect 4. Also test [5, 5], [9, 8, 7], and []. Explain correctness and complexity.

<a id="part-5-worked-solution-3"></a>
##### Worked solution

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

<a id="part-5-problem-2---evaluate-duplicate-detection"></a>
#### Problem 2 - Evaluate duplicate detection

Return true if any two distinct indexes contain equal numbers. Compare a nested loop and a Set-based implementation. Test [4, 1, 4], [4, 1, 2] and [].

<a id="part-5-worked-solution-4"></a>
##### Worked solution

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

<a id="part-5-problem-3---evaluate-a-multi-stage-report"></a>
#### Problem 3 - Evaluate a multi-stage report

Given [10, -2, 30, 0, 20], calculate the average of strictly positive values without modifying the input. Return null if none qualify. Compare collecting values first with a single traversal. Expected average: 20.

<a id="part-5-worked-solution-5"></a>
##### Worked solution

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

<a id="part-5-summary"></a>
### Summary

Evaluate solutions against their actual contracts. Establish valid initial state, preserve the intended invariant, ensure progress, and test boundaries. Compare time and storage using explicit assumptions. Consider mutation and readability before selecting an optimization. A useful solution is one you can explain, verify and adapt, not merely one that produces an expected result once.

[Previous: Part 4](#part-4) | [Lesson 27](#lesson-27) | [Next: Part 6](#part-6)

---

<a id="part-6"></a>
## Lesson 27 - Part 6: Independent Problem-Solving

[Previous: Part 5](#part-5) | [Complete lesson](#lesson-27)

<a id="part-6-contents"></a>
### Contents

- [How to approach the problems](#part-6-how-to-approach-the-problems)
- [Problems](#part-6-problems)
- [Worked solutions](#part-6-worked-solutions)
- [Independent mastery checklist](#part-6-independent-mastery-checklist)

<a id="part-6-how-to-approach-the-problems"></a>
### How to approach the problems

Independent problem-solving does not mean inventing a new algorithm every time. It means deciding which established techniques apply, adapting them to the requirements and checking the resulting implementation. Before looking at an answer, write down the input, output, constraints and at least one manual calculation. Decide what information must survive from one iteration to the next. Give each variable a specific responsibility. Then choose boundaries and updates that make progress toward the result.

All numeric examples below assume dense arrays of finite integers, with arithmetic within the safe integer range. Records have the stated properties. We use traditional loops, not array iteration methods. The questions are separated from their answers so you can attempt them independently.

<a id="part-6-problems"></a>
### Problems

<a id="part-6-problem-1---positive-transaction-report"></a>
#### Problem 1 - Positive transaction report

Given `[-4, 10, 0, 6, -2]`, report the number, total and average of strictly positive transactions. If none qualify, return a count and total of zero and an average of `null`. Do not modify the input. Expected: count 2, total 16, average 8. Also test an empty array and `[0, -1]`.

<a id="part-6-problem-2---longest-increasing-run"></a>
#### Problem 2 - Longest increasing run

Given `[2, 4, 6, 3, 5, 7, 9]`, find the starting index and length of the longest contiguous strictly increasing run. Keep the earliest run in a tie. Return start -1 and length 0 for an empty array. Expected: start 3, length 4. For `[1, 2, 1, 2]`, expect start 0, length 2. Equal neighbors do not extend a run.

<a id="part-6-problem-3---highest-total-in-a-fixed-window"></a>
#### Problem 3 - Highest total in a fixed window

Given `[4, -2, 7, 1, 3]`, find the greatest sum of three consecutive values and its starting index. Keep the earliest tie. Assume the window size is an integer between 1 and the array length. Expected: start 2, sum 11. Also consider all-negative inputs. First derive a direct solution, then reuse overlapping totals.

<a id="part-6-problem-4---grouped-sales-report"></a>
#### Problem 4 - Grouped sales report

Given records `{item: 'pen', amount: 3}`, `{item: 'book', amount: 8}`, `{item: 'pen', amount: 5}`, `{item: 'book', amount: 2}`, calculate the total and number of transactions for each item, preserving first appearance order. Expected: pen 8 from 2, book 10 from 2. An empty input produces no groups. Amounts may be negative (refunds).

<a id="part-6-problem-5---pair-search-in-sorted-data"></a>
#### Problem 5 - Pair search in sorted data

Given sorted ascending values `[1, 3, 4, 6, 9]` and target 10, find any two distinct indexes whose values sum to the target. Return `null` if none exist. Expected valid result: indexes 0 and 4. Test `[5, 5]` with target 10 and `[5]` with target 10. Do not modify the input.

<a id="part-6-problem-6---eligible-student-ranking"></a>
#### Problem 6 - Eligible student ranking

Given records A: 70, B: 90, C: 90, D: 40, E: 80, retain scores at least 50, rank them from highest to lowest, preserve original order for ties, and calculate the average of the first three eligible students (or all eligible students if fewer). If none qualify, the average is null. Expected ranking B, C, E, A; selected average 260 / 3. Preserve the original array and records.

<a id="part-6-problem-7---range-totals-with-repeated-queries"></a>
#### Problem 7 - Range totals with repeated queries

Given `[5, 2, 8, 1, 4]`, answer inclusive index queries `[1, 3]`, `[0, 4]`, `[2, 2]`. Expected totals 11, 20, 8. Assume valid query indexes. Prepare cumulative totals once rather than traverse the requested range for each query.

<a id="part-6-problem-8---circular-batches"></a>
#### Problem 8 - Circular batches

Given `[10, 20, 30, 40, 50]`, visit exactly five elements starting at index 3, wrapping to index 0 when necessary. Divide the visited values into batches of size two, retaining a shorter final batch. Expected batches `[40, 50]`, `[10, 20]`, `[30]`. Assume a valid start and positive integer batch size. For empty input return no batches. Do not modify the input.

<a id="part-6-worked-solutions"></a>
### Worked solutions

<a id="part-6-solution-1---positive-transaction-report"></a>
#### Solution 1 - Positive transaction report

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

<a id="part-6-solution-2---longest-increasing-run"></a>
#### Solution 2 - Longest increasing run

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

<a id="part-6-solution-3---highest-total-in-a-fixed-window"></a>
#### Solution 3 - Highest total in a fixed window

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

<a id="part-6-solution-4---grouped-sales-report"></a>
#### Solution 4 - Grouped sales report

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

<a id="part-6-solution-5---pair-search-in-sorted-data"></a>
#### Solution 5 - Pair search in sorted data

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

<a id="part-6-solution-6---eligible-student-ranking"></a>
#### Solution 6 - Eligible student ranking

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

<a id="part-6-solution-7---range-totals-with-repeated-queries"></a>
#### Solution 7 - Range totals with repeated queries

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

<a id="part-6-solution-8---circular-batches"></a>
#### Solution 8 - Circular batches

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

<a id="part-6-independent-mastery-checklist"></a>
### Independent mastery checklist

Try each problem again with different inputs without reading the answer. Explain the purpose and initial value of every state variable, evaluate the central expressions using actual indexes, trace at least one complete iteration, and justify the stopping condition. Then test the smallest permitted input, ties, no-match cases and any unusual values allowed by the contract. Compare an alternative implementation and explain its trade-offs. Do not consider a problem mastered merely because you recognize the solution when reading it.

This completes the Plain Loops in JavaScript curriculum. Continue practicing with unfamiliar problems until you can derive and verify solutions independently.

[Previous: Part 5](#part-5) | [Complete lesson](#lesson-27)
