# Lesson 17 - Working With Arrays

Previous: [Working With Numbers](16-working-with-numbers.md) | Next: Working With Strings (planned)

## Contents

1. [Array basics](#1-array-basics)
2. [Traversal](#2-traversal)
3. [Sparse arrays](#3-sparse-arrays)
4. [Aggregation](#4-aggregation)
5. [Searching](#5-searching)
6. [Creating arrays](#6-creating-arrays)
7. [Modifying arrays](#7-modifying-arrays)
8. [Rearranging arrays](#8-rearranging-arrays)
9. [Comparing arrays](#9-comparing-arrays)
10. [Multiple arrays](#10-multiple-arrays)
11. [Duplicates](#11-duplicates)
12. [Nested arrays](#12-nested-arrays)
13. [Integrated exercises](#13-integrated-exercises)

## 1. Array basics

An array stores an ordered collection. Indexes begin at zero; length is one greater than the last possible index. A loop can select elements by index rather than requiring separate variable names. A const declaration prevents reassignment of the variable, not modification of its array.

```javascript
const marks = [72, 85, 64];
for (let i = 0; i < marks.length; i++) {
  marks[i] += 5;
}
console.log(marks); // [77, 90, 69]
```

Use i < marks.length, not i <= marks.length. Reading an absent index normally returns undefined; adding undefined to a numeric total produces NaN. push appends and returns the new length. pop removes the last element and returns it (undefined for an empty array).

### Storage, references and boundaries

An array literal creates a new array object. A variable holds a reference to that object. Assigning the variable to another variable copies the reference, not the object. Reassigning a variable changes which object it refers to; modifying an indexed element changes the object itself.

```javascript
const a = [10, 20];
let b = a;
b[0] = 99;
console.log(a[0]); // 99
b = [7, 8];
console.log(a[0], b[0]); // 99 7
```

An absent index normally reads as undefined. Numeric addition with undefined produces NaN, a special numeric value representing an invalid numerical result. Further addition does not repair it. NaN is not strictly equal even to itself. Number.isNaN(value) tests specifically for this value without converting its argument.

```javascript
const a = [10, 20];
let total = 0;
for (let i = 0; i <= a.length; i++) total += a[i];
console.log(total, Number.isNaN(total)); // NaN true
```

The third iteration reads index 2, which is absent. Correct the condition to i < a.length. The sum then becomes 30. With an empty array the corrected loop executes zero times.

### Growing and shrinking

```javascript
const a = [10, 20];
console.log(a.push(30, 40)); // 4
console.log(a.pop()); // 40
console.log(a); // [10, 20, 30]
```

push accepts one or more values, appends them in order and returns the new length. pop takes no arguments and removes the last value. Do not assign a.push(value) to a variable intended to hold the array: the return value is a number. Test pop on an empty array: it returns undefined and leaves length zero.

Practice: Predict the output of assigning two variables to the same array and changing an element through one. Then create a separate array literal and repeat. Explain the difference. Debug a summation using <= and test with [], [5], and [5, 10].

## 2. Traversal

Forward traversal starts at zero; backward traversal starts at length - 1. Changing traversal order does not change storage order. A step of two selects alternating positions, not necessarily even values. For consecutive comparisons start at index one so the previous index exists.

```javascript
const temperatures = [20, 23, 21, 25, 25];
for (let i = 1; i < temperatures.length; i++) {
  console.log(temperatures[i] - temperatures[i - 1]);
}
// 3, -2, 4, 0
```

Practice: Count increases in [10, 15, 12, 18, 18, 20]. Expected: 3.

### Selecting positions and relationships

A request to process every second position is different from a request to process even values. In [11, 20, 31, 40], indexes 0 and 2 contain odd values. Derive the update from the positions, or use a condition when selection depends on the values.

```javascript
const a = [11, 20, 31, 40];
const positions = [];
const evens = [];
for (let i = 0; i < a.length; i += 2) positions.push(a[i]);
for (let i = 0; i < a.length; i++) {
  if (a[i] % 2 === 0) evens.push(a[i]);
}
console.log(positions, evens); // [11,31] [20,40]
```

For a bounded traversal, use an exclusive ending index. Given a nonnegative integer start and end, stop at either end or length, whichever arrives first.

```javascript
const a = [10,20,30,40,50];
const start = 2;
const end = 8;
for (let i = start; i < end && i < a.length; i++) console.log(a[i]);
// 30, 40, 50
```

A start beyond length produces no iterations. Negative starts are outside this example's contract. For reverse traversal start at length - 1, and stop when i becomes negative. For neighboring comparisons, start at one, so i - 1 is valid. To examine groups of three consecutive elements, start at two and access i - 2, i - 1, i.

Practice: Print every consecutive triple in [1,2,3,4,5]: [1,2,3], [2,3,4], [3,4,5]. Predict how many triples exist for lengths 0, 1, 2 and 3. Explain why backward printing does not mutate an array.

## 3. Sparse arrays

A hole is an absent indexed property, not an element explicitly containing undefined. Length includes holes. Traditional loops do not automatically skip them. Object.hasOwn(object, key) returns whether a property belongs directly to the object. The in operator also considers inherited properties. Assigning beyond the end or increasing length can create holes; decreasing length removes trailing elements.

```javascript
const values = [10, , 30];
let total = 0;
let count = 0;
for (let i = 0; i < values.length; i++) {
  if (!Object.hasOwn(values, i)) continue;
  total += values[i];
  count++;
}
console.log(count > 0 ? total / count : 'No elements'); // 20
```

The conditional expression condition ? first : second selects the first value when the condition is truthy, otherwise the second. The average assumes present elements are numbers. Practice: Count holes and present elements in [5, , 10, , 20]. Expected: 2 holes, 3 elements.

### Property existence is not value equality

An array index is a property key. An own property belongs to the array itself. JavaScript objects may also inherit properties from another object, called their prototype. The in operator asks whether a key exists either directly or through inheritance. Object.hasOwn asks only about direct ownership. We do not need to modify prototypes to process arrays; assume ordinary arrays with no inherited numeric keys.

```javascript
const a = [10, , undefined];
for (let i = 0; i < a.length; i++) {
  console.log(i, a[i], i in a, Object.hasOwn(a, i));
}
// 0 10 true true
// 1 undefined false false
// 2 undefined true true
```

Checking a[i] === undefined cannot distinguish the last two positions. Decide whether the task requires present values, numeric values, or all positions. For numeric data cleaning, typeof value returns a string identifying the value's type; typeof 5 is "number", typeof undefined is "undefined". NaN also has type "number", so Number.isFinite(value) is useful: it returns true only for finite numbers, without converting strings.

```javascript
const a = [10, , undefined, NaN, 30, "20"];
let total = 0;
let count = 0;
for (let i = 0; i < a.length; i++) {
  if (Object.hasOwn(a, i) && Number.isFinite(a[i])) {
    total += a[i];
    count++;
  }
}
console.log(total, count, total / count); // 40 2 20
```

For all-invalid input, guard count before dividing. A dense copy built by push turns holes into explicit undefined if every index is read. To preserve holes, assign only existing indexes and set the final length explicitly.

```javascript
const a = [10, , 30, ,];
const b = [];
for (let i = 0; i < a.length; i++) {
  if (Object.hasOwn(a, i)) b[i] = a[i];
}
b.length = a.length;
console.log(b.length, Object.hasOwn(b, 1), Object.hasOwn(b, 3)); // 4 false false
```

Practice: Report length, holes, present values and finite numbers in [10, , undefined, 20, NaN]. Expected: 5, 1, 4, 2. Explain why increasing length is not equivalent to filling an array with zeros.

## 4. Aggregation

Choose state according to the requested statistics. Initialize a sum and count to zero. Initialize extrema from actual elements, guarding empty arrays. For a conditional average, divide by the qualifying count rather than length.

```javascript
const marks = [30, 60, 45, 80, 70];
let total = 0;
let count = 0;
for (let i = 0; i < marks.length; i++) {
  if (marks[i] >= 50) {
    total += marks[i];
    count++;
  }
}
if (count > 0) console.log(total / count); // 70
```

Track an extreme index to recover both position and value. Strict comparison preserves the first tie; inclusive comparison selects the last tie. Practice: Find the first and last minimum positions in [8, 3, 7, 3, 9]. Expected: 1 and 3.

### Deriving a combined report

Given [72,85,64,90,78], we need total, mean, extrema and count of marks at least 80. The sum and count have additive starting values of zero. Extrema must begin with a real candidate; zero would give an incorrect maximum for an all-negative array. A nonempty dense array of finite numbers is the contract for this example.

```javascript
const a = [72,85,64,90,78];
let total = 0;
let high = 0;
let min = a[0];
let max = a[0];
for (let i = 0; i < a.length; i++) {
  const v = a[i];
  total += v;
  if (v >= 80) high++;
  if (v < min) min = v;
  if (v > max) max = v;
}
console.log(total, total/a.length, min, max, high); // 389 77.8 64 90 2
```

For a qualifying maximum, the first array element might not qualify. Use a missing index until the first valid candidate arrives. Short-circuit evaluation protects the absent index: the right side of || is not evaluated when the left side is true.

```javascript
const a = [90, 45, 48, 70, 48];
let best = -1;
for (let i = 0; i < a.length; i++) {
  if (a[i] < 50 && (best === -1 || a[i] > a[best])) best = i;
}
if (best !== -1) console.log(best, a[best]); // 2 48
```

The first qualifying value initializes best; later candidates replace it only if greater. No qualifying values leaves best at -1. To collect all maximum positions, first find the maximum, then traverse again collecting equal values. Alternatively, clear the positions array whenever a new maximum arrives and append ties. Two passes are often easier to reason about.

Practice: Find the largest negative value and its first index in [0,-8,4,-2,-2]. Expected -2 at 3. Test [0,4] and []. Debug a maximum initialized to zero on [-9,-4,-7].

## 5. Searching

A first-match search stops at the first qualifying position. A backward search finds the last match. To collect all matches, omit break. Use -1 for a missing index, not zero.

```javascript
const values = [10, 20, 30, 20, 40];
let foundIndex = -1;
for (let i = 0; i < values.length; i++) {
  if (values[i] === 20) {
    foundIndex = i;
    break;
  }
}
console.log(foundIndex); // 1
```

Closest-value searches minimize Math.abs(value - target). Decide how to resolve ties and handle empty arrays. Practice: Find the closest value to 50 in [35, 48, 60, 51]. Expected: 51.

### From first match to nth match

A search must specify what qualifies, which occurrence is required, and what to return when no match exists. For the third occurrence, maintain a match count and stop only when that count reaches three.

```javascript
const a = [7,2,7,4,7,7];
let count = 0;
let found = -1;
for (let i = 0; i < a.length; i++) {
  if (a[i] === 7) {
    count++;
    if (count === 3) {
      found = i;
      break;
    }
  }
}
console.log(found); // 4
```

A search restricted to indexes 1 through 3 starts at 1 and stops at 3 or the last valid index. A compound search condition such as a[i] >= 50 && a[i] <= 80 finds values in a range. To find all matching positions, append indexes rather than breaking. To search sparse arrays for explicitly stored undefined, check Object.hasOwn before equality.

### Closest qualifying candidate

A warehouse needs the smallest package capacity that can hold 25 units. Capacities below 25 are unsuitable, even if numerically close. For [20,32,24,28,28], the correct capacity is 28, not 24. First filter by the requirement, then minimize the remaining candidates.

```javascript
const capacities = [20,32,24,28,28];
let best = -1;
for (let i = 0; i < capacities.length; i++) {
  if (capacities[i] >= 25) {
    if (best === -1 || capacities[i] < capacities[best]) best = i;
  }
}
if (best === -1) console.log("No suitable package");
else console.log(best, capacities[best]); // 3 28
```

For unrestricted nearest-value search use absolute distance. To prefer the smaller value on a tie, compare both distance and value. The following example assumes a dense array of finite numbers.

```javascript
const a = [28,22,40];
const target = 25;
let best = -1;
for (let i = 0; i < a.length; i++) {
  if (best === -1) best = i;
  else {
    const d = Math.abs(a[i] - target);
    const old = Math.abs(a[best] - target);
    if (d < old || (d === old && a[i] < a[best])) best = i;
  }
}
if (best !== -1) console.log(a[best]); // 22
```

Practice: Find the second occurrence of 5 in [5,2,5,5], expected 2; find the closest value strictly below 25 in [30,24,20,25], expected 24. Test no match, empty input and a tie. Explain why index zero must not be treated as a failed search.

## 6. Creating arrays

Transformation produces a new value for each input. Filtering produces values only when a condition holds. Start with an empty result and append with push; do not assign its return value to the result.

```javascript
const values = [2, 5, 8, 11];
const result = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 === 0) result.push(values[i] * 2);
}
console.log(result); // [4, 16]
```

Copying primitive elements into a new array creates independent storage. Copying nested arrays copies references, not the nested contents. Practice: Collect squares of positive values in [-2, 3, 0, 4]. Expected: [9, 16].

### Preserving input while producing output

A transformation normally produces one result per input. A filter may produce fewer. A partition distributes elements among multiple output arrays. The order of push calls determines the output order.

```javascript
const a = [5,-2,0,8,-4];
const negative = [];
const other = [];
for (let i = 0; i < a.length; i++) {
  if (a[i] < 0) negative.push(a[i]);
  else other.push(a[i]);
}
console.log(negative, other); // [-2,-4] [5,0,8]
```

A dense primitive copy can be created by appending each element. For a reversed copy, traverse backward and append. Neither operation changes the original. Copying an object reference, however, does not duplicate the referenced object.

```javascript
const a = [10,20,30];
const b = [];
for (let i = a.length - 1; i >= 0; i--) b.push(a[i]);
console.log(a,b); // [10,20,30] [30,20,10]
```

Practice: Create a new array of discounted prices, applying a 10 percent discount only to prices at least 100. Input [50,100,200], expected [50,90,180]. Verify the input is unchanged. Explain why using push only inside the qualifying branch would implement filtering instead of the required transformation.

## 7. Modifying arrays

Replacing an element preserves positions. Removing an element shifts later elements left. splice(start, count) removes count elements starting at start and returns an array of removed elements. It modifies the original array. An optional third argument inserts a replacement. For example [10,20,30].splice(1,1) returns [20] and leaves [10,30] in the original array.

```javascript
const values = [5, 0, 0, 8, 0];
for (let i = values.length - 1; i >= 0; i--) {
  if (values[i] === 0) values.splice(i, 1);
}
console.log(values); // [5, 8]
```

Forward removal with an unconditional increment skips shifted elements. Backward removal avoids that error. When appending one copy per original element, save the original length before iteration. Practice: Remove negatives from [-1,-2,5,-3,8]. Expected: [5,8].

### Understanding splice and shifting

splice(start, deleteCount, ...items) modifies an array and returns the removed elements as a new array. Here the notation ...items means any number of additional arguments, not a construct you need to type. A zero deletion count inserts without removing. A negative start counts from the end; a start beyond length is treated as length. An excessive deletion count removes only available elements. In this lesson use explicit nonnegative integer counts.

```javascript
const a = [10,20,30,40];
console.log(a.splice(1,2)); // [20,30]
console.log(a); // [10,40]
console.log(a.splice(1,0,25)); // []
console.log(a); // [10,25,40]
console.log(a.splice(-1,1,50,60)); // [40]
console.log(a); // [10,25,50,60]
```

A forward removal loop with an unconditional increment fails on adjacent matches. For [5,0,0,8,0], deleting index 1 shifts the second zero into index 1, but the update advances to index 2. The result is [5,0,8]. Keep the index unchanged after deletion instead.

```javascript
const a = [5,0,0,8,0];
let i = 0;
while (i < a.length) {
  if (a[i] === 0) a.splice(i,1);
  else i++;
}
console.log(a); // [5,8]
```

### Deriving in-place compaction

Repeated deletion shifts later values. Instead, retain values in the existing array's front portion. We need one index to examine input and another to identify the next output position. Call them read and write. Initially neither has processed anything, so both start at zero. Read advances every iteration; write advances only when retaining a value.

| read | value | write before | action | write after |
|---|---|---|---|---|
| 0 | 5 | 0 | copy to 0 | 1 |
| 1 | 0 | 1 | skip | 1 |
| 2 | 0 | 1 | skip | 1 |
| 3 | 8 | 1 | copy to 1 | 2 |
| 4 | 0 | 2 | skip | 2 |

```javascript
const a = [5,0,0,8,0];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (a[read] !== 0) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
console.log(a); // [5,8]
```

Before each iteration, the first write positions contain exactly the retained values from the already examined prefix. Since write never exceeds read, writing cannot destroy a future unread element. Values are copied in encounter order, preserving relative order. The tail is stale until length is shortened. This technique assumes dense arrays.

The same technique can transform retained elements: replace a[write] = a[read] with a[write] = a[read] * 2 when retaining positive values. If the original must remain unchanged, use a separate result array instead. Backward splice is simple but may shift many elements repeatedly; compaction avoids those repeated shifts. Forward splice is useful when processing order matters, but requires careful index updates.

### Appending without chasing a moving boundary

```javascript
const a = [2,4,6];
const originalLength = a.length;
for (let i = 0; i < originalLength; i++) a.push(a[i]*2);
console.log(a); // [2,4,6,4,8,12]
```

Using i < a.length here would grow the boundary every iteration, so the loop would not finish normally before resource or array-length limits intervene. Do not run that incorrect version.

Practice: Compact [0,0,5,0,8,0] to [5,8]. Test [], [0], [5], [0,0], [5,8]. Transform retained positive values in [-2,3,0,4] to [6,8] in place. Compare the result with a separate-array implementation. Trace why forward deletion skips adjacent matches.

## 8. Rearranging arrays

Swapping requires preserving the first value before overwriting it. To reverse an array in place, swap opposite positions and move both indexes inward until they meet or cross.

```javascript
const values = [10, 20, 30, 40, 50];
let left = 0;
let right = values.length - 1;
while (left < right) {
  const temporary = values[left];
  values[left] = values[right];
  values[right] = temporary;
  left++;
  right--;
}
console.log(values); // [50,40,30,20,10]
```

Empty and singleton arrays require no swaps. Practice: Rotate [1,2,3,4] left by one position by saving the first value, shifting others left, then restoring the saved value at the end. Expected: [2,3,4,1].

### Rotation and preserving unread values

A left rotation by one moves every element except the first one position left, then places the first at the end. Save that first value before overwriting it. Copy from left to right because the source is always one position ahead. For right rotation the source is one position behind, so traverse from right to left.

```javascript
const a = [1,2,3,4];
if (a.length > 1) {
  const last = a[a.length-1];
  for (let i = a.length-1; i > 0; i--) a[i] = a[i-1];
  a[0] = last;
}
console.log(a); // [4,1,2,3]
```

To rotate left by k positions into a new array, use (i+k) % length as the source index. The remainder wraps indexes back to zero. This assumes k is a nonnegative integer. Guard empty input before taking a remainder by length.

```javascript
const a = [10,20,30,40,50];
const k = 7;
const result = [];
for (let i = 0; i < a.length; i++) result.push(a[(i+k)%a.length]);
console.log(result); // [30,40,50,10,20]
```

The loop does not execute for empty input. Seven positions on a five-element array has the same effect as two. For very large k, normalize k first to avoid unnecessarily large index arithmetic.

Practice: Explain why swapping opposite positions for the entire array reverses it twice. Predict the result for [1,2,3,4]. Rotate [1,2,3,4,5] left by three, expected [4,5,1,2,3]. Test zero rotation and rotation by length.

## 9. Comparing arrays

Arrays are objects. Two separate arrays with equal contents are not strictly equal. To compare dense primitive arrays by ordered contents, first compare lengths, then corresponding elements.

```javascript
const a = [2, 4, 6];
const b = [2, 4, 6];
let equal = a.length === b.length;
for (let i = 0; equal && i < a.length; i++) {
  if (a[i] !== b[i]) equal = false;
}
console.log(equal); // true
```

This uses strict element equality, so NaN does not equal itself and nested arrays are compared by identity. Sparse structural equality additionally requires comparing presence at each index. Practice: Compare [1,2] and [2,1] by ordered contents. Expected: false.

### Equality contracts

Identity asks whether two references point to the same object. Ordered content equality asks whether corresponding values match. Unordered equality may instead require the same values with the same multiplicities. For example [1,1,2] and [1,2,2] have the same distinct values but different multiplicities.

For unordered equality of dense primitive arrays, mark positions in the second array as used. Each first-array element must consume exactly one unused matching position. Equal lengths are necessary but not sufficient.

```javascript
const a = [1,2,1];
const b = [2,1,1];
const used = [];
for (let j = 0; j < b.length; j++) used.push(false);
let equal = a.length === b.length;
for (let i = 0; equal && i < a.length; i++) {
  let match = -1;
  for (let j = 0; j < b.length; j++) {
    if (!used[j] && a[i] === b[j]) {
      match = j;
      break;
    }
  }
  if (match === -1) equal = false;
  else used[match] = true;
}
console.log(equal); // true
```

Without used, both occurrences of 1 could match the same position. The flag array prevents that error. This uses strict equality, so NaN requires a special policy if allowed. For structural sparse equality, compare Object.hasOwn for both arrays at every index before comparing present values.

Practice: Compare [1,1,2] with [1,2,2], expected false; compare [2,1,1] with [1,2,1], expected true; compare [] with [], expected true. Explain why checking membership alone is insufficient.

## 10. Multiple arrays

Parallel arrays associate values by position. Validate matching lengths before combining corresponding elements. For concatenation, traverse each array separately. For common values, search one array for each element of the other.

```javascript
const prices = [20, 50, 10];
const quantities = [2, 1, 3];
let total = 0;
if (prices.length === quantities.length) {
  for (let i = 0; i < prices.length; i++) {
    total += prices[i] * quantities[i];
  }
  console.log(total); // 120
}
```

Practice: Combine [2,4,6] and [1,3,5] by position. Expected: [3,7,11].

### Combining unequal lengths and matching by value

Parallel arrays represent corresponding records. If prices and quantities have unequal lengths, reject the data rather than silently treating a missing quantity as zero. In contrast, interleaving independent arrays should usually retain the remainder of the longer array.

```javascript
const a = [1,2,3];
const b = [10,20];
const result = [];
for (let i = 0; i < a.length || i < b.length; i++) {
  if (i < a.length) result.push(a[i]);
  if (i < b.length) result.push(b[i]);
}
console.log(result); // [1,10,2,20,3]
```

The outer condition asks whether either array still has a value. Each inner condition protects its own boundary. Using && loses the longer array's tail.

To collect values in the first array that are absent from the second, search the second array and append only when the search fails.

```javascript
const a = [2,4,6,8];
const b = [4,8,10];
const result = [];
for (let i = 0; i < a.length; i++) {
  let found = false;
  for (let j = 0; j < b.length; j++) {
    if (a[i] === b[j]) { found = true; break; }
  }
  if (!found) result.push(a[i]);
}
console.log(result); // [2,6]
```

This preserves repeated qualifying occurrences in the first array. Distinct output is a separate requirement. To collect common values, reverse the final condition. To concatenate, use separate loops and append all elements of each array in sequence.

Practice: Interleave [] and [10,20], expected [10,20]. Find values in [2,2,4,6] absent from [4], expected [2,2,6]. Explain why a nested search is needed when correspondence is by value rather than position.

## 11. Duplicates

To preserve first occurrences, search the result accumulated so far before appending a value. The inner loop checks whether the current value has already been retained. Reset the flag for every input element.

```javascript
const values = [4, 2, 4, 3, 2];
const unique = [];
for (let i = 0; i < values.length; i++) {
  let seen = false;
  for (let j = 0; j < unique.length; j++) {
    if (unique[j] === values[i]) {
      seen = true;
      break;
    }
  }
  if (!seen) unique.push(values[i]);
}
console.log(unique); // [4,2,3]
```

This assumes ordinary primitive values and strict equality. Practice: Count occurrences of each distinct value in [3,1,3,2,1,3]. Expected: 3 occurs 3 times, 1 occurs 2 times, 2 occurs once.

### Adjacent versus separated duplicates

For grouped equal values, comparing with the last retained value is enough. For arbitrary order it is not: [4,2,4] contains a separated duplicate. Searching all retained values handles arbitrary order. The grouped technique is useful when only consecutive repetition should be removed.

```javascript
const a = [4,4,2,2,4];
const result = [];
for (let i = 0; i < a.length; i++) {
  if (i === 0 || a[i] !== a[i-1]) result.push(a[i]);
}
console.log(result); // [4,2,4]
```

### Counting distinct values

Maintain parallel arrays of distinct values and their counts. Search for the current value in distinct. If absent, append the value and a count of one. Otherwise increment its existing count. The two arrays must remain aligned.

```javascript
const a = [3,1,3,2,1,3];
const distinct = [];
const counts = [];
for (let i = 0; i < a.length; i++) {
  let position = -1;
  for (let j = 0; j < distinct.length; j++) {
    if (a[i] === distinct[j]) { position = j; break; }
  }
  if (position === -1) {
    distinct.push(a[i]);
    counts.push(1);
  } else counts[position]++;
}
console.log(distinct, counts); // [3,1,2] [3,2,1]
```

After processing the first three values, distinct is [3,1] and counts is [2,1]. The next value 2 creates a new position. To find values occurring exactly once, traverse counts and collect distinct[i] whenever counts[i] === 1. To find repeated values, use counts[i] > 1. Do not confuse repeated values with repeated occurrences: [5,5,5] has one repeated distinct value and two occurrences beyond the first.

Practice: Remove duplicates from [], [5], [5,5,5], [1,2,3], [3,1,3,2,1]. Expected [], [5], [5], [1,2,3], [3,1,2]. Count repeated distinct values in [4,2,4,3,2,8], expected 2. Collect values occurring once, expected [3,8]. Explain why the seen flag must reset for every input.

## 12. Nested arrays

Each row is an array. Use the current row's length rather than the first row's length. Initialize row-specific state inside the outer loop and overall state outside it.

```javascript
const rows = [[10,20], [], [5,15,25]];
let grandTotal = 0;
for (let i = 0; i < rows.length; i++) {
  let rowTotal = 0;
  for (let j = 0; j < rows[i].length; j++) {
    rowTotal += rows[i][j];
  }
  grandTotal += rowTotal;
  console.log(rowTotal);
}
console.log(grandTotal); // 30, 0, 45, 75
```

To construct independent rows, create a fresh inner array inside each outer iteration. Reusing one row object makes all stored references point to the same row.

### Row identity and independent construction

A nested array stores references to inner arrays. Reading rows[i] retrieves the row object; rows[i][j] accesses its element. An absent row cannot be indexed further without an error. Assume each outer element is an actual array, including possibly empty rows.

```javascript
const table = [];
for (let i = 0; i < 3; i++) {
  const row = [];
  for (let j = 0; j < 4; j++) row.push(i*4+j+1);
  table.push(row);
}
console.log(table); // [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
```

If row were initialized once outside the outer loop, every appended reference would identify the same growing array. A modification through one row would appear through all references. Creating a fresh row each time prevents this.

### Column aggregation with unequal row lengths

Given [[10,20],[],[5,15,25]], column totals are [15,35,25]. Instead of assuming a fixed width, extend the totals array whenever a new column is encountered. Each row is dense in this example.

```javascript
const rows = [[10,20],[],[5,15,25]];
const totals = [];
for (let i = 0; i < rows.length; i++) {
  for (let j = 0; j < rows[i].length; j++) {
    if (j === totals.length) totals.push(0);
    totals[j] += rows[i][j];
  }
}
console.log(totals); // [15,35,25]
```

The first row establishes two columns. The empty row contributes nothing. The third row adds to existing columns and introduces a third. For column averages, maintain a parallel count for each column; do not divide by the number of rows if some rows lack that column.

### Copying and flattening

A two-dimensional dense primitive array can be copied by creating a new outer array and a fresh copy of each row. Flattening instead appends individual values directly to a single result array.

```javascript
const rows = [[10,20],[],[30]];
const copy = [];
const flat = [];
for (let i = 0; i < rows.length; i++) {
  const row = [];
  for (let j = 0; j < rows[i].length; j++) {
    row.push(rows[i][j]);
    flat.push(rows[i][j]);
  }
  copy.push(row);
}
copy[0][0] = 99;
console.log(rows, copy, flat); // [[10,20],[],[30]] [[99,20],[],[30]] [10,20,30]
```

This copies two levels only; deeper objects would still be shared. For searching a nested array, use a labeled break (Lesson 8) to exit both loops after the first match, or include a found flag in the outer condition.

Practice: Calculate column totals for [[1,2,3],[4],[],[5,6]], expected [10,8,3]. Find the first position of 25 in [[10,20],[15,25],[25]], expected row 1 column 1. Copy the rows independently and demonstrate that changing a copied element leaves the original unchanged.

## 13. Integrated exercises

1. Summarize [30,80,50,90,40]: passing count 3, passing average 73.33333333333333, maximum 90.
2. Remove zeros from [0,0,5,0,8,0] in place: [5,8].
3. Collect distinct common values from [2,2,4,6] and [4,2,8]: [2,4].
4. Reverse each row of [[1,2],[],[3,4,5]] independently: [[2,1],[],[5,4,3]].
5. Find all positions of the maximum in [5,9,3,9]: [1,3].
6. Compare [1,,3] and [1,undefined,3] by presence and value: false.
7. Append one doubled copy of each original element of [2,3]: [2,3,4,6]. Explain termination.
8. Calculate row totals and grand total for [[4,6],[],[2,3,5]]: [10,0,10], grand total 20.

### Additional independent problems

9. Inventory: products ["Pen","Book","Bag"], prices [20,100,500], quantities [10,4,2]. Report per-product values [200,400,1000], total 1600, highest-value product Bag. Reject mismatched lengths.
10. Given [5,9,3,9,7,9], collect all maximum positions [1,3,5]. Compare a two-pass approach with a single-pass approach that resets positions when a new maximum is found.
11. Given [[10,20,30],[],[40,50],[5,15]], report row averages 20, no average, 45, 10; best row 2; overall average 170/7. Explain why averaging the row averages is incorrect.
12. Given [5,-2,0,8,-4,0,3], produce [-2,-4,0,0,5,8,3] while preserving order within each category. Keep the input unchanged.
13. Debug a forward splice loop on [0,0,1,0]. Explain the skipped element and compare three corrected approaches. Expected [1].
14. Compare unordered contents of [1,2,2] and [2,1,1]. Expected false. Design three additional tests, including an empty array.
15. Given [10,,undefined,20,NaN], create a dense array of finite numbers [10,20] and report two retained values. Explain why a hole and an explicit undefined are different.

## Summary

Choose the traversal boundary from the valid positions, the state from the requested result, and the mutation strategy from whether original storage must survive. Check empty input, ties, missing values, duplicate multiplicities and unequal lengths according to the problem contract. Prefer a simple correct implementation before optimizing. All numerical examples assume small finite integers or ordinary finite numeric data unless stated otherwise. Sparse-array algorithms explicitly check presence; other algorithms assume dense arrays.

Previous: [Working With Numbers](16-working-with-numbers.md) | Next: Lesson 18 - Working With Strings (planned).
