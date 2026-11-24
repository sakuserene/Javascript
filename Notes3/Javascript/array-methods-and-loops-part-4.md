# JavaScript Array Methods and Plain Loops

## Part 4 - Searching arrays and advanced search algorithms

In the previous part, we learned how to copy, extract, merge, rotate and traverse arrays. We also introduced two-pointer algorithms, sliding windows and circular indexing.

We will now concentrate on searching.

Searching is not limited to determining whether an element exists in an array. Depending on the problem, we might need to find its position, retrieve a matching object, locate multiple occurrences, identify a sequence, find an insertion position or search efficiently through millions of sorted elements.

JavaScript provides several methods for searching arrays, but understanding plain loops allows us to construct more specialized searching algorithms ourselves.

[Previous: Part 3](array-methods-and-loops-part-3.md) | [Next: Part 5](array-methods-and-loops-part-5.md)

## Table of contents
1. Understanding searching
2. Equality and comparison mechanisms
3. indexOf()
4. lastIndexOf()
5. includes()
6. find()
7. findIndex()
8. findLast()
9. findLastIndex()
10. Searching within specified ranges
11. Searching for multiple occurrences
12. Searching arrays of objects
13. Searching for multiple target values
14. Searching for consecutive sequences
15. Finding the closest matching value
16. Linear search
17. Binary search
18. Lower and upper bounds
19. Searching rotated sorted arrays
20. Advanced searching algorithms
21. Performance and complexity
22. Common mistakes
23. Progressive exercises and selected solutions
24. Coverage tracker

## 1. Understanding searching

```javascript
const identifiers = [104, 208, 315, 427, 532];
const target = 315;
let found = false;

for (let i = 0; i < identifiers.length; i++) {
    if (identifiers[i] === target) {
        found = true;
        break;
    }
}
console.log(found);
```

Output: `true`.

The flag initially assumes no match. Each iteration compares the current element with the target. A match sets the flag and terminates the loop.

| Index | Value | Comparison | Action |
|---|---|---|---|
| 0 | 104 | false | Continue |
| 1 | 208 | false | Continue |
| 2 | 315 | true | Stop |

| Requirement | Example |
|---|---|
| Existence | Does a value exist? |
| Position | At which index does it exist? |
| Retrieval | Which object matches a condition? |
| First occurrence | Where does the value first appear? |
| Last occurrence | Where does it last appear? |
| Multiple occurrences | Where are all matching positions? |
| Frequency | How many times does it appear? |
| Conditional search | Which value satisfies a condition? |
| Approximate search | Which value is closest to the target? |
| Sequence search | Does a consecutive pattern exist? |
| Boundary search | Where do matching values begin and end? |

## 2. Equality and comparison mechanisms

Strict equality compares without type conversion. Loose equality can convert types. Object.is distinguishes positive and negative zero and recognizes NaN. SameValueZero recognizes NaN and treats signed zeros as equal.

| Values | == | === | Object.is | SameValueZero |
|---|---|---|---|---|
| 10, 10 | true | true | true | true |
| 10, "10" | true | false | false | false |
| 0, false | true | false | false | false |
| NaN, NaN | false | false | true | true |
| 0, -0 | true | true | false | true |
| null, undefined | true | false | false | false |
| [], [] | false | false | false | false |
| {}, {} | false | false | false | false |

```javascript
function sameValueZero(a, b) {
    return a === b || (Number.isNaN(a) && Number.isNaN(b));
}
const first = { id: 101 };
const second = { id: 101 };
console.log(first === second); // false
console.log(first === first); // true
```

Objects compare by identity, not contents.

## 3. indexOf()

Syntax: `array.indexOf(searchElement, fromIndex)`.

Returns the first matching index or -1. Uses strict equality, skips absent positions, searches forward. Omitted searchElement is undefined; fromIndex defaults to zero.

```javascript
const countries = ["Kenya", "Japan", "Canada", "Brazil"];
console.log(countries.indexOf("Canada")); // 2
console.log(countries.indexOf("Germany")); // -1
const numbers = [15, 30, 45, 30, 60, 30];
console.log(numbers.indexOf(30)); // 1
console.log(numbers.indexOf(30, 2)); // 3
console.log(numbers.indexOf(30, 4)); // 5
console.log(numbers.indexOf(30, 6)); // -1
console.log(numbers.indexOf(30, -3)); // 3
console.log(numbers.indexOf(30, -2)); // 5
console.log(numbers.indexOf(30, -100)); // 1
```

Negative starting positions are calculated relative to length and clamped to zero. The second argument is a starting position, not a count.

Given `[10,20,30,20,40]`:

| Expression | Result |
|---|---:|
| indexOf(20) | 1 |
| indexOf(20, 1) | 1 |
| indexOf(20, 2) | 3 |
| indexOf(20, -2) | 3 |
| indexOf(20, -100) | 1 |
| indexOf(20, 100) | -1 |
| indexOf(20, 1.9) | 1 |
| indexOf(20, NaN) | 1 |
| indexOf(20, Infinity) | -1 |
| indexOf(20, -Infinity) | 1 |
| indexOf("20") | -1 |

```javascript
function toInteger(value) {
    const number = +value;
    if (Number.isNaN(number) || number === 0) {
        return 0;
    }
    return Math.trunc(number);
}

function manualIndexOf(array, target, fromIndex = 0) {
    const length = array.length;
    let start = toInteger(fromIndex);
    if (start < 0) {
        start = Math.max(length + start, 0);
    }
    for (let i = start; i < length; i++) {
        if (i in array && array[i] === target) {
            return i;
        }
    }
    return -1;
}
console.log(manualIndexOf([10, , 30, undefined], undefined)); // 3
```

The `in` operator checks whether the indexed property exists. Returning immediately terminates the function and loop.

