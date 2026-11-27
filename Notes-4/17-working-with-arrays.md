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

## 13. Integrated exercises

1. Summarize [30,80,50,90,40]: passing count 3, passing average 73.33333333333333, maximum 90.
2. Remove zeros from [0,0,5,0,8,0] in place: [5,8].
3. Collect distinct common values from [2,2,4,6] and [4,2,8]: [2,4].
4. Reverse each row of [[1,2],[],[3,4,5]] independently: [[2,1],[],[5,4,3]].
5. Find all positions of the maximum in [5,9,3,9]: [1,3].
6. Compare [1,,3] and [1,undefined,3] by presence and value: false.
7. Append one doubled copy of each original element of [2,3]: [2,3,4,6]. Explain termination.
8. Calculate row totals and grand total for [[4,6],[],[2,3,5]]: [10,0,10], grand total 20.

Next: Lesson 18 - Working With Strings.
