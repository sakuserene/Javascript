# JavaScript Arrays

[Previous: JavaScript foundations](#introduction) | [Next: Array methods](https://javascript.info/array-methods)

> Reconstructed comprehensive lesson. The original wording and individual exercises could not be recovered verbatim. Interactive illustrations are represented by static Markdown.

## Table of contents

- [1. Introduction](#1-introduction)
- [2. Declaration and initialization](#2-declaration-and-initialization)
- [3. Elements and data types](#3-elements-and-data-types)
- [4. Indexes and positions](#4-indexes-and-positions)
- [5. Accessing elements](#5-accessing-elements)
- [6. Updating elements](#6-updating-elements)
- [7. The length property](#7-the-length-property)
- [8. Accessing elements with at()](#8-accessing-elements-with-at)
- [9. Adding and removing elements](#9-adding-and-removing-elements)
- [10. Stacks, queues and deques](#10-stacks-queues-and-deques)
- [11. let, const and freezing arrays](#11-let-const-and-freezing-arrays)
- [12. Array internals](#12-array-internals)
- [13. Empty slots and sparse arrays](#13-empty-slots-and-sparse-arrays)
- [14. Performance and complexity](#14-performance-and-complexity)
- [15. Iterating over arrays](#15-iterating-over-arrays)
- [16. References and identity](#16-references-and-identity)
- [17. Shallow and deep copying](#17-shallow-and-deep-copying)
- [18. Spread syntax](#18-spread-syntax)
- [19. Array destructuring](#19-array-destructuring)
- [20. Mutable and immutable operations](#20-mutable-and-immutable-operations)
- [21. Arrays of objects](#21-arrays-of-objects)
- [22. Objects containing arrays](#22-objects-containing-arrays)
- [23. Multidimensional arrays](#23-multidimensional-arrays)
- [24. Jagged and deeply nested arrays](#24-jagged-and-deeply-nested-arrays)
- [25. Constructors and static methods](#25-constructors-and-static-methods)
- [26. Conversion and coercion](#26-conversion-and-coercion)
- [27. Comparison and equality](#27-comparison-and-equality)
- [28. Validating arrays](#28-validating-arrays)
- [29. Common mistakes and edge cases](#29-common-mistakes-and-edge-cases)
- [30. Summary and capability reference](#30-summary-and-capability-reference)
- [31. Progressive exercises](#31-progressive-exercises)

## 1. Introduction

An array stores an ordered collection of values. Unlike an ordinary object, it provides operations designed around positions and a special `length` property.

```javascript
const students = ["Anna", "Ben", "Chloe"];
const student = { name: "Anna", age: 21 };
console.log(students[0]); // Anna
console.log(student.name); // Anna
```

Use arrays for ordered collections and objects for named properties. Arrays are themselves objects, but have special indexed-property behavior.

## 2. Declaration and initialization

```javascript
const empty = [];
const fruits = ["Apple", "Orange", "Plum"];
const other = new Array();
const numbers = [2 + 3, 4 * 2,];
console.log(numbers); // [5, 8]
```
Square brackets create an array literal. Commas separate elements; a single trailing comma does not create an extra element. Each expression is evaluated when the array is created. Prefer literals for ordinary creation.

## 3. Elements and data types

Arrays can contain any JavaScript value, including functions, objects and other arrays.

```javascript
const values = ["Hello", 42, true, null, undefined, { id: 1 }, [2, 3], () => "Hi"];
console.log(values[5].id); // 1
console.log(values[6][1]); // 3
console.log(values[7]()); // Hi
```
A homogeneous array contains values of a consistent type; a heterogeneous array contains mixed types. JavaScript does not enforce homogeneity.

## 4. Indexes and positions

Indexes start at zero, not one.

```text
Index:     0        1        2
Value:   Apple    Orange    Plum
```
For a dense array of length `n`, valid positions run from `0` through `n - 1`. Sparse arrays may have missing properties within that range.

## 5. Accessing elements

```javascript
const days = ["Monday", "Tuesday", "Wednesday"];
let index = 1;
console.log(days[index]); // Tuesday
console.log(days[99]); // undefined
console.log(days[99] ?? "Unknown day"); // Unknown day
console.log(days[99]?.length); // undefined
```
The expression inside brackets is evaluated first. `??` supplies a fallback only for null or undefined; `?.` stops property access when the receiver is nullish.

To validate a user-selected position:

```javascript
const position = 2;
if (Number.isInteger(position) && position >= 1 && position <= days.length) {
    console.log(days[position - 1]); // Tuesday
}
```

## 6. Updating elements

```javascript
const scores = [10, 20, 30];
scores[1] = 25;
scores[0] += 5;
scores[2]++;
console.log(scores); // [15, 25, 31]
```
`+=` reads, adds and assigns; `++` increments by one. Swap two positions using a temporary variable or destructuring:

```javascript
let temp = scores[0];
scores[0] = scores[1];
scores[1] = temp;
console.log(scores); // [25, 15, 31]
[scores[0], scores[1]] = [scores[1], scores[0]];
console.log(scores); // [15, 25, 31]
```

## 7. The length property

```javascript
const items = ["A", "B", "C"];
console.log(items.length); // 3
console.log(items[items.length - 1]); // C
items[items.length] = "D";
items[6] = "G";
console.log(items.length); // 7
items.length = 2;
console.log(items); // ["A", "B"]
items.length = 4; // creates two empty slots
items.length = 0; // clears the array
```
Length is not the count of present elements. It is always greater than every own array index. Assigning a smaller length deletes indexed properties beyond the new boundary (unless deletion is prevented). Increasing length creates holes, not explicit undefined values. Valid lengths are integers from 0 to 2**32 - 1; invalid lengths throw RangeError.

## 8. Accessing elements with at()

```javascript
const temperatures = [18, 21, 25, 23];
console.log(temperatures.at(0)); // 18
console.log(temperatures.at(-1)); // 23
console.log(temperatures.at(-2)); // 25
console.log(temperatures.at(-5)); // undefined
console.log(temperatures.at(-1) - temperatures.at(-2)); // -2
```
A negative index is interpreted relative to length. For -2, the effective index is length - 2. Bracket access with a negative number instead looks for a property named "-2".

## 9. Adding and removing elements

| Method | Operation | Return value |
| --- | --- | --- |
| push(...items) | Append | New length |
| pop() | Remove last | Removed value |
| unshift(...items) | Prepend | New length |
| shift() | Remove first | Removed value |

```javascript
const fruits = ["Apple", "Orange"];
console.log(fruits.push("Plum")); // 3
console.log(fruits.pop()); // Plum
console.log(fruits.unshift("Pear", "Kiwi")); // 4
console.log(fruits.shift()); // Pear
console.log(fruits); // ["Kiwi", "Apple", "Orange"]
```
`push` returns length, whereas an assignment expression returns the assigned value.

```javascript
const a = [];
console.log(a.push("A")); // 1
console.log(a[a.length] = "B"); // B
a.push(["C", "D"]); // one nested array
a.push(...["E", "F"]); // two separate elements
console.log(a); // ["A", "B", ["C", "D"], "E", "F"]
```
`at(-1)` reads without removing; `pop()` removes. Both pop and shift return undefined on empty arrays. `unshift(a,b)` preserves argument order. For middle insertion/removal use `splice(start, deleteCount, ...items)`. `delete a[0]` leaves a hole rather than shifting elements.

## 10. Stacks, queues and deques

A stack uses last-in-first-out (LIFO); a queue uses first-in-first-out (FIFO); a deque permits operations at either end.

```javascript
const history = [];
history.push("Draw circle", "Draw square");
console.log(history.pop()); // Draw square
const jobs = ["Document A", "Document B"];
jobs.push("Document C");
console.log(jobs.shift()); // Document A
```
```text
Stack: push/pop --> [A][B][C] <-- top
Queue: remove <-- [A][B][C] <-- append
```
For very large queues, a head-index or dedicated deque can avoid repeated shifting.

## 11. let, const and freezing arrays

```javascript
const colors = ["Red"];
colors.push("Blue"); // allowed
// colors = []; // TypeError
let other = colors;
other = []; // allowed
const frozen = Object.freeze([{ count: 1 }]);
frozen[0].count = 2; // nested object is not frozen
```
`const` prevents reassignment of the binding, not mutation of its value. `Object.freeze` prevents changes to the array itself; it is shallow. Failed writes throw in strict mode, and many mutating methods throw even outside strict mode.

## 12. Array internals

```javascript
const a = [10, 20];
console.log(typeof a); // object
console.log(Array.isArray(a)); // true
console.log(a["0"]); // 10
a.label = "Scores";
a[-1] = 99;
a[1.5] = 42;
a["01"] = 7;
console.log(a.length); // 2
```
Array indexes are canonical integer property names from 0 through 2**32 - 2. Other properties do not affect length. Engines may optimize dense arrays differently from sparse arrays; contiguous memory is not guaranteed by JavaScript.

## 13. Empty slots and sparse arrays

```javascript
const a = [10, , undefined, null, 0];
console.log(a.length); // 5
console.log(1 in a); // false (assuming no inherited index)
console.log(Object.hasOwn(a, 2)); // true
console.log(a[1]); // undefined
console.log(a[2]); // undefined
let count = 0;
for (let i = 0; i < a.length; i++) {
    if (Object.hasOwn(a, i)) count++;
}
console.log(count); // 4
```
A hole is an absent own property, unlike an explicit undefined. `in` also checks inherited properties. `for...of` normally reads holes as undefined; `forEach` normally skips them. `new Array(5)`, length extension and delete can create holes. Null, zero, false and empty strings are actual values.

## 14. Performance and complexity

Typical costs for ordinary dense arrays (not ECMAScript guarantees):

| Operation | Typical time |
| --- | --- |
| Indexed read/write | O(1) |
| push | Amortized O(1) |
| pop | O(1) |
| shift/unshift | O(n) |
| Search or traversal | O(n) |
| Copy | O(n) |
| All pairs | O(n^2) |

Appending usually avoids moving existing elements. Removing the first element generally requires moving or logically reindexing later elements. Copying uses O(n) additional space; a simple traversal uses O(1) auxiliary space. Actual engine behavior depends on representation and operations.

## 15. Iterating over arrays

```javascript
const numbers = [10, 20, 30];
let sum = 0;
for (let i = 0; i < numbers.length; i++) {
    sum += numbers[i];
}
console.log(sum); // 60
```
Initialization runs once; the condition is checked before each iteration; the body runs when true; the update runs afterward.

| i | Value | Sum before | Sum after |
| --- | --- | --- | --- |
| 0 | 10 | 0 | 10 |
| 1 | 20 | 10 | 30 |
| 2 | 30 | 30 | 60 |

```javascript
for (let i = numbers.length - 1; i >= 0; i--) console.log(numbers[i]); // 30, 20, 10
for (let i = 0; i < numbers.length; i += 2) console.log(numbers[i]); // 10, 30
for (let i = 1; i < 3; i++) console.log(numbers[i]); // 20, 30
let i = 0;
while (i < numbers.length) console.log(numbers[i++]); // 10, 20, 30
let j = 0;
if (numbers.length) {
    do { console.log(numbers[j++]); } while (j < numbers.length);
}
for (const value of numbers) console.log(value);
for (const [index, value] of numbers.entries()) console.log(index, value);
for (const key in numbers) console.log(key); // string property names
```
Prefer indexed loops for positions, for...of for values, and entries for both. Avoid for...in for normal array traversal because it enumerates enumerable string properties, including inherited ones.

```javascript
for (let i = 0; i < numbers.length; i++) numbers[i] *= 2;
for (const value of numbers) {
    if (value === 20) continue;
    if (value === 60) break;
    console.log(value); // 40
}
```
`continue` skips the remaining body; `break` exits the loop. Assigning to a loop variable does not replace the corresponding array element.

## 16. References and identity

```javascript
const original = [1, 2];
let alias = original;
alias.push(3);
console.log(original); // [1, 2, 3]
console.log(alias === original); // true
alias = [];
console.log(original); // [1, 2, 3]
function change(list) {
    list.push(4);
    list = []; // only changes local binding
}
change(original);
console.log(original); // [1, 2, 3, 4]
```
Assignment copies the reference value, not the array. JavaScript passes arguments by value, including reference values. Clearing via length = 0 affects all aliases; reassignment does not.

## 17. Shallow and deep copying

```javascript
const original = [1, 2, 3];
const a = [...original];
const b = original.slice();
const c = Array.from(original);
const d = [].concat(original);
const e = [];
for (const value of original) e.push(value);
```
These are shallow copies (their treatment of holes can differ).

```javascript
const books = [{ title: "Atlas" }];
const copy = [...books];
copy[0].title = "Maps";
console.log(books[0].title); // Maps
const deep = structuredClone(books);
deep[0].title = "World";
console.log(books[0].title); // Maps
```
Structured cloning supports many built-in types and cycles, but not functions and certain host objects. JSON.parse(JSON.stringify(value)) loses or changes unsupported values, dates and holes, rejects BigInt and cycles, and is not a general deep clone.

## 18. Spread syntax

```javascript
const a = [1, 2];
const b = [3, 4];
const merged = [...a, ...b];
const extended = [...a, 5];
console.log(merged); // [1, 2, 3, 4]
console.log(extended); // [1, 2, 5]
console.log(Math.max(...merged)); // 4
```
Spread expands an iterable into elements or arguments. It is shallow. Very large argument spreads may exceed engine limits; use a loop for large inputs.

## 19. Array destructuring

```javascript
const [first, , third] = [10, 20, 30];
const [name = "Unknown"] = [];
const [head, ...tail] = [1, 2, 3];
let x = 5, y = 9;
[x, y] = [y, x];
const [[a, b], c] = [[1, 2], 3];
console.log(first, third, name, head, tail); // 10 30 Unknown 1 [2, 3]
console.log(x, y, a, b, c); // 9 5 1 2 3
```
Commas can skip positions. Defaults apply only to undefined. Rest collects remaining values and must be last. Nested patterns unpack nested iterables.

## 20. Mutable and immutable operations

```javascript
const a = [3, 1, 2];
console.log(a.with(1, 9)); // [3, 9, 2]
console.log(a.toSpliced(1, 1, 8)); // [3, 8, 2]
console.log(a.toSorted((x, y) => x - y)); // [1, 2, 3]
console.log(a.toReversed()); // [2, 1, 3]
console.log(a); // [3, 1, 2]
```
These modern methods return new shallow arrays. `with` throws RangeError for an out-of-range index. Traditional splice, sort, reverse, fill, copyWithin, push, pop, shift and unshift mutate the original. Check runtime support for newer methods.

## 21. Arrays of objects

```javascript
const books = [{ id: 1, title: "Atlas", pages: 120 }, { id: 2, title: "Ocean", pages: 200 }];
books.push({ id: 3, title: "Forest", pages: 150 });
for (const book of books) {
    if (book.id === 2) book.pages = 210;
}
for (let i = 0; i < books.length; i++) {
    if (books[i].id === 1) {
        books.splice(i, 1);
        break;
    }
}
console.log(books[0].title); // Ocean
```
The array organizes records; objects describe each record. CRUD means create, read, update and delete. IDs are generally more stable than array positions.

## 22. Objects containing arrays

```javascript
const student = { name: "Anna", subjects: ["Math", "Science"] };
student.subjects.push("History");
for (const subject of student.subjects) console.log(subject);
// Math, Science, History
```
Use dot notation for named properties and bracket notation for indexed positions. `student.subjects[0]` reads the first subject.

## 23. Multidimensional arrays

```javascript
const matrix = [[1, 2, 3], [4, 5, 6]];
console.log(matrix[1][2]); // 6
console.log(matrix.length); // 2
console.log(matrix[0].length); // 3
let total = 0;
for (let row = 0; row < matrix.length; row++) {
    for (let col = 0; col < matrix[row].length; col++) {
        total += matrix[row][col];
    }
}
console.log(total); // 21
```
```text
       col 0  col 1  col 2
row 0    1      2      3
row 1    4      5      6
```
| Row | Column | Value | Running total |
| --- | --- | --- | --- |
| 0 | 0 | 1 | 1 |
| 0 | 1 | 2 | 3 |
| 0 | 2 | 3 | 6 |
| 1 | 0 | 4 | 10 |
| 1 | 1 | 5 | 15 |
| 1 | 2 | 6 | 21 |

```javascript
matrix.push([7, 8, 9]);
const warehouse = [[[10, 20], [30, 40]], [[50, 60], [70, 80]]];
console.log(warehouse[1][0][1]); // 60
```
Three-dimensional arrays require three index operations. JavaScript represents these as nested arrays, not a special matrix type.

## 24. Jagged and deeply nested arrays

```javascript
const rows = [[1], [2, 3], [], [4, 5, 6]];
for (const row of rows) {
    for (const value of row) console.log(value);
}
const deep = [1, [2, [3, 4]]];
function visit(values) {
    for (const value of values) {
        if (Array.isArray(value)) visit(value);
        else console.log(value);
    }
}
visit(deep); // 1, 2, 3, 4
```
Jagged rows have different lengths. Recursion handles variable nesting but needs cycle protection for cyclic inputs and can exhaust the call stack.

```javascript
const wrong = Array(3).fill([]);
wrong[0].push(1);
console.log(wrong); // [[1], [1], [1]]
const right = Array.from({ length: 3 }, () => []);
right[0].push(1);
console.log(right); // [[1], [], []]
```

## 25. Constructors and static methods

```javascript
console.log(new Array(3)); // three empty slots
console.log(Array.of(3)); // [3]
console.log(Array.from("abc")); // ["a", "b", "c"]
console.log(Array.from({ length: 4 }, (_, i) => i * 2)); // [0, 2, 4, 6]
console.log(Array.isArray([])); // true
```
`Array.from(source, mapFn?, thisArg?)` converts iterables or array-like objects, optionally transforming each value. `Array.of(...items)` creates an array from arguments regardless of a single numeric argument. `Array.isArray` reliably identifies arrays across realms.

```javascript
async function* values() {
    yield 10;
    yield 20;
}
async function main() {
    console.log(await Array.fromAsync(values())); // [10, 20]
}
main();
```
`Array.fromAsync` returns a promise for an array and supports asynchronous iteration. Async functions and generators are covered later.

## 26. Conversion and coercion

```javascript
console.log([1, 2, 3].toString()); // "1,2,3"
console.log([1, 2, 3].join(" - ")); // "1 - 2 - 3"
console.log("red,blue".split(",")); // ["red", "blue"]
console.log([...new Set([1, 1, 2])]); // [1, 2]
console.log(Object.fromEntries([["a", 1], ["b", 2]])); // {a: 1, b: 2}
console.log([1, 2] + [3, 4]); // "1,23,4"
console.log(Boolean([])); // true
```
`+` is not array concatenation. `split("")` splits UTF-16 code units, not necessarily complete Unicode characters. Spread on a string iterates code points, not necessarily whole grapheme clusters.

## 27. Comparison and equality

```javascript
console.log([1, 2] === [1, 2]); // false
console.log([] == 0); // true (coercion)
function equal(a, b) {
    if (!Array.isArray(a) || !Array.isArray(b) || a.length !== b.length) return false;
    for (let i = 0; i < a.length; i++) {
        if (Object.hasOwn(a, i) !== Object.hasOwn(b, i)) return false;
        if (!Object.is(a[i], b[i])) return false;
    }
    return true;
}
console.log(equal([1, 2], [1, 2])); // true
```
Strict equality compares object identity. Loose equality may coerce arrays to primitives. The function compares shallow values, distinguishes holes and uses Object.is (NaN equals NaN, positive and negative zero differ).

```javascript
function nestedEqual(a, b) {
    if (Object.is(a, b)) return true;
    if (!Array.isArray(a) || !Array.isArray(b) || a.length !== b.length) return false;
    for (let i = 0; i < a.length; i++) {
        if (Object.hasOwn(a, i) !== Object.hasOwn(b, i)) return false;
        if (Object.hasOwn(a, i) && !nestedEqual(a[i], b[i])) return false;
    }
    return true;
}
console.log(nestedEqual([1, [2]], [1, [2]])); // true
```
This handles acyclic nested arrays, not general deep object equality or cyclic structures.

## 28. Validating arrays

```javascript
function validNumbers(values) {
    if (!Array.isArray(values)) return false;
    for (let i = 0; i < values.length; i++) {
        if (!Object.hasOwn(values, i) || !Number.isFinite(values[i])) return false;
    }
    return true;
}
console.log(validNumbers([1, 2])); // true
console.log(validNumbers([1, , 2])); // false
console.log(validNumbers([1, NaN])); // false
```
Check array identity before length. An empty array has length zero; decide whether emptiness is valid for the task. For records, validate each element is a non-null object with required fields and expected types.

```javascript
function validBook(book) {
    return book !== null && typeof book === "object" && !Array.isArray(book)
        && Number.isInteger(book.id) && typeof book.title === "string";
}
```

## 29. Common mistakes and edge cases

| Mistake | Correction |
| --- | --- |
| First index is 1 | Start at 0 |
| Last index is length | Use length - 1 |
| Loop condition i <= length | Use i < length |
| Assigning push result to array | push returns length |
| Assignment creates a copy | Use an appropriate copy operation |
| Comparing arrays with === for contents | Compare elements |
| const means immutable | const prevents reassignment |
| delete removes and shifts | Use splice for removal |
| Forward splice skips elements | Iterate backward or adjust index |
| Empty array is falsy | Check length |
| push(array) merges values | Use push(...array) |
| fill([]) creates independent rows | Use Array.from with a factory |
| new Array(5) creates [5] | Use Array.of(5) |
| Spread deeply copies objects | Spread is shallow |

```javascript
const a = [2, 2, 3, 2];
for (let i = a.length - 1; i >= 0; i--) {
    if (a[i] === 2) a.splice(i, 1);
}
console.log(a); // [3]
```

## 30. Summary and capability reference

| Syntax | Purpose |
| --- | --- |
| [] | Create |
| a[i] | Read/write |
| a.length | Length |
| a.at(-1) | Last element |
| [...a] | Shallow copy |
| [first, ...rest] = a | Destructure |
| Array.isArray(a) | Validate type |

### Array method inventory

| Family | Methods |
| --- | --- |
| Access | at |
| Add/remove | push, pop, shift, unshift, splice, toSpliced |
| Copy/combine | slice, concat, with |
| Search | includes, indexOf, lastIndexOf, find, findIndex, findLast, findLastIndex |
| Test | every, some |
| Transform | map, filter, flat, flatMap |
| Accumulate | reduce, reduceRight |
| Traverse | forEach, keys, values, entries, Symbol.iterator |
| Reorder | sort, reverse, toSorted, toReversed |
| Overwrite | fill, copyWithin |
| Convert | join, toString, toLocaleString |
| Static | isArray, of, from, fromAsync |

### Problem-solving capabilities

| Capability | Typical approach |
| --- | --- |
| Create/access/update | Literal, indexing |
| Insert/delete | push, splice, immutable alternatives |
| Traverse/search | Loops, find |
| Filter/count | Conditional loops, filter |
| Aggregate | Accumulator, reduce |
| Transform | Loop, map |
| Sort | Comparator, sort |
| Group/frequency | Object or Map accumulator |
| Deduplicate | Set or membership check |
| Compare | Element-by-element comparison |
| Merge | Spread, concat |
| Flatten | Nested loops, flat |
| Partition | Two accumulators |
| Validate | Conditions, every (beware holes) |
| Rotate | Slice and concatenate |
| Matrix processing | Nested loops |

Individual method signatures, callback details and plain-loop equivalents belong to the next chapter.

## 31. Progressive exercises

Practice independently before consulting solutions. Test empty arrays, one-element arrays, duplicates and boundary indexes where relevant.

### Beginner

1. Create an array of five colors.
2. Read the first and last colors.
3. Replace the second color.
4. Append two colors.
5. Remove the last color and print it.
6. Prepend a color.
7. Remove the first color.
8. Print length and last index.
9. Read the second-last element with at.
10. Clear an array without reassigning it.

### Basic problem solving

11. Print every number using for.
12. Print values in reverse order.
13. Print every second element.
14. Sum [4, 7, 9]. Expected: 20.
15. Find the largest value in [4, 7, 9]. Expected: 9.
16. Count even numbers in [1, 2, 4, 5]. Expected: 2.
17. Collect positive numbers from [-2, 0, 3, 5]. Expected: [3, 5].
18. Find the first index of 7 in [4, 7, 7]. Expected: 1.
19. Count occurrences of 7 in [4, 7, 7]. Expected: 2.
20. Reverse an array into a new array.

### Intermediate

21. Copy an array without sharing its outer identity.
22. Demonstrate shared nested objects after a shallow copy.
23. Deep-clone a nested record.
24. Merge two arrays without mutating either.
25. Remove duplicates from [1, 2, 1, 3]. Expected: [1, 2, 3].
26. Partition numbers into even and odd arrays.
27. Build a frequency table for ["a", "b", "a"].
28. Rotate [1, 2, 3, 4] left once. Expected: [2, 3, 4, 1].
29. Compare two arrays by content.
30. Validate a dense array of finite numbers.

### Complex structures

31. Create three independent rows.
32. Sum a 2D matrix.
33. Calculate each row sum.
34. Calculate each column sum in a rectangular matrix.
35. Find the largest value in a jagged array.
36. Count all present values in jagged rows.
37. Transpose a rectangular matrix.
38. Flatten one nesting level using loops.
39. Traverse a three-dimensional array.
40. Recursively flatten an acyclic deeply nested array.

### Advanced

41. Implement a stack with undo.
42. Implement a FIFO queue.
43. Implement a queue with a head index.
44. Implement record creation and lookup by ID.
45. Update a record by ID without changing the original array or record.
46. Delete every matching record without skipping adjacent matches.
47. Compare sparse arrays while distinguishing holes from undefined.
48. Find all pairs whose sum equals a target.
49. Find the longest consecutive increasing run.
50. Find the maximum contiguous subarray sum in linear time.

### Five additional challenges

1. Is array copied? Given `let fruits = ["Apples", "Pear", "Orange"]; let shoppingCart = fruits; shoppingCart.push("Banana");`, predict fruits.length. Expected: 4.
2. Array operations: start with Jazz and Blues, append Rock-n-Roll, replace the middle with Classics, remove the first, then prepend Rap and Reggae. Expected: ["Rap", "Reggae", "Classics", "Rock-n-Roll"].
3. Calling in an array context: `let arr = ["a", "b"]; arr.push(function () { console.log(this); }); arr[2]();` Explain why this is the array when called as a method.
4. Sum input numbers: repeatedly prompt for numbers until cancel, empty input or invalid input, then return their sum. Example: 2, 5, -1 gives 6.
5. Maximal subarray: find the maximum contiguous sum, allowing an empty subarray with sum zero. For [-1, 2, 3, -9], expected 5; for [-1, -2], expected 0. Try a quadratic solution, then a linear running-sum solution.

### References

- [JavaScript.info: Arrays](https://javascript.info/array)
- [JavaScript.info: Array methods](https://javascript.info/array-methods)
- [MDN: Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN: Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring)
- [MDN: Spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
- [ECMAScript: Indexed collections](https://tc39.es/ecma262/#sec-indexed-collections)

Deferred: exhaustive individual array methods, advanced iteration protocols, typed arrays, and full exercise solutions.

---

[Back to table of contents](#table-of-contents) | [Next: Array methods](https://javascript.info/array-methods)