## 4. lastIndexOf()

Syntax: `array.lastIndexOf(searchElement, fromIndex)`.

Searches backward using strict equality, skips absent positions, returns -1 if unsuccessful. Omitted fromIndex defaults to length - 1.

```javascript
const signals = ["ON", "OFF", "ON", "OFF", "ON"];
console.log(signals.indexOf("ON")); // 0
console.log(signals.lastIndexOf("ON")); // 4
const activities = ["START", "PAUSE", "RESUME", "PAUSE", "RESUME", "STOP"];
console.log(activities.lastIndexOf("PAUSE")); // 3
const numbers = [10, 20, 30, 20, 40, 20];
console.log(numbers.lastIndexOf(20)); // 5
console.log(numbers.lastIndexOf(20, 4)); // 3
console.log(numbers.lastIndexOf(20, 2)); // 1
console.log(numbers.lastIndexOf(20, 0)); // -1
console.log(numbers.lastIndexOf(20, -2)); // 3
console.log(numbers.lastIndexOf(20, -4)); // 1
console.log(numbers.lastIndexOf(20, -100)); // -1
```

```javascript
function manualLastIndexOf(array, target, ...args) {
    const length = array.length;
    if (length === 0) return -1;
    let start = args.length === 0 ? length - 1 : toInteger(args[0]);
    if (start < 0) {
        start = length + start;
    } else {
        start = Math.min(start, length - 1);
    }
    for (let i = start; i >= 0; i--) {
        if (i in array && array[i] === target) return i;
    }
    return -1;
}
console.log([10,20,10].lastIndexOf(10)); // 2
console.log([10,20,10].lastIndexOf(10, undefined)); // 0
```

Rest parameters distinguish omitted fromIndex from explicit undefined.

## 5. includes()

Syntax: `array.includes(searchElement, fromIndex)`; returns Boolean, uses SameValueZero, reads absent positions as undefined in ordinary arrays.

```javascript
const supportedFormats = ["jpg", "png", "webp", "gif"];
const extension = "webp";
console.log(supportedFormats.includes(extension)); // true
let supported = false;
for (const format of supportedFormats) {
    if (format === extension) {
        supported = true;
        break;
    }
}
console.log(supported); // true
const tags = ["javascript", "html", "css"];
if (!tags.includes("javascript")) tags.push("javascript");
console.log(tags); // ["javascript", "html", "css"]
console.log([12, NaN, 24, 30].indexOf(NaN)); // -1
console.log([12, NaN, 24, 30].includes(NaN)); // true
console.log([10, , 30].indexOf(undefined)); // -1
console.log([10, , 30].includes(undefined)); // true
```

```javascript
function manualIncludes(array, target, fromIndex = 0) {
    const length = array.length;
    let start = toInteger(fromIndex);
    if (start < 0) start = Math.max(length + start, 0);
    for (let i = start; i < length; i++) {
        if (sameValueZero(array[i], target)) return true;
    }
    return false;
}
const values = [10, NaN, , 40];
console.log(manualIncludes(values, 10)); // true
console.log(manualIncludes(values, NaN)); // true
console.log(manualIncludes(values, undefined)); // true
console.log(manualIncludes(values, 100)); // false
console.log(manualIncludes(values, 10, 1)); // false
```

## 6. find()

Syntax: `array.find(callback, thisArg)`. Callback receives element, index and original array. Returns the first element whose callback result is truthy, or undefined. Captures initial length, visits holes, and stops at the first match.

```javascript
const temperatures = [18, 22, 27, 34, 31, 40];
console.log(temperatures.find(temperature => temperature > 30)); // 34
```

| Index | Value | Condition | Action |
|---|---|---|---|
| 0 | 18 | false | Continue |
| 1 | 22 | false | Continue |
| 2 | 27 | false | Continue |
| 3 | 34 | true | Return 34 |
| 4, 5 | 31, 40 | Not evaluated | Stop |

```javascript
const products = [
    { id: 101, name: "Notebook", stock: 12 },
    { id: 102, name: "Marker", stock: 3 },
    { id: 103, name: "Folder", stock: 8 },
    { id: 104, name: "Ruler", stock: 2 }
];
console.log(products.find(item => item.stock < 5));
// { id: 102, name: "Marker", stock: 3 }
const books = [
    { title: "Book A", pages: 180, available: true },
    { title: "Book B", pages: 420, available: false },
    { title: "Book C", pages: 350, available: true },
    { title: "Book D", pages: 500, available: true }
];
console.log(books.find(item => item.pages >= 300 && item.available).title); // Book C
console.log([-5,10,-8,20,-12,30].find((value,index) => index > 2 && value < 0)); // -12
console.log([30,25,20,28,35].find((value,index,array) => index > 0 && value > array[index-1])); // 28
console.log([0,"",null,15,20].find(value => value)); // 15
console.log(products.find(item => item.id === 999)?.name ?? "Product not found");
```

find returns a reference, not a clone. Accessing properties on an unsuccessful result throws TypeError unless guarded.

```javascript
function manualFind(array, callback, thisArg) {
    const length = array.length;
    if (typeof callback !== "function") {
        throw new TypeError("Callback must be a function");
    }
    for (let i = 0; i < length; i++) {
        const value = array[i];
        if (callback.call(thisArg, value, i, array)) return value;
    }
    return undefined;
}
const numbers = [10,20,30];
const result = numbers.find((value,index,array) => {
    if (index === 0) {
        array[index] = 99;
        return true;
    }
    return false;
});
console.log(result); // 10
console.log(numbers); // [99,20,30]
[10, , 30].find((value,index) => {
    console.log(index,value);
    return false;
});
// 0 10; 1 undefined; 2 30
```

The value is retrieved before callback execution, so storing it preserves the returned value even if the callback replaces the element.

