# Lesson 24 - Debugging and Testing Loops

[Previous: Lesson 23](23-loop-correctness-and-edge-cases.md) | Next: Lesson 25 (planned)

## Table of contents

- [Learning objectives](#learning-objectives)
- [Tracing loops manually](#tracing-loops-manually)
- [Debugging tools](#debugging-tools)
- [Common loop errors](#common-loop-errors)
- [Testing loop behavior](#testing-loop-behavior)
- [Regression testing](#regression-testing)
- [Integrated exercises](#integrated-exercises)
- [Summary](#summary)

## Learning objectives

By the end of this chapter, you should be able to predict execution, locate the first incorrect state, inspect a running program, design meaningful tests, and preserve tests for repaired defects. You should already understand traditional loops, functions, arrays, conditions, state tracking and loop invariants from earlier lessons.

A defect is a problem in an implementation. A failure is observable incorrect behavior caused by a defect under particular conditions. Debugging investigates causes; testing compares actual behavior with required behavior. A passing test establishes correctness for that test, not necessarily for all inputs.

## Tracing loops manually

Suppose a shop records daily sales as `[4, 7, 2, 9]`. We want the total of days with sales of at least 5. The expected result is 16, because only 7 and 9 qualify.

```javascript
const sales = [4, 7, 2, 9];
let total = 0;
for (let i = 0; i < sales.length; i++) {
  if (sales[i] >= 5) {
    total += sales[i];
  }
}
console.log(total);
```

Expected output:

```text
16
```

A trace records execution rather than merely listing input values. Before the first iteration, `total` is zero. At each iteration, `sales[i]` retrieves the current value, the comparison determines whether it qualifies, and `+=` adds that value to the previous total only when the condition is true.

| i | sales[i] | Condition | Total before | Total after |
|---|---|---|---|---|
| 0 | 4 | false | 0 | 0 |
| 1 | 7 | true | 0 | 7 |
| 2 | 2 | false | 7 | 7 |
| 3 | 9 | true | 7 | 16 |

After the update from 3 to 4, `4 < 4` is false. The loop terminates. Include this final failed condition when investigating boundary errors.

### Finding the first divergence

Consider a program intended to count positive values. It instead adds the values themselves:

```javascript
const values = [2, -1, 3];
let count = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) count += values[i];
}
console.log(count);
```

Expected output:

```text
5
```

The required result is 2. Compare expected and actual state after each iteration. At index 0, expected count is 1 but actual count is 2. This first divergence is more informative than the final discrepancy. The condition is correct; the update is wrong. Replace `count += values[i]` with `count++`. A counter measures occurrences, not magnitudes.

### Tracing multiple state variables

Find the longest consecutive run of positive values in `[2, 3, 0, 4, 5, 6, -1]`. A run ends at any nonpositive value. We need both the current run length and the greatest run length observed so far.

```javascript
const values = [2, 3, 0, 4, 5, 6, -1];
let current = 0;
let best = 0;
for (let i = 0; i < values.length; i++) {
  if (values[i] > 0) current++;
  else current = 0;
  if (current > best) best = current;
}
console.log(best);
```

Expected output:

```text
3
```

| Value | current after update | best after comparison |
|---|---|---|
| 2 | 1 | 1 |
| 3 | 2 | 2 |
| 0 | 0 | 2 |
| 4 | 1 | 2 |
| 5 | 2 | 2 |
| 6 | 3 | 3 |
| -1 | 0 | 3 |

The order matters: compare after updating `current`. If you compare before updating, a run ending at the final element may never be recorded. Reset only `current`; resetting `best` would erase historical information.

### Tracing nested loops

For each pair of distinct positions in `[3, 5, 7]`, print the sum once. Starting the inner index at `i + 1` prevents self-pairs and reversed duplicates.

```javascript
const values = [3, 5, 7];
for (let i = 0; i < values.length; i++) {
  for (let j = i + 1; j < values.length; j++) {
    console.log(i, j, values[i] + values[j]);
  }
}
```

Expected output:

```text
0 1 8
0 2 10
1 2 12
```

Trace the outer index, inner index and inner initialization separately. At i=2, j begins at 3, so the inner body never runs. That is correct. Changing the inner initialization to zero would create self-pairs and reversed pairs. A trace of index pairs makes this structural error visible even when duplicate numeric values obscure it.

### Practice: predict and explain

For `[1, 2, 0, 3]`, predict the final values of `current` and `best` in the positive-run algorithm. Then explain what happens if the reset is removed.

#### Worked answer

The successive states are (1,1), (2,2), (0,2), (1,2). The final current run is 1 and the best run is 2. Without the reset, current becomes 1, 2, 2, 3 and best becomes 3. The algorithm would count positive elements rather than consecutive positive elements. The zero is the smallest useful input feature for exposing the defect.

## Debugging tools

Manual traces are valuable for small examples. For larger programs, inspect actual execution with logging or a debugger. Begin with a reproducible failure and a precise expected result. Avoid changing several unrelated statements at once.

### Logging meaningful state

A log is diagnostic output. `console.log` accepts multiple arguments and displays their values. Labels help distinguish indexes, data and state. To investigate why a sum becomes incorrect, record the value before and after its update.

```javascript
const values = [4, 8, 3];
let total = 0;
for (let i = 0; i < values.length; i++) {
  console.log('before', i, values[i], total);
  total += values[i];
  console.log('after', i, total);
}
```

Expected output:

```text
before 0 4 0
after 0 4
before 1 8 4
after 1 12
before 2 3 12
after 2 15
```

Logging only the final total cannot identify which update failed. Logging before and after the update reveals both its operands and its effect. If a loop has thousands of iterations, log only the suspicious range using an `if` condition. Remove temporary diagnostics when finished, especially when output contains private data.

### Breakpoints and stepping

A breakpoint pauses execution before a selected statement. Unlike logging, it lets you inspect variables and choose when to resume. In a browser, open developer tools, select the Sources or Debugger panel, open the JavaScript source and click the line number beside the statement to pause on. Interface labels vary by browser. In an editor such as VS Code, open a JavaScript file and use its Node.js debugging configuration.

Try this program in a local JavaScript file:

```javascript
const values = [6, 2, 8];
let minimum = values[0];
for (let i = 1; i < values.length; i++) {
  if (values[i] < minimum) {
    minimum = values[i];
  }
}
console.log(minimum);
```

Expected output:

```text
2
```

Place a breakpoint on the `if` line. At the first pause, i=1, values[i]=2 and minimum=6. Step over the condition and assignment to observe minimum becoming 2. Resume to the next breakpoint: i=2, values[i]=8 and minimum=2. The condition is false.

Step over executes the current statement without entering a called function. Step into enters a called function when possible. Step out resumes until the current function returns. Resume continues until another breakpoint or program completion. A watch expression is an expression evaluated by the debugger while paused, such as `values[i] < minimum`. Avoid watch expressions that mutate state, such as `i++`.

A conditional breakpoint pauses only when its condition is true. For example, `i === 500` targets one iteration. A breakpoint inside a branch that never executes will never pause; move it to the condition to inspect why the branch is skipped.

### The debugger statement

The JavaScript `debugger;` statement requests a pause when debugging facilities are active. Without an active debugger it normally has no effect. It is useful when you can edit the source but cannot easily locate the line in the debugger interface.

```js
for (let i = 0; i < 10; i++) {
  if (i === 5) debugger;
  console.log(i);
}
```

With an attached debugger, the program pauses at i=5 before printing 5. Resume to complete the loop. This is an interactive example, not an automated test.

### Practice: choose an inspection point

A search returns -1 for a value you know exists. Where should you pause, and what should you inspect?

#### Worked answer

Pause at the comparison inside the search. Inspect the index, current element, target and equality expression. If the target is at index zero but the first inspected index is one, initialization is wrong. If both values appear to be 7 but strict equality is false, inspect `typeof` for each operand: the string '7' and number 7 are different types. Do not replace strict equality blindly; determine whether the contract requires numeric conversion or exact type matching.

## Common loop errors

### Boundary errors and undefined values

Suppose a function sums a dense array of finite numbers. An inclusive upper boundary reads one position beyond the last element.

```javascript
const values = [3, 5];
let total = 0;
for (let i = 0; i <= values.length; i++) {
  total += values[i];
}
console.log(total);
```

Expected output:

```text
NaN
```

At i=2, `values[2]` is undefined. Numeric addition with undefined produces NaN (not a number). The preceding total was 8, so the final iteration introduces the failure. Correct the condition to `i < values.length`. Do not hide the problem by substituting zero for every undefined value: that could conceal invalid data or a different indexing defect.

### Premature termination

A search must inspect every candidate until a match is found. Returning failure inside the first nonmatching iteration is incorrect.

```javascript
function find(values, target) {
  for (let i = 0; i < values.length; i++) {
    if (values[i] === target) return i;
    return -1;
  }
  return -1;
}
console.log(find([4, 8, 2], 8));
```

Expected output:

```text
-1
```

At i=0, 4 is not 8, so `return -1` exits the entire function. It does not merely end the iteration. The failure conclusion is justified only after all candidates have been checked.

```javascript
function find(values, target) {
  for (let i = 0; i < values.length; i++) {
    if (values[i] === target) return i;
  }
  return -1;
}
console.log(find([4, 8, 2], 8));
console.log(find([], 8));
```

Expected output:

```text
1
-1
```

### Nontermination and skipped updates

This intentionally incorrect fragment must not be run without a debugger or execution limit:

```js
let i = 0;
while (i < 5) {
  if (i === 2) continue;
  i++;
}
```

At i=2, continue jumps back to the condition, bypassing the update. The same state repeats forever. To diagnose such a loop, trace the progress variable on every possible path. Here a for loop naturally performs its update after continue.

```javascript
for (let i = 0; i < 5; i++) {
  if (i === 2) continue;
  console.log(i);
}
```

Expected output:

```text
0
1
3
4
```

For a while loop, another solution is to advance the index on the skipped path before continuing. Choose the structure that makes progress easiest to verify. If an actual program freezes, stop its process rather than adding unlimited logs to a nonterminating loop.

### Mutation and skipped elements

Removing an element shifts later elements left. An unconditional index increment can skip the shifted value.

```javascript
const values = [0, 0, 3, 0];
for (let i = 0; i < values.length; i++) {
  if (values[i] === 0) values.splice(i, 1);
}
console.log(values.join(','));
```

Expected output:

```text
0,3
```

After deleting the first zero, the second zero moves into index zero. The update moves i to one, so that zero is skipped. Traverse backward, or increment only when retaining an element.

```javascript
const values = [0, 0, 3, 0];
let i = 0;
while (i < values.length) {
  if (values[i] === 0) values.splice(i, 1);
  else i++;
}
console.log(values.join(','));
```

Expected output:

```text
3
```

Each iteration either reduces the array length or increases i. Both reduce the number of unprocessed elements. This reasoning explains termination even when i does not advance on every iteration.

### Practice: diagnose a maximum calculation

A maximum algorithm initializes `best = 0` and updates it whenever an element is greater. It returns zero for `[-8,-3,-5]`. Identify the faulty assumption and correct it. Specify what should happen for an empty array.

#### Worked answer

Zero is not necessarily an input value or a lower bound. Initialize best from the first element, then inspect remaining elements. Choose an explicit empty-array result; here we use undefined to represent no maximum.

```javascript
function maximum(values) {
  if (values.length === 0) return undefined;
  let best = values[0];
  for (let i = 1; i < values.length; i++) {
    if (values[i] > best) best = values[i];
  }
  return best;
}
console.log(maximum([-8,-3,-5]));
console.log(maximum([]));
```

Expected output:

```text
-3
undefined
```

The first element establishes a valid candidate. Each update preserves the invariant that best is the greatest value inspected. This contract assumes dense arrays of finite numbers.

## Testing loop behavior

### From examples to assertions

Printing results requires a person to compare them. An assertion performs a comparison and signals failure automatically. We can implement a simple assertion using `throw new Error(message)`. The `Error` object carries a diagnostic message; `throw` interrupts normal execution. If uncaught, it stops the current program with an error.

```javascript
function assertEqual(actual, expected, name) {
  if (!Object.is(actual, expected)) {
    throw new Error(name + ': expected ' + expected + ', got ' + actual);
  }
  console.log('PASS: ' + name);
}
assertEqual(2 + 3, 5, 'addition');
assertEqual(NaN, NaN, 'NaN comparison');
```

Expected output:

```text
PASS: addition
PASS: NaN comparison
```

`Object.is(a,b)` compares values without type conversion. Unlike strict equality, it treats NaN as equal to itself and distinguishes positive zero from negative zero. For objects and arrays it compares identity, not contents. Use a comparison suited to the contract. For example, two separately created arrays with identical elements are not the same object.

### Selecting meaningful test cases

Consider a function counting values at least a threshold. Its contract accepts a dense numeric array and finite threshold, returns a count, and does not mutate the input. Test empty input, a single qualifying value, a single nonqualifying value, exact equality, mixed values, all qualifying and none qualifying.

```javascript
function countAtLeast(values, threshold) {
  let count = 0;
  for (let i = 0; i < values.length; i++) {
    if (values[i] >= threshold) count++;
  }
  return count;
}
const cases = [
  [[], 5, 0], [[5], 5, 1], [[4], 5, 0],
  [[4,5,6], 5, 2], [[5,8], 5, 2], [[1,2], 5, 0]
];
for (let i = 0; i < cases.length; i++) {
  const test = cases[i];
  const actual = countAtLeast(test[0], test[1]);
  if (actual !== test[2]) throw new Error('Case ' + i);
}
console.log('Passed ' + cases.length + ' cases');
```

Expected output:

```text
Passed 6 cases
```

Each inner array holds input, threshold and expected count. `test[0]` is itself an array; `test[1]` is the threshold; `test[2]` is the independently calculated answer. The equality case distinguishes `>=` from `>`. Empty input exposes incorrect initialization or unconditional first-element access. A table of cases separates test data from the test-running loop.

### Comparing arrays and checking mutation

For dense arrays of primitive values, compare lengths and corresponding elements. To check that a function leaves its input unchanged, create an independent copy before calling it. A shallow copy is sufficient for primitive elements but not for nested mutable objects.

```javascript
function equalArrays(a, b) {
  if (a.length !== b.length) return false;
  for (let i = 0; i < a.length; i++) {
    if (!Object.is(a[i], b[i])) return false;
  }
  return true;
}
function positives(values) {
  const result = [];
  for (let i = 0; i < values.length; i++) {
    if (values[i] > 0) result.push(values[i]);
  }
  return result;
}
const input = [-2, 3, 0, 4];
const before = [];
for (let i = 0; i < input.length; i++) before.push(input[i]);
if (!equalArrays(positives(input), [3,4])) throw new Error('Wrong result');
if (!equalArrays(input, before)) throw new Error('Input changed');
console.log('Both checks passed');
```

Expected output:

```text
Both checks passed
```

This comparator does not distinguish holes from explicit undefined values. If sparse structure is part of the contract, compare `Object.hasOwn(a,i)` and `Object.hasOwn(b,i)` as well. Do not claim a general deep-equality implementation from this simple loop.

### Independent reference implementations

An optimized algorithm can be checked against a simpler implementation whose reasoning is independent. For example, a rolling window subtracts an outgoing value and adds an incoming one. A direct nested loop recalculates each window. Compare them for small inputs.

```javascript
function direct(a, k) {
  let best = -Infinity;
  for (let i = 0; i <= a.length - k; i++) {
    let sum = 0;
    for (let j = i; j < i + k; j++) sum += a[j];
    if (sum > best) best = sum;
  }
  return best;
}
function rolling(a, k) {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += a[i];
  let best = sum;
  for (let i = k; i < a.length; i++) {
    sum -= a[i-k];
    sum += a[i];
    if (sum > best) best = sum;
  }
  return best;
}
const arrays = [[-4,-2,-7], [1,5,2,8], [0], [3,3,3]];
let checked = 0;
for (let i = 0; i < arrays.length; i++) {
  const a = arrays[i];
  for (let k = 1; k <= a.length; k++) {
    if (direct(a,k) !== rolling(a,k)) throw new Error('Mismatch');
    checked++;
  }
}
console.log(checked);
```

Expected output:

```text
11
```

The contract restricts k to integers from 1 through array length and uses small integers to avoid rounding differences. The nested test loop varies both input and window size. `-Infinity` is below every finite number, so the direct algorithm handles all-negative windows. Agreement is useful evidence, not proof: both implementations might share an incorrect interpretation of the requirements.

### Floating-point tests

For approximate numeric results, exact equality may be unsuitable. An absolute tolerance measures the allowed magnitude of the difference. Choose it from the problem's required precision, not simply to make a test pass.

```javascript
let sum = 0;
for (let i = 0; i < 3; i++) sum += 0.1;
const expected = 0.3;
const tolerance = 1e-12;
console.log(sum === expected);
console.log(Math.abs(sum - expected) <= tolerance);
```

Expected output:

```text
false
true
```

For very large or small values, relative tolerance may also be appropriate. Reject NaN and infinities when the contract requires finite results. A tolerance should not mask an off-by-one error or an incorrect formula.

### Practice: design tests before implementation

A function returns the first index of a target or -1 when absent. Design cases that expose starting at index one, stopping before the last element, returning the last match instead of the first, and failing on empty input.

#### Worked answer

Use `[7,2],7 -> 0`, `[2,7],7 -> 1`, `[7,2,7],7 -> 0`, `[],7 -> -1`, and `[2,3],7 -> -1`. Each case targets a different requirement. The duplicate case distinguishes first-match behavior from last-match behavior. A singleton `[7],7 -> 0` combines both boundary positions and is also useful.

## Regression testing

A regression occurs when a change breaks behavior that previously worked. A regression test preserves a reproducible example of a repaired defect. Keep the failing input and independently established expected output, repair the implementation, and rerun both old and new tests.

### Preserving a discovered defect

Suppose a run-length function returns the longest run of equal values. It works for runs followed by a different value, but fails when the longest run reaches the end. A useful regression case is `[1,2,2,2] -> 3`.

Derive a solution that updates the best length during each iteration instead of waiting for a run to end. Handle empty input before initializing the current run to one.

```javascript
function longestRun(a) {
  if (a.length === 0) return 0;
  let current = 1;
  let best = 1;
  for (let i = 1; i < a.length; i++) {
    if (a[i] === a[i-1]) current++;
    else current = 1;
    if (current > best) best = current;
  }
  return best;
}
const tests = [
  [[1,1,2],2], [[1,2,2,2],3], [[],0],
  [[9],1], [[4,4,4],3], [[1,2,3],1]
];
for (let i = 0; i < tests.length; i++) {
  if (longestRun(tests[i][0]) !== tests[i][1]) {
    throw new Error('Regression case ' + i);
  }
}
console.log('Passed ' + tests.length + ' regression cases');
```

Expected output:

```text
Passed 6 regression cases
```

The original failure is retained alongside ordinary cases. The all-equal and singleton cases reinforce initialization and finalization behavior. Do not delete old tests merely because a new implementation passes a new example. If requirements genuinely change, revise the affected expected results deliberately.

### Practice: preserve a mutation bug

A function removes all zeros in place. A repair changes forward deletion to backward deletion. Design a regression suite.

#### Worked answer

Use `[0,0,3] -> [3]` to expose adjacent deletions, `[0,0] -> []` for complete removal, `[] -> []` for no iterations, `[3,4] -> [3,4]` for preservation, and `[0,3,0] -> [3]` for both boundaries. Check the original array after the function call, not merely a returned array, because in-place mutation is required.

## Integrated exercises

Attempt each problem before reading its solution.

### Exercise 1: repair a batch total

Given `[3,4,0,2,5,0,6]`, zero separates batches. Return the totals of nonempty batches, including a final batch without a separator. Expected `[7,7,6]`. Empty batches are ignored. Assume positive integer items and zero separators. A defective implementation pushes totals only at separators. Explain the failure and test the correction.

#### Worked solution

Maintain a running total. Positive items extend the current batch; zero closes it. Because all items are positive, total greater than zero also indicates a nonempty batch. After traversal, flush any remaining total. This last step handles the missing final separator.

```javascript
function batchTotals(a) {
  const result = [];
  let total = 0;
  for (let i = 0; i < a.length; i++) {
    if (a[i] === 0) {
      if (total > 0) result.push(total);
      total = 0;
    } else total += a[i];
  }
  if (total > 0) result.push(total);
  return result;
}
console.log(batchTotals([3,4,0,2,5,0,6]).join(','));
console.log(batchTotals([0,0,2,0]).join(','));
console.log(batchTotals([]).length);
```

Expected output:

```text
7,7,6
2
0
```

The first two separators save 7 each and reset total. The final 6 remains pending when the loop ends. The final conditional saves it once. A trailing separator resets total to zero, preventing duplicate output. If negative or zero-valued items were allowed, total alone could not distinguish an empty batch; a separate count or flag would be necessary.

### Exercise 2: verify binary search boundaries

Implement first-occurrence binary search for a sorted ascending numeric array. Return -1 if absent. Test duplicates, empty input, first and last positions and absent values. Explain why each iteration terminates.

#### Worked solution

Maintain inclusive left and right boundaries and a saved answer initialized to -1. When a match occurs, save its index but continue searching the left portion for an earlier match. A smaller midpoint value eliminates the left portion; a larger value eliminates the right portion.

```javascript
function first(a, target) {
  let left = 0;
  let right = a.length - 1;
  let answer = -1;
  while (left <= right) {
    const mid = left + Math.trunc((right-left)/2);
    if (a[mid] < target) left = mid + 1;
    else {
      if (a[mid] === target) answer = mid;
      right = mid - 1;
    }
  }
  return answer;
}
const tests = [
  [[1,2,2,2,4],2,1], [[],2,-1], [[2],2,0],
  [[1,3,5],1,0], [[1,3,5],5,2], [[1,3,5],4,-1]
];
for (let i = 0; i < tests.length; i++) {
  const t = tests[i];
  if (first(t[0],t[1]) !== t[2]) throw new Error('Case ' + i);
}
console.log('Passed ' + tests.length + ' cases');
```

Expected output:

```text
Passed 6 cases
```

For `[1,2,2,2,4]`, the first midpoint is 2, so answer becomes 2 and right becomes 1. The next midpoint is 0, whose value is too small; left becomes 1. Midpoint 1 matches and answer becomes 1. Right becomes 0 and the loop stops. `right-left` measures the boundary distance, division by two finds its halfway offset, and truncation gives an integer index. Each update excludes mid, reducing the candidate count. The sorted-input precondition is essential.

### Exercise 3: test a stable sorting implementation

Sort records by ascending score, retaining the original relative order of equal scores. Given A:3, B:1, C:3, D:1, expect B,D,A,C. Use insertion sort and explain the test that exposes instability.

#### Worked solution

Save the current record before shifting larger records right. Shift only when the preceding score is strictly greater. Equal scores must not shift past one another.

```javascript
const a = [{id:'A',score:3},{id:'B',score:1},
           {id:'C',score:3},{id:'D',score:1}];
for (let i = 1; i < a.length; i++) {
  const item = a[i];
  let j = i - 1;
  while (j >= 0 && a[j].score > item.score) {
    a[j+1] = a[j];
    j--;
  }
  a[j+1] = item;
}
let order = '';
for (let i = 0; i < a.length; i++) order += a[i].id;
if (order !== 'BDAC') throw new Error('Wrong stable order');
console.log(order);
```

Expected output:

```text
BDAC
```

When inserting D, the two score-3 records shift right, but B has score 1 and stops the shifting. D is inserted after B. Changing `>` to `>=` would shift equal-score records too, violating stability. Testing only the sequence of scores would miss this defect; record identities expose it. Additional tests should cover already sorted, reverse sorted, empty and all-equal records, and preservation of every original record.

## Summary

Start with a clear contract and reproducible failure. Trace the first incorrect state rather than guessing from the final result. Use breakpoints and targeted logging to inspect execution. Correct the cause, then verify ordinary, boundary and adversarial cases. Preserve the discovered failure as a regression test. Tests complement invariants and termination reasoning; neither a few passing examples nor a successful run alone proves general correctness.

[Previous: Lesson 23](23-loop-correctness-and-edge-cases.md) | Next: Lesson 25 - Loop Performance and Optimization (planned)
