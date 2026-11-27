# Lesson 27 - Part 1: Understanding the Problem

[Previous lesson](26-writing-clean-and-maintainable-loops.md) | Next: Part 2 - Designing a Loop-Based Solution (planned)

## Contents

- [From words to requirements](#from-words-to-requirements)
- [Inputs, outputs and contracts](#inputs-outputs-and-contracts)
- [Resolving ambiguous requirements](#resolving-ambiguous-requirements)
- [Boundaries and special cases](#boundaries-and-special-cases)
- [Constraints and choosing an approach](#constraints-and-choosing-an-approach)
- [Multi-stage requirements](#multi-stage-requirements)
- [Independent practice](#independent-practice)
- [Worked answers](#worked-answers)

## From words to requirements

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

### Constructing the solution gradually

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

### Similar words, different requirements

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

## Inputs, outputs and contracts

A contract describes what a solution expects and what it promises. A precondition is a requirement that must hold before processing. A postcondition describes the result after processing. For positive summation, the precondition is a dense array of finite integers with safe intermediate sums. The postcondition is the sum of exactly those elements greater than zero. The input remains unchanged.

A contract may specify invalid input behavior too. There is a difference between assuming valid input and checking it. Neither is automatically correct: the problem must tell us which behavior is required.

### Example: accepting only valid scores

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

## Resolving ambiguous requirements

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

## Boundaries and special cases

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

## Constraints and choosing an approach

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

## Multi-stage requirements

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

## Independent practice

Attempt these before reading the answers. Assume dense arrays of finite safe integers unless specified otherwise.

1. Given `[4, -2, 0, 6, -3]`, sum only negative values. Expected: -5. Explain why the initial total is zero. Test an empty array and an array with no negative values.
2. Given `[7, 2, 2, 5]`, return the last index of the minimum, or -1 for an empty array. Expected: 2. Test `[8]` and `[3,3,3]`.
3. Count integer temperatures from -10 through 40 inclusive, skipping all invalid entries. Input: `[-11,-10,0,40,41,2.5,"20"]`. Expected: 3. Explain why checking only the range is insufficient.
4. Given `[3, 8, 8, 2, 8]`, return the number of occurrences of the maximum value. Expected: 3. Empty input returns zero. Derive a one-pass solution.
5. A report asks for the average of positive values in `[0, -2, 4, 8]`. Expected: 6. Return null if there are no positive values. Identify the error in dividing by the full array length.

## Worked answers

### Exercise 1: sum negative values

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

### Exercise 2: last minimum index

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

### Exercise 3: validate temperatures

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

### Exercise 4: count occurrences of the maximum

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

### Exercise 5: average positive values

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

## Summary

Understand the required output before choosing variables or loop patterns. Clarify input guarantees, tie rules, invalid-input behavior, mutation permissions and empty cases. Derive expected results manually, then use them to check the implementation. A faster algorithm is appropriate only when its assumptions match the contract. In Part 2 we will develop the design process in greater depth, including selecting state, deriving update rules and proving termination.

[Previous lesson](26-writing-clean-and-maintainable-loops.md) | [Contents](#contents) | Next: Part 2 - Designing a Loop-Based Solution (planned)