## 7. findIndex()

Syntax: `array.findIndex(callback, thisArg)`. Returns the first matching index or -1.

```javascript
const numbers = [12,18,24,35,42];
console.log(numbers.find(number => number > 30)); // 35
console.log(numbers.findIndex(number => number > 30)); // 3
const tasks = [{id:101,completed:false},{id:102,completed:false},{id:103,completed:true}];
const index = tasks.findIndex(task => task.id === 102);
if (index !== -1) tasks[index].completed = true;
const documents = [{id:1,title:"Introduction"},{id:2,title:"Chapter One"},{id:3,title:"Chapter Two"}];
const position = documents.findIndex(document => document.id === 2);
if (position !== -1) documents.splice(position,1);
```

```javascript
function manualFindIndex(array, callback, thisArg) {
    const length = array.length;
    if (typeof callback !== "function") throw new TypeError("Callback must be a function");
    for (let i = 0; i < length; i++) {
        if (callback.call(thisArg, array[i], i, array)) return i;
    }
    return -1;
}
console.log(manualFindIndex([5,12,8,25,30], number => number >= 20)); // 3
console.log([10,undefined,30].find(value => value === undefined)); // undefined
console.log([10,undefined,30].findIndex(value => value === undefined)); // 1
```

findIndex distinguishes a matching undefined from no match.

## 8. findLast()

Syntax: `array.findLast(callback, thisArg)`. Searches backward and returns the matching element or undefined.

```javascript
const operations = [{id:1,status:"success"},{id:2,status:"failed"},{id:3,status:"success"},{id:4,status:"failed"}];
console.log(operations.findLast(operation => operation.status === "success"));
// { id: 3, status: "success" }
console.log([12,45,28,60,35,72].findLast(number => number >= 20 && number <= 50)); // 35
function manualFindLast(array, callback, thisArg) {
    const length = array.length;
    if (typeof callback !== "function") throw new TypeError("Callback must be a function");
    for (let i = length - 1; i >= 0; i--) {
        const value = array[i];
        if (callback.call(thisArg, value, i, array)) return value;
    }
    return undefined;
}
```

## 9. findLastIndex()

Syntax: `array.findLastIndex(callback, thisArg)`. Returns the last matching index or -1.

```javascript
const readings = [12,35,18,42,25,10];
console.log(readings.findIndex(value => value > 30)); // 1
console.log(readings.findLastIndex(value => value > 30)); // 3
function manualFindLastIndex(array, callback, thisArg) {
    const length = array.length;
    if (typeof callback !== "function") throw new TypeError("Callback must be a function");
    for (let i = length - 1; i >= 0; i--) {
        if (callback.call(thisArg, array[i], i, array)) return i;
    }
    return -1;
}
```

| Method | Search | Direction | Returns | No match | Visits holes |
|---|---|---|---|---|---|
| indexOf | Value | Forward | Index | -1 | No |
| lastIndexOf | Value | Backward | Index | -1 | No |
| includes | Value | Forward | Boolean | false | Yes |
| find | Callback | Forward | Element | undefined | Yes |
| findIndex | Callback | Forward | Index | -1 | Yes |
| findLast | Callback | Backward | Element | undefined | Yes |
| findLastIndex | Callback | Backward | Index | -1 | Yes |

Manual implementations are educational approximations for ordinary arrays, not complete ECMAScript polyfills.

## 10. Searching within specified ranges

Native searching methods do not all support both start and end boundaries. Plain loops allow precise control.

```javascript
function searchRange(array, target, start, end) {
    if (!Number.isInteger(start) || !Number.isInteger(end) ||
        start < 0 || end > array.length || start > end) {
        throw new RangeError("Invalid search range");
    }
    for (let i = start; i < end; i++) {
        if (array[i] === target) {
            return i;
        }
    }
    return -1;
}
const numbers = [10, 20, 30, 20, 40, 20];
console.log(searchRange(numbers, 20, 2, 5)); // 3
console.log(searchRange(numbers, 20, 4, 5)); // -1
```

The ending index is exclusive. A conditional range search replaces the equality comparison with a predicate:

```javascript
function findIndexInRange(array, callback, start, end) {
    if (!Number.isInteger(start) || !Number.isInteger(end) ||
        start < 0 || end > array.length || start > end) {
        throw new RangeError("Invalid search range");
    }
    for (let i = start; i < end; i++) {
        if (callback(array[i], i, array)) {
            return i;
        }
    }
    return -1;
}
console.log(findIndexInRange([15, 38, 22, 26, 35, 40], value => value > 30, 2, 5)); // 4
```

## 11. Searching for multiple occurrences

```javascript
const numbers = [10, 20, 30, 20, 40, 20];
const indexes = [];
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] === 20) {
        indexes[indexes.length] = i;
    }
}
console.log(indexes); // [1, 3, 5]
```

Do not break after a match when collecting all occurrences.

| Index | Value | Collected indexes |
|---|---|---|
| 0 | 10 | [] |
| 1 | 20 | [1] |
| 2 | 30 | [1] |
| 3 | 20 | [1, 3] |
| 4 | 40 | [1, 3] |
| 5 | 20 | [1, 3, 5] |

Using repeated indexOf():

```javascript
const numbers = [10, 20, 30, 20, 40, 20];
const indexes = [];
let position = numbers.indexOf(20);
while (position !== -1) {
    indexes[indexes.length] = position;
    position = numbers.indexOf(20, position + 1);
}
console.log(indexes); // [1, 3, 5]
```

The next search starts after the previous match. For unchanged ordinary arrays, the searched portions do not overlap.

```javascript
function findAllIndexes(array, predicate) {
    const result = [];
    const length = array.length;
    for (let i = 0; i < length; i++) {
        if (predicate(array[i], i, array)) {
            result[result.length] = i;
        }
    }
    return result;
}
console.log(findAllIndexes([12, 35, 18, 42, 25, 10], value => value > 20)); // [1, 3, 4]

function nthIndexOf(array, target, occurrence) {
    if (!Number.isSafeInteger(occurrence) || occurrence <= 0) {
        throw new RangeError("Invalid occurrence");
    }
    let count = 0;
    for (let i = 0; i < array.length; i++) {
        if (i in array && array[i] === target) {
            count++;
            if (count === occurrence) {
                return i;
            }
        }
    }
    return -1;
}
console.log(nthIndexOf([10, 20, 30, 20, 40, 20], 20, 3)); // 5

function findBoundaries(array, target) {
    let first = -1;
    let last = -1;
    for (let i = 0; i < array.length; i++) {
        if (i in array && array[i] === target) {
            if (first === -1) {
                first = i;
            }
            last = i;
        }
    }
    return { first, last };
}
console.log(findBoundaries([10, 20, 30, 20, 40, 20], 20)); // { first: 1, last: 5 }

function searchStatistics(array, target) {
    let first = -1;
    let last = -1;
    let count = 0;
    const indexes = [];
    for (let i = 0; i < array.length; i++) {
        if (sameValueZero(array[i], target)) {
            if (first === -1) {
                first = i;
            }
            last = i;
            count++;
            indexes[indexes.length] = i;
        }
    }
    return { found: count > 0, first, last, count, indexes };
}
console.log(searchStatistics([10, 20, 30, 20, 40, 20], 20));
```

Output:

```text
{ found: true, first: 1, last: 5, count: 3, indexes: [1, 3, 5] }
```

## 12. Searching arrays of objects

```javascript
const employees = [
    { id: 101, name: "Alice", department: "Engineering", active: true },
    { id: 102, name: "Brian", department: "Design", active: false },
    { id: 103, name: "Clara", department: "Engineering", active: true }
];
console.log(employees.find(item => item.id === 103).name); // Clara
let employee;
for (const item of employees) {
    if (item.id === 103) {
        employee = item;
        break;
    }
}
console.log(employee?.name); // Clara
console.log(employees.find(item => item.department === "Engineering" && item.active === true).name); // Alice

const names = ["Alexander", "Beatrice", "Charlotte"];
const normalizedTarget = "BEATRICE".toLowerCase();
console.log(names.find(name => name.toLowerCase() === normalizedTarget)); // Beatrice

const titles = ["Introduction to Programming", "Understanding JavaScript", "Modern Web Development"];
console.log(titles.find(title => title.toLowerCase().includes("javascript"))); // Understanding JavaScript

const customers = [{ id: 1, address: { city: "Tokyo" } }, { id: 2 }, { id: 3, address: { city: "Oslo" } }];
console.log(customers.find(item => item.address?.city === "Oslo").id); // 3

function findByProperty(array, property, target) {
    for (const item of array) {
        if (item?.[property] === target) {
            return item;
        }
    }
    return undefined;
}
console.log(findByProperty(employees, "id", 102));
```

Normalize the target once outside the callback. Optional chaining handles missing nested properties. Dynamic bracket notation allows the property name to be supplied as an argument.

## 13. Searching for multiple target values

```javascript
const measurements = [12, 18, 25, 30, 42];
const prohibited = [25, 42];
console.log(measurements.find(value => prohibited.includes(value))); // 25
let result;
search:
for (let i = 0; i < measurements.length; i++) {
    for (let j = 0; j < prohibited.length; j++) {
        if (measurements[i] === prohibited[j]) {
            result = measurements[i];
            break search;
        }
    }
}
console.log(result); // 25
```

Collecting all matching targets:

```javascript
const numbers = [10, 20, 30, 40, 50, 20];
const targets = [20, 40];
const matches = [];
for (const number of numbers) {
    for (const target of targets) {
        if (number === target) {
            matches[matches.length] = number;
            break;
        }
    }
}
console.log(matches); // [20, 40, 20]
```

The inner break prevents duplicate target entries from duplicating a source match. Using Set:

```javascript
const targets = new Set([20, 40]);
const matches = [];
for (const number of [10, 20, 30, 40, 50, 20]) {
    if (targets.has(number)) {
        matches[matches.length] = number;
    }
}
console.log(matches); // [20, 40, 20]
```

Set membership is typically constant time on average in hash-based implementations, but ECMAScript does not guarantee constant time.

## 14. Searching for consecutive sequences

```javascript
function findSequence(array, pattern) {
    if (pattern.length === 0) {
        return 0;
    }
    for (let start = 0; start <= array.length - pattern.length; start++) {
        let matched = true;
        for (let j = 0; j < pattern.length; j++) {
            if (array[start + j] !== pattern[j]) {
                matched = false;
                break;
            }
        }
        if (matched) {
            return start;
        }
    }
    return -1;
}
console.log(findSequence(["A", "B", "C", "B", "C", "D", "A", "B"], ["B", "C", "D"])); // 3
```

The outer loop selects a candidate starting position. The inner loop checks the pattern. `start + j` converts the relative pattern index to a source index.

| Start | Window | Match |
|---|---|---|
| 0 | A B C | No |
| 1 | B C B | No |
| 2 | C B C | No |
| 3 | B C D | Yes |

```javascript
function findAllSequences(array, pattern) {
    const indexes = [];
    if (pattern.length === 0) {
        return indexes;
    }
    for (let start = 0; start <= array.length - pattern.length; start++) {
        let matched = true;
        for (let j = 0; j < pattern.length; j++) {
            if (array[start + j] !== pattern[j]) {
                matched = false;
                break;
            }
        }
        if (matched) {
            indexes[indexes.length] = start;
        }
    }
    return indexes;
}
console.log(findAllSequences([1, 2, 1, 2, 1, 2], [1, 2])); // [0, 2, 4]
console.log(findAllSequences([1, 1, 1, 1], [1, 1])); // [0, 1, 2]
```

Advancing one position includes overlapping matches. Advancing by the pattern length after a match excludes them. Empty-pattern behavior is a design decision: the first function returns 0, the second returns [].

## 15. Finding the closest matching value

```javascript
function findClosest(array, target) {
    if (array.length === 0) {
        return undefined;
    }
    let closest = array[0];
    let distance = Math.abs(closest - target);
    for (let i = 1; i < array.length; i++) {
        const currentDistance = Math.abs(array[i] - target);
        if (currentDistance < distance) {
            closest = array[i];
            distance = currentDistance;
        }
    }
    return closest;
}
console.log(findClosest([12, 18, 27, 35, 42], 30)); // 27
console.log(findClosest([10, 20, 30, 40], 26)); // 30
console.log(findClosest([10, 20, 30, 40], 25)); // 20
```

| Value | Absolute difference from 30 | Best candidate |
|---|---|---|
| 12 | 18 | 12 |
| 18 | 12 | 18 |
| 27 | 3 | 27 |
| 35 | 5 | 27 |
| 42 | 12 | 27 |

Math.abs measures distance regardless of direction. Equal distances retain the first candidate. Assumes dense arrays of finite numbers.

```javascript
function closestBelow(array, limit) {
    let best;
    for (const value of array) {
        if (value <= limit && (best === undefined || value > best)) {
            best = value;
        }
    }
    return best;
}
console.log(closestBelow([18, 42, 25, 36, 30], 34)); // 30
```

## 16. Linear search

```javascript
function linearSearch(array, target) {
    for (let i = 0; i < array.length; i++) {
        if (array[i] === target) {
            return i;
        }
    }
    return -1;
}
function bidirectionalSearch(array, target) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        if (array[left] === target) {
            return left;
        }
        if (right !== left && array[right] === target) {
            return right;
        }
        left++;
        right--;
    }
    return -1;
}
console.log(bidirectionalSearch([10, 20, 30, 40, 50], 40)); // 3
```

Linear search works on unsorted data. Best case O(1), worst case O(n). Bidirectional search changes inspection order but does not improve worst-case complexity or guarantee the first occurrence.

## 17. Binary search

Binary search repeatedly eliminates half of a sorted search range.

```javascript
function binarySearch(array, target) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] === target) {
            return middle;
        }
        if (array[middle] < target) {
            left = middle + 1;
        } else {
            right = middle - 1;
        }
    }
    return -1;
}
const numbers = [5, 12, 19, 26, 33, 40, 47, 54, 61, 68, 75, 82, 89, 96, 103];
console.log(binarySearch(numbers, 89)); // 12
console.log(binarySearch(numbers, 100)); // -1
```

| Iteration | Left | Right | Middle | Value | Action |
|---|---|---|---|---|---|
| 1 | 0 | 14 | 7 | 54 | Search right |
| 2 | 8 | 14 | 11 | 82 | Search right |
| 3 | 12 | 14 | 13 | 96 | Search left |
| 4 | 12 | 12 | 12 | 89 | Found |

`left` and `right` are inclusive boundaries. `Math.floor` produces an integer middle index. `middle + 1` and `middle - 1` discard the inspected position and the impossible half. The loop stops when the range is empty.

```javascript
console.log(binarySearch([50, 10, 80, 20, 30], 50)); // -1
```

Unsorted data invalidates the algorithm.

```javascript
function binarySearchDescending(array, target) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] === target) {
            return middle;
        }
        if (array[middle] > target) {
            left = middle + 1;
        } else {
            right = middle - 1;
        }
    }
    return -1;
}
console.log(binarySearchDescending([90, 70, 50, 30, 10], 30)); // 3

function binarySearchBy(array, target, compare) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        const middle = left + Math.floor((right - left) / 2);
        const order = compare(array[middle], target);
        if (order === 0) {
            return middle;
        }
        if (order < 0) {
            left = middle + 1;
        } else {
            right = middle - 1;
        }
    }
    return -1;
}
const records = [{ id: 101, name: "Alice" }, { id: 205, name: "Brian" }, { id: 310, name: "Clara" }];
console.log(binarySearchBy(records, 205, (record, target) => record.id - target)); // 1
```

The comparator must consistently return negative, zero or positive numbers according to the data's ordering.

| Elements | Linear maximum | Binary maximum |
|---|---:|---:|
| 10 | 10 | 4 |
| 100 | 100 | 7 |
| 1000 | 1000 | 10 |
| 10000 | 10000 | 14 |
| 100000 | 100000 | 17 |
| 1000000 | 1000000 | 20 |
| 1000000000 | 1000000000 | 30 |

## 18. Lower and upper bounds

Lower bound: first position with value greater than or equal to target. Upper bound: first position with value strictly greater than target.

```javascript
function lowerBound(array, target) {
    let left = 0;
    let right = array.length;
    while (left < right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] < target) {
            left = middle + 1;
        } else {
            right = middle;
        }
    }
    return left;
}
function upperBound(array, target) {
    let left = 0;
    let right = array.length;
    while (left < right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] <= target) {
            left = middle + 1;
        } else {
            right = middle;
        }
    }
    return left;
}
const numbers = [10, 20, 20, 20, 30, 40];
console.log(lowerBound(numbers, 20)); // 1
console.log(upperBound(numbers, 20)); // 4
```

```text
Index:  0   1   2   3   4   5
Value: 10  20  20  20  30  40
           ^           ^
         lower       upper
```

These algorithms use half-open ranges [left, right). The returned index may equal the array length.

```javascript
function firstOccurrence(array, target) {
    const index = lowerBound(array, target);
    if (index < array.length && array[index] === target) {
        return index;
    }
    return -1;
}
function lastOccurrence(array, target) {
    const index = upperBound(array, target) - 1;
    if (index >= 0 && array[index] === target) {
        return index;
    }
    return -1;
}
function countOccurrencesSorted(array, target) {
    return upperBound(array, target) - lowerBound(array, target);
}
console.log(firstOccurrence(numbers, 20)); // 1
console.log(lastOccurrence(numbers, 20)); // 3
console.log(countOccurrencesSorted(numbers, 20)); // 3
const values = [10, 20, 40, 50];
const position = lowerBound(values, 30);
values.splice(position, 0, 30);
console.log(values); // [10, 20, 30, 40, 50]
const readings = [10, 20, 20, 25, 30, 40, 40, 50];
console.log(upperBound(readings, 40) - lowerBound(readings, 20)); // 6
```

Finding insertion positions and counting sorted occurrences take O(log n). Inserting into the middle still typically takes O(n) because elements must shift.

## 19. Searching rotated sorted arrays

```javascript
function searchRotated(array, target) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] === target) {
            return middle;
        }
        if (array[left] <= array[middle]) {
            if (array[left] <= target && target < array[middle]) {
                right = middle - 1;
            } else {
                left = middle + 1;
            }
        } else {
            if (array[middle] < target && target <= array[right]) {
                left = middle + 1;
            } else {
                right = middle - 1;
            }
        }
    }
    return -1;
}
const numbers = [50, 60, 70, 10, 20, 30, 40];
console.log(searchRotated(numbers, 60)); // 1
console.log(searchRotated(numbers, 30)); // 5
console.log(searchRotated(numbers, 100)); // -1
```

At least one half is sorted when values are distinct. Determine the sorted half and whether the target belongs to it.

| Left | Right | Middle | Value | Action |
|---|---|---|---|---|
| 0 | 6 | 3 | 10 | Target 60 outside sorted right half |
| 0 | 2 | 1 | 60 | Found |

With duplicates, add this check immediately after the equality test in the loop:

```javascript
if (array[left] === array[middle] && array[middle] === array[right]) {
    left++;
    right--;
    continue;
}
```

Distinct values: O(log n). Duplicates may cause O(n) worst-case behavior.

## 20. Advanced searching algorithms

### Two-sum

```javascript
function twoSum(array, target) {
    for (let i = 0; i < array.length - 1; i++) {
        for (let j = i + 1; j < array.length; j++) {
            if (array[i] + array[j] === target) {
                return [i, j];
            }
        }
    }
    return undefined;
}
console.log(twoSum([3, 8, 4, 7, 12], 15)); // [0, 4]

function twoSumSorted(array, target) {
    let left = 0;
    let right = array.length - 1;
    while (left < right) {
        const sum = array[left] + array[right];
        if (sum === target) {
            return [left, right];
        }
        if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return undefined;
}
console.log(twoSumSorted([2, 4, 7, 9, 12, 15], 16)); // [1, 4]

function twoSumWithMap(array, target) {
    const seen = new Map();
    for (let i = 0; i < array.length; i++) {
        const complement = target - array[i];
        if (seen.has(complement)) {
            return [seen.get(complement), i];
        }
        seen.set(array[i], i);
    }
    return undefined;
}
console.log(twoSumWithMap([3, 8, 4, 7, 12], 15)); // [1, 3]
```

Nested loops take O(n^2). Sorted two pointers take O(n) excluding sorting. Map-based searching typically takes O(n) average time and O(n) space. Different algorithms may discover different valid pairs.

### Missing number

```javascript
const numbers = [1, 2, 4, 5, 6];
for (let target = 1; target <= 6; target++) {
    if (!numbers.includes(target)) {
        console.log(target); // 3
        break;
    }
}
const existing = new Set(numbers);
for (let target = 1; target <= 6; target++) {
    if (!existing.has(target)) {
        console.log(target); // 3
        break;
    }
}
function findMissingNumber(array) {
    const n = array.length + 1;
    const expected = n * (n + 1) / 2;
    let actual = 0;
    for (const number of array) {
        actual += number;
    }
    return expected - actual;
}
console.log(findMissingNumber(numbers)); // 3
```

Expected sum 21, actual sum 18, missing 3. Assumes exactly one missing integer from 1 through n, distinct values and safe arithmetic.

### Repeated, duplicated and unique values

```javascript
function firstRepeated(array) {
    const seen = new Set();
    for (const value of array) {
        if (seen.has(value)) {
            return value;
        }
        seen.add(value);
    }
    return undefined;
}
function firstDuplicatedValue(array) {
    const counts = new Map();
    for (const value of array) {
        counts.set(value, (counts.get(value) ?? 0) + 1);
    }
    for (const value of array) {
        if (counts.get(value) > 1) {
            return value;
        }
    }
    return undefined;
}
function firstUnique(array) {
    const counts = new Map();
    for (const value of array) {
        counts.set(value, (counts.get(value) ?? 0) + 1);
    }
    for (const value of array) {
        if (counts.get(value) === 1) {
            return value;
        }
    }
    return undefined;
}
console.log(firstRepeated([4, 7, 2, 7, 4, 9])); // 7
console.log(firstDuplicatedValue([4, 7, 2, 7, 4, 9])); // 4
console.log(firstUnique(["A", "B", "A", "C", "B", "D"])); // C
```

First repetition encountered differs from earliest original element with a duplicate.

### Local peak

```javascript
function findPeakIndex(array) {
    if (array.length === 0) {
        return -1;
    }
    let left = 0;
    let right = array.length - 1;
    while (left < right) {
        const middle = left + Math.floor((right - left) / 2);
        if (array[middle] < array[middle + 1]) {
            left = middle + 1;
        } else {
            right = middle;
        }
    }
    return left;
}
console.log(findPeakIndex([10, 25, 15, 40, 20, 30, 10])); // 3
```

Finds a weak local peak, not necessarily the global maximum, in O(log n) for dense numerical arrays without NaN.

## 21. Performance and complexity

| Algorithm | Time | Auxiliary space |
|---|---|---|
| Native linear searching | O(n) worst case | O(1) |
| Find all indexes | O(n) | O(1), excluding output |
| Closest-value search | O(n) | O(1) |
| Binary search | O(log n) | O(1) |
| Lower and upper bounds | O(log n) | O(1) |
| Rotated search, distinct values | O(log n) | O(1) |
| Naive sequence search | O(nm) upper bound | O(1) |
| Nested-loop two-sum | O(n^2) | O(1) |
| Sorted two-pointer search | O(n) | O(1) |
| Map-based two-sum | O(n) typical average | O(n) |

Callback costs are excluded. Sorting before a single search may cost more than linear search. For repeated lookups, sorted data, Set or Map can be useful.

## 22. Common mistakes

1. Treating an index as a Boolean: 0 is falsy and -1 is truthy. Use `index !== -1`.
2. Comparing object contents using indexOf(): use findIndex with a property predicate.
3. Confusing find() (value) and findIndex() (position).
4. Accessing properties of an unsuccessful find(): use optional chaining or a guard.
5. Calling splice(-1, 1) after an unsuccessful search removes the last element. Check the index first.
6. Forgetting return inside a block-bodied arrow callback.
7. Using binary search on unsorted data.
8. Failing to advance binary search boundaries: use middle + 1 or middle - 1 for closed intervals.
9. Expecting find() to return all matches: use filter() or a collection loop.
10. Confusing a falsy matching value with no match: check explicitly, or use findIndex() when undefined may be a match.

## 23. Progressive exercises and selected solutions

Attempt using plain loops first, then native methods where appropriate.

### Level 1 - Basic searching

1. Find the index of a specified number without indexOf().
2. Check whether a value exists without includes().
3. Find the last occurrence of a value.
4. Find the first number greater than 50.
5. Find the first negative number.
6. Find the index of the last positive number.
7. Implement SameValueZero comparison.
8. Search for NaN in an array.
9. Implement indexOf() with fromIndex.
10. Implement lastIndexOf() with negative starting indexes.

### Level 2 - Conditional searching

11. Find an object by its identifier.
12. Find the first available item satisfying two conditions.
13. Perform case-insensitive name searching.
14. Find a record containing a specified substring.
15. Search objects containing nested properties.
16. Implement find() with callback arguments and thisArg.
17. Implement findIndex().
18. Implement findLast().
19. Implement findLastIndex().
20. Find the first element greater than both neighbors.

### Level 3 - Multiple occurrences and ranges

21. Find all indexes containing a target value.
22. Find the third occurrence of a value.
23. Find first and last occurrences in one traversal.
24. Return complete search statistics.
25. Search only within a specified index range.
26. Search backward within a specified range.
27. Find all elements matching any supplied target.
28. Find all indexes satisfying a callback.
29. Find the closest numerical value.
30. Find the closest value without exceeding a limit.

### Level 4 - Algorithmic searching

31. Implement linear search.
32. Implement bidirectional linear search.
33. Implement ascending binary search.
34. Implement descending binary search.
35. Implement binary search for sorted objects.
36. Implement lowerBound().
37. Implement upperBound().
38. Find the first occurrence in a sorted array.
39. Find the last occurrence in a sorted array.
40. Count occurrences in logarithmic time.

### Level 5 - Advanced searching

41. Find an insertion position using binary search.
42. Count sorted values within an inclusive range.
43. Search a rotated sorted array.
44. Search a rotated sorted array containing duplicates.
45. Find the first occurrence of a consecutive sequence.
46. Find every overlapping sequence occurrence.
47. Find a pair whose sum equals a target using nested loops.
48. Solve two-sum using two pointers.
49. Solve two-sum using a Map.
50. Find a local peak in logarithmic time.

### Level 6 - Extended challenges

51. Find the missing number in a consecutive integer range.
52. Find every missing number in a specified range.
53. Find the first repeated value encountered.
54. Find the earliest original value that has a duplicate.
55. Find the first unique value.
56. Find the longest consecutive run of a target value.
57. Find the closest pair of values across two sorted arrays.
58. Find the intersection of two sorted arrays using two pointers.
59. Find a target inside a row-and-column sorted matrix.
60. Build a reusable search index for repeated object lookups.

### Selected solution 26

```javascript
function searchBackwardRange(array, target, start, end) {
    if (!Number.isInteger(start) || !Number.isInteger(end) ||
        start < 0 || end > array.length || start > end) {
        throw new RangeError("Invalid search range");
    }
    for (let i = end - 1; i >= start; i--) {
        if (array[i] === target) {
            return i;
        }
    }
    return -1;
}
console.log(searchBackwardRange([10, 20, 30, 20, 40, 20], 20, 1, 5)); // 3
```

### Selected solution 52

```javascript
function findMissingNumbers(array, start, end) {
    const existing = new Set(array);
    const missing = [];
    for (let number = start; number <= end; number++) {
        if (!existing.has(number)) {
            missing[missing.length] = number;
        }
    }
    return missing;
}
console.log(findMissingNumbers([1, 3, 4, 7, 10], 1, 10)); // [2, 5, 6, 8, 9]
```

Assumes valid safe integer boundaries and a reasonably sized range.

### Selected solution 56

```javascript
function longestRun(array, target) {
    let currentStart = -1;
    let currentLength = 0;
    let bestStart = -1;
    let bestLength = 0;
    for (let i = 0; i < array.length; i++) {
        if (array[i] === target) {
            if (currentLength === 0) {
                currentStart = i;
            }
            currentLength++;
            if (currentLength > bestLength) {
                bestStart = currentStart;
                bestLength = currentLength;
            }
        } else {
            currentStart = -1;
            currentLength = 0;
        }
    }
    return {
        start: bestStart,
        end: bestLength === 0 ? -1 : bestStart + bestLength - 1,
        length: bestLength
    };
}
console.log(longestRun([1, 1, 0, 1, 1, 1, 0, 1], 1));
```

Output: `{ start: 3, end: 5, length: 3 }`.

| Index | Value | Current length | Best length |
|---|---|---|---|
| 0 | 1 | 1 | 1 |
| 1 | 1 | 2 | 2 |
| 2 | 0 | 0 | 2 |
| 3 | 1 | 1 | 2 |
| 4 | 1 | 2 | 2 |
| 5 | 1 | 3 | 3 |
| 6 | 0 | 0 | 3 |
| 7 | 1 | 1 | 3 |

### Selected solution 57

```javascript
function closestPair(first, second) {
    if (first.length === 0 || second.length === 0) {
        return undefined;
    }
    let i = 0;
    let j = 0;
    let bestPair;
    let bestDifference = Infinity;
    while (i < first.length && j < second.length) {
        const difference = Math.abs(first[i] - second[j]);
        if (difference < bestDifference) {
            bestDifference = difference;
            bestPair = [first[i], second[j]];
        }
        if (difference === 0) {
            break;
        }
        if (first[i] < second[j]) {
            i++;
        } else {
            j++;
        }
    }
    return { pair: bestPair, difference: bestDifference };
}
console.log(closestPair([10, 25, 40, 60], [5, 18, 38, 70]));
```

Output: `{ pair: [40, 38], difference: 2 }`. Assumes ascending sorted finite numbers. O(n + m).

### Selected solution 58

```javascript
function intersectSorted(first, second) {
    const result = [];
    let i = 0;
    let j = 0;
    while (i < first.length && j < second.length) {
        if (first[i] === second[j]) {
            result[result.length] = first[i];
            i++;
            j++;
        } else if (first[i] < second[j]) {
            i++;
        } else {
            j++;
        }
    }
    return result;
}
console.log(intersectSorted([1, 2, 4, 6, 8], [2, 3, 4, 7, 8])); // [2, 4, 8]
```

Preserves duplicate matches up to the smaller occurrence count.

### Selected solution 59

```javascript
function searchSortedMatrix(matrix, target) {
    if (matrix.length === 0 || matrix[0].length === 0) {
        return undefined;
    }
    let row = 0;
    let column = matrix[0].length - 1;
    while (row < matrix.length && column >= 0) {
        const value = matrix[row][column];
        if (value === target) {
            return [row, column];
        }
        if (value > target) {
            column--;
        } else {
            row++;
        }
    }
    return undefined;
}
const matrix = [
    [1, 4, 7, 11],
    [2, 5, 8, 12],
    [3, 6, 9, 16],
    [10, 13, 14, 17]
];
console.log(searchSortedMatrix(matrix, 6)); // [2, 1]
```

Search path: 11 -> 7 -> 4 -> 5 -> 6. Start at the top right, move left if too large, down if too small. Assumes a rectangular row-and-column sorted matrix. O(r + c) rather than O(rc).

### Selected solution 60

```javascript
function createIndex(array, property) {
    const index = new Map();
    for (const item of array) {
        const key = item[property];
        if (index.has(key)) {
            throw new Error("Duplicate index key");
        }
        index.set(key, item);
    }
    return index;
}
const products = [{ id: 101, name: "Notebook" }, { id: 205, name: "Marker" }, { id: 310, name: "Folder" }];
const productIndex = createIndex(products, "id");
console.log(productIndex.get(205)); // { id: 205, name: "Marker" }
console.log(productIndex.has(310)); // true
console.log(productIndex.has(999)); // false
```

Update the index when records or their identifiers change.

## 24. Coverage tracker

- [x] indexOf(), lastIndexOf(), includes(), find(), findIndex(), findLast(), findLastIndex()
- [x] Parameters, return values, fromIndex, callbacks, thisArg, sparse arrays, equality, manual implementations
- [x] Forward, backward, bounded and conditional searching
- [x] Object, nested property, case-insensitive and multiple-target searching
- [x] All indexes, nth occurrences, first and last boundaries, statistics
- [x] Consecutive and overlapping sequences, closest values
- [x] Linear, bidirectional, binary and comparator-based search
- [x] Lower and upper bounds, sorted occurrence counting, rotated arrays
- [x] Two-sum, missing values, duplicate and unique values, local peaks
- [x] Sorted matrix search and Map indexing

Deferred: recursive binary search, Knuth-Morris-Pratt, Rabin-Karp, interpolation search, exponential search, graph searching, breadth-first and depth-first search, complete Set and Map APIs.

### References

- [JavaScript.info - Array methods](https://javascript.info/array-methods)
- [MDN - Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN - Equality comparisons and sameness](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Equality_comparisons_and_sameness)
- [MDN - Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [MDN - Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [ECMAScript - Indexed collections](https://tc39.es/ecma262/#sec-indexed-collections)

Custom algorithms assume ordinary arrays and appropriate input types; they are not complete specification-compliant polyfills.

## Next: Part 5 - Filtering, partitioning and conditional collection

filter(), manual filtering, dynamic criteria, ranges, membership, nested properties, sparse arrays, duplicates, stable and unstable partitioning, in-place compaction, multiple collections, intersection, union, difference, Set and Map, combined searching and filtering, performance and exercises.

[Previous: Part 3](array-methods-and-loops-part-3.md) | [Next: Part 5](array-methods-and-loops-part-5.md)
