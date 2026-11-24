# JavaScript Array Methods and Plain Loops

## Part 3 - Copying, extracting, merging and traversing arrays

[Previous: Part 2](array-methods-and-loops-part-2.md) | [Next: Part 4](array-methods-and-loops-part-4.md)

In the previous part, we learned how arrays grow and shrink, how elements are shifted, how callbacks work, and how to implement insertion and removal operations using plain loops.

We will now learn how to copy, extract, combine and traverse arrays without unnecessarily modifying the original data.

## Table of contents

1. Understanding array copying
2. Extracting elements with `slice()`
3. Implementing `slice()` using plain loops
4. Advanced range extraction
5. Combining arrays with `concat()`
6. Implementing `concat()` using plain loops
7. Merging and interleaving arrays
8. Dividing arrays into chunks
9. Overlapping ranges and windows
10. Accessing elements with `at()`
11. Circular indexing and traversal
12. Traversing arrays with `forEach()`
13. Implementing `forEach()` using plain loops
14. Array `keys()`, `values()` and `entries()`
15. Advanced traversal patterns
16. Performance and complexity
17. Common mistakes
18. Exercises and selected solutions
19. Coverage tracker

## 1. Understanding array copying

Suppose we have an array containing the scores of five contestants. We want to create another array containing the same scores so that we can modify the new array without affecting the original.

```javascript
const original = [72, 85, 91, 64, 78];
const copy = original;
copy[0] = 100;
console.log(original);
console.log(copy);
```

Output:

```text
[100, 85, 91, 64, 78]
[100, 85, 91, 64, 78]
```

The assignment did not create another array. Both variables refer to the same array.

```text
original ----> [100, 85, 91, 64, 78]
copy --------> same array
```

### Copying using a plain loop

```javascript
const original = [72, 85, 91, 64, 78];
const copy = [];

for (let i = 0; i < original.length; i++) {
    copy[i] = original[i];
}

copy[0] = 100;
console.log(original);
console.log(copy);
```

Output:

```text
[72, 85, 91, 64, 78]
[100, 85, 91, 64, 78]
```

The expression `copy[i] = original[i]` reads the source element, locates the destination position and assigns the value.

| Iteration | Destination array |
|---|---|
| Before loop | `[]` |
| 0 | `[72]` |
| 1 | `[72, 85]` |
| 2 | `[72, 85, 91]` |
| 3 | `[72, 85, 91, 64]` |
| 4 | `[72, 85, 91, 64, 78]` |

### Other ways of copying arrays

```javascript
const original = [10, 20, 30];
const copy1 = original.slice();
const copy2 = [...original];
const copy3 = Array.from(original);
const copy4 = [].concat(original);
console.log(copy1, copy2, copy3, copy4);
```

Each copy contains `[10, 20, 30]`. These are shallow copies.

```javascript
const original = [
    { name: "Oak", height: 12 },
    { name: "Pine", height: 18 }
];
const copy = original.slice();
copy[0].height = 25;
console.log(original[0].height); // 25
```

The outer array is independent, but the objects inside it are shared. `slice()` preserves ordinary sparse-array holes, whereas spread generally converts them to explicit `undefined` elements.

## 2. Extracting elements with slice()

```javascript
array.slice(start, end);
```

| Parameter | Description | Default |
|---|---|---|
| start | Starting index, included | 0 |
| end | Ending index, excluded | array.length |

Returns a new array without directly modifying the original.

```javascript
const temperatures = [24, 28, 31, 26, 29, 33, 25];
const selected = temperatures.slice(1, 4);
console.log(selected); // [28, 31, 26]
```

```text
Index:    0   1   2   3   4   5   6
Value:   24  28  31  26  29  33  25
             |-------|   excluded
```

The half-open interval is `start <= i < end`.

### Omitting arguments

```javascript
const languages = ["JavaScript", "Python", "Rust", "Java", "Go"];
console.log(languages.slice(2)); // ["Rust", "Java", "Go"]
const copy = languages.slice();
```

### Negative indexes

```javascript
const numbers = [10, 20, 30, 40, 50, 60];
console.log(numbers.slice(-3));     // [40, 50, 60]
console.log(numbers.slice(-4, -1)); // [30, 40, 50]
console.log(numbers.slice(1, -2));  // [20, 30, 40]
```

Effective negative position: `array.length + negativeIndex`. For -3, 6 + (-3) = 3.

### Extracting first and last elements

```javascript
const activities = [
    "Opened application", "Updated settings", "Created document",
    "Edited document", "Exported document"
];
const firstTwo = activities.slice(0, 2);
const lastTwo = activities.slice(-2);
console.log(firstTwo); // ["Opened application", "Updated settings"]
console.log(lastTwo); // ["Edited document", "Exported document"]
```

### Index normalization and edge cases

For `[10, 20, 30, 40, 50]`:

| Expression | Result |
|---|---|
| slice() | [10, 20, 30, 40, 50] |
| slice(2) | [30, 40, 50] |
| slice(1, 3) | [20, 30] |
| slice(-2) | [40, 50] |
| slice(0, -1) | [10, 20, 30, 40] |
| slice(3, 1) | [] |
| slice(2, 2) | [] |
| slice(10) | [] |
| slice(-100, 2) | [10, 20] |
| slice(2, 100) | [30, 40, 50] |
| slice(1.8, 3.9) | [20, 30] |
| slice(null, 2) | [10, 20] |
| slice(2, undefined) | [30, 40, 50] |
| slice(2, null) | [] |
| slice(NaN) | [10, 20, 30, 40, 50] |

JavaScript converts indexes into integers or infinity and clamps them to the valid range. An undefined end behaves like an omitted end.

### Difference between slice() and splice()

```javascript
const first = [10, 20, 30, 40, 50];
const second = [10, 20, 30, 40, 50];
console.log(first.slice(1, 3)); // [20, 30]
console.log(second.splice(1, 3)); // [20, 30, 40]
console.log(first); // [10, 20, 30, 40, 50]
console.log(second); // [10, 50]
```

| Feature | slice() | splice() |
|---|---|---|
| Purpose | Copy/extract | Insert/remove/replace |
| Second argument | End index | Delete count |
| Mutates | No, ordinarily | Yes |
| Returns | Copied elements | Removed elements |
| Negative start | Yes | Yes |

## 3. Implementing slice() using plain loops

### Example 1: Extracting a specified range

```javascript
const measurements = [14, 18, 23, 29, 31, 36, 42];
const extracted = [];
for (let i = 2; i < 5; i++) {
    extracted[extracted.length] = measurements[i];
}
console.log(extracted); // [23, 29, 31]
```

Equivalent: `measurements.slice(2, 5)`.

| i | Source value | Destination | Result |
|---|---|---|---|
| 2 | 23 | 0 | [23] |
| 3 | 29 | 1 | [23, 29] |
| 4 | 31 | 2 | [23, 29, 31] |
| 5 | Not accessed | - | Stop |

Alternative destination calculation:

```javascript
const extracted = [];
const start = 2;
const end = 5;
for (let i = start; i < end; i++) {
    extracted[i - start] = measurements[i];
}
```

`i - start` converts source indexes 2, 3, 4 into destination indexes 0, 1, 2.

Alternative using two counters:

```javascript
const extracted = [];
for (let read = 2, write = 0; read < 5; read++, write++) {
    extracted[write] = measurements[read];
}
```

### Example 2: Reusable extraction function

```javascript
function extract(array, start, end) {
    const result = [];
    for (let i = start; i < end; i++) {
        result[result.length] = array[i];
    }
    return result;
}
const numbers = [10, 20, 30, 40, 50, 60];
console.log(extract(numbers, 1, 4)); // [20, 30, 40]
console.log(extract(numbers, 3, 6)); // [40, 50, 60]
console.log(extract(numbers, -2, 3)); // [undefined, undefined, 10, 20, 30]
```

The function does not normalize negative indexes.

### Example 3: Index normalization

```javascript
function toInteger(value) {
    const number = +value;
    if (Number.isNaN(number) || number === 0) {
        return 0;
    }
    return Math.trunc(number);
}

function normalizeIndex(index, length) {
    const integer = toInteger(index);
    if (integer < 0) {
        return Math.max(length + integer, 0);
    }
    return Math.min(integer, length);
}
console.log(normalizeIndex(-2, 6)); // 4
console.log(normalizeIndex(-100, 6)); // 0
console.log(normalizeIndex(100, 6)); // 6
console.log(normalizeIndex(2.9, 6)); // 2
```

### Example 4: manualSlice()

```javascript
function manualSlice(array, start = 0, end = undefined) {
    const length = array.length;
    const from = normalizeIndex(start, length);
    const to = end === undefined ? length : normalizeIndex(end, length);
    const result = [];
    for (let i = from; i < to; i++) {
        result[result.length] = array[i];
    }
    return result;
}
const numbers = [10, 20, 30, 40, 50, 60];
console.log(manualSlice(numbers)); // [10, 20, 30, 40, 50, 60]
console.log(manualSlice(numbers, 1, 4)); // [20, 30, 40]
console.log(manualSlice(numbers, -3)); // [40, 50, 60]
console.log(manualSlice(numbers, -4, -1)); // [30, 40, 50]
console.log(manualSlice(numbers, 4, 2)); // []
```

Capture length, normalize start and end, create destination, traverse range, copy values, return destination.

### Handling sparse arrays

```javascript
const numbers = [10, , 30, 40];
console.log(1 in numbers.slice()); // false
console.log(1 in manualSlice(numbers)); // true
```

```javascript
function sparseAwareSlice(array, start = 0, end = undefined) {
    const length = array.length;
    const from = normalizeIndex(start, length);
    const to = end === undefined ? length : normalizeIndex(end, length);
    const result = [];
    for (let i = from; i < to; i++) {
        const destination = i - from;
        if (i in array) {
            result[destination] = array[i];
        }
    }
    result.length = Math.max(to - from, 0);
    return result;
}
const numbers = [10, , 30, 40];
const copy = sparseAwareSlice(numbers);
console.log(copy); // [10, empty, 30, 40]
console.log(1 in copy); // false
console.log(copy.length); // 4
```

Final length assignment preserves trailing holes. These are educational approximations, not complete polyfills; native methods handle array-like objects, species constructors, accessors and other specification behavior.

## 4. Advanced range extraction

### Inclusive ending index

```javascript
const numbers = [10, 20, 30, 40, 50, 60, 70];
const result = [];
for (let i = 2; i <= 5; i++) {
    result[result.length] = numbers[i];
}
console.log(result); // [30, 40, 50, 60]
```

Equivalent: `numbers.slice(2, 5 + 1)`.

### Reverse extraction

```javascript
const result = [];
for (let i = 5; i >= 2; i--) {
    result[result.length] = numbers[i];
}
console.log(result); // [60, 50, 40, 30]
```

Equivalent: `numbers.slice(2, 6).reverse()`.

### Every third element within a range

```javascript
const measurements = [20, 21, 23, 25, 24, 27, 29, 28, 30, 31, 33, 32];
const result = [];
for (let i = 2; i < 11; i += 3) {
    result[result.length] = measurements[i];
}
console.log(result); // [23, 27, 30]
```

Indexes visited: 2, 5, 8; stop at 11. Equivalent: `measurements.slice(2, 11).filter((value, index) => index % 3 === 0)`.

### Multiple independent ranges

```javascript
const samples = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
const ranges = [[1, 3], [5, 7], [8, 10]];
const result = [];
for (const [start, end] of ranges) {
    for (let i = start; i < end; i++) {
        result[result.length] = samples[i];
    }
}
console.log(result); // [20, 30, 60, 70, 90, 100]
```

Equivalent: `ranges.flatMap(([start, end]) => samples.slice(start, end))`.

### Excluding a selected range

```javascript
const numbers = [10, 20, 30, 40, 50, 60, 70];
const result = [];
for (let i = 0; i < numbers.length; i++) {
    if (i >= 2 && i <= 4) {
        continue;
    }
    result[result.length] = numbers[i];
}
console.log(result); // [10, 20, 60, 70]
```

Alternatives: `numbers.slice(0, 2).concat(numbers.slice(5))` or `numbers.filter((value, index) => index < 2 || index > 4)`.

## 5. Combining arrays with concat()

```javascript
array.concat(value1, value2, /* ... */ valueN);
```

Creates a new array, copies original elements, expands supplied arrays by default, appends non-array values individually and does not directly mutate sources.

```javascript
const morning = [18, 21, 24];
const afternoon = [29, 31, 28];
const combined = morning.concat(afternoon);
console.log(combined); // [18, 21, 24, 29, 31, 28]
console.log(morning); // [18, 21, 24]
console.log(afternoon); // [29, 31, 28]
```

```javascript
const first = [10, 20];
const second = [30, 40];
const third = [50, 60];
console.log(first.concat(second, third)); // [10, 20, 30, 40, 50, 60]
console.log(first.concat(30, [40, 50], 60, [70, 80]));
// [10, 20, 30, 40, 50, 60, 70, 80]
console.log([1, 2].concat([3, [4, 5]])); // [1, 2, 3, [4, 5]]
```

Nested arrays are not recursively flattened.

```javascript
const animals = ["Lion", "Elephant"];
const animal = { name: "Giraffe", height: 5 };
console.log(animals.concat(animal));
// ["Lion", "Elephant", { name: "Giraffe", height: 5 }]
```

### Array-like objects and Symbol.isConcatSpreadable

```javascript
const arrayLike = { 0: "North", 1: "South", length: 2 };
const directions = ["East", "West"];
console.log(Array.isArray(arrayLike)); // false
console.log(directions.concat(arrayLike));
// ["East", "West", { 0: "North", 1: "South", length: 2 }]
arrayLike[Symbol.isConcatSpreadable] = true;
console.log(directions.concat(arrayLike));
// ["East", "West", "North", "South"]
```

```javascript
const first = [10, 20];
const second = [30, 40];
second[Symbol.isConcatSpreadable] = false;
const result = first.concat(second);
console.log(result); // [10, 20, [30, 40]]
console.log(result.length); // 3
console.log(result[2] === second); // true
```

### concat() versus spread

| Feature | concat() | Spread |
|---|---|---|
| Expands arrays | Yes | Yes |
| Expands strings/Sets | No | Yes |
| Uses iteration protocol | Not ordinarily | Yes |
| Respects isConcatSpreadable | Yes | No |
| Preserves ordinary holes | Yes | No |
| Shallow copy | Yes | Yes |

```javascript
console.log([10, 20].concat("ABC")); // [10, 20, "ABC"]
console.log([10, 20, ..."ABC"]); // [10, 20, "A", "B", "C"]
```

## 6. Implementing concat() using plain loops

```javascript
const first = [10, 20, 30];
const second = [40, 50, 60];
const result = [];
for (let i = 0; i < first.length; i++) {
    result[result.length] = first[i];
}
for (let i = 0; i < second.length; i++) {
    result[result.length] = second[i];
}
console.log(result); // [10, 20, 30, 40, 50, 60]
```

Alternatively, copy first to `result[i]` and second to `result[first.length + i]`. The offset maps second-array indexes 0, 1, 2 to 3, 4, 5.

### Unknown number of arrays

```javascript
function combineArrays(...arrays) {
    const result = [];
    for (const array of arrays) {
        for (let i = 0; i < array.length; i++) {
            result[result.length] = array[i];
        }
    }
    return result;
}
console.log(combineArrays([10, 20], [30, 40, 50], [60], [70, 80]));
// [10, 20, 30, 40, 50, 60, 70, 80]
```

### Arrays and individual values

```javascript
function manualConcat(array, ...items) {
    const result = [];
    for (let i = 0; i < array.length; i++) {
        result[result.length] = array[i];
    }
    for (const item of items) {
        if (Array.isArray(item)) {
            for (let i = 0; i < item.length; i++) {
                result[result.length] = item[i];
            }
        } else {
            result[result.length] = item;
        }
    }
    return result;
}
console.log(manualConcat([10, 20], 30, [40, 50], 60));
// [10, 20, 30, 40, 50, 60]
```

Select each argument; if it is an array, copy its elements, otherwise append it individually.

### Spreadable objects and empty slots

```javascript
function isConcatSpreadable(value) {
    const isObject = value !== null &&
        (typeof value === "object" || typeof value === "function");
    if (!isObject) {
        return false;
    }
    const spreadable = value[Symbol.isConcatSpreadable];
    if (spreadable !== undefined) {
        return Boolean(spreadable);
    }
    return Array.isArray(value);
}

function advancedConcat(array, ...items) {
    const result = [];
    const sources = [array, ...items];
    let write = 0;
    for (const source of sources) {
        if (isConcatSpreadable(source)) {
            const length = source.length;
            for (let read = 0; read < length; read++) {
                if (read in source) {
                    result[write] = source[read];
                }
                write++;
            }
        } else {
            result[write] = source;
            write++;
        }
    }
    result.length = write;
    return result;
}
const first = [10, , 30];
const second = { 0: 40, 1: 50, length: 2, [Symbol.isConcatSpreadable]: true };
const result = advancedConcat(first, second, 60);
console.log(result); // [10, empty, 30, 40, 50, 60]
console.log(1 in result); // false
```

The write counter advances even for holes. Assumes valid nonnegative integer lengths; does not fully implement native length coercion, species or property creation semantics.

## 7. Merging and interleaving arrays

### Equal lengths

```javascript
const first = [10, 20, 30];
const second = [15, 25, 35];
const result = [];
for (let i = 0; i < first.length; i++) {
    result[result.length] = first[i];
    result[result.length] = second[i];
}
console.log(result); // [10, 15, 20, 25, 30, 35]
```

### Unequal lengths

```javascript
const first = ["A", "B", "C", "D"];
const second = [1, 2];
const result = [];
const length = Math.max(first.length, second.length);
for (let i = 0; i < length; i++) {
    if (i < first.length) {
        result[result.length] = first[i];
    }
    if (i < second.length) {
        result[result.length] = second[i];
    }
}
console.log(result); // ["A", 1, "B", 2, "C", "D"]
```

| i | First | Second | Result |
|---|---|---|---|
| 0 | A | 1 | [A, 1] |
| 1 | B | 2 | [A, 1, B, 2] |
| 2 | C | unavailable | [A, 1, B, 2, C] |
| 3 | D | unavailable | [A, 1, B, 2, C, D] |

### Zipping

```javascript
const names = ["Alice", "Brian", "Clara"];
const scores = [85, 92, 78];
const result = [];
for (let i = 0; i < names.length; i++) {
    result[result.length] = [names[i], scores[i]];
}
console.log(result); // [["Alice", 85], ["Brian", 92], ["Clara", 78]]
```

Equivalent: `names.map((name, index) => [name, scores[index]])`. For unequal lengths, use Math.min to stop at the shorter array, or Math.max and a fallback.

### Unzipping

```javascript
const records = [["Oak", 12], ["Pine", 18], ["Cedar", 15]];
const names = [];
const heights = [];
for (const [name, height] of records) {
    names[names.length] = name;
    heights[heights.length] = height;
}
console.log(names); // ["Oak", "Pine", "Cedar"]
console.log(heights); // [12, 18, 15]
```

### Merging sorted arrays

```javascript
function mergeSorted(first, second) {
    const result = [];
    let i = 0;
    let j = 0;
    while (i < first.length && j < second.length) {
        if (first[i] <= second[j]) {
            result[result.length] = first[i];
            i++;
        } else {
            result[result.length] = second[j];
            j++;
        }
    }
    while (i < first.length) {
        result[result.length] = first[i];
        i++;
    }
    while (j < second.length) {
        result[result.length] = second[j];
        j++;
    }
    return result;
}
console.log(mergeSorted([2, 8, 14, 21], [3, 5, 17, 25]));
// [2, 3, 5, 8, 14, 17, 21, 25]
```

Equivalent method approach: `first.concat(second).sort((a, b) => a - b)`.

| i | j | Comparison | Selected |
|---|---|---|---|
| 0 | 0 | 2 <= 3 | 2 |
| 1 | 0 | 8 <= 3 | 3 |
| 1 | 1 | 8 <= 5 | 5 |
| 1 | 2 | 8 <= 17 | 8 |
| 2 | 2 | 14 <= 17 | 14 |
| 3 | 2 | 21 <= 17 | 17 |
| 3 | 3 | 21 <= 25 | 21 |
| 4 | 3 | First exhausted | 25 |

Two-pointer merging takes O(n + m) time and is a fundamental component of merge sort.

## 8. Dividing arrays into chunks

Chunking divides a collection into smaller groups for pagination, batch processing and grid layouts.

```javascript
const numbers = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
const chunks = [];
for (let i = 0; i < numbers.length; i += 3) {
    chunks[chunks.length] = numbers.slice(i, i + 3);
}
console.log(chunks); // [[10,20,30], [40,50,60], [70,80,90], [100]]
```

| i | Extraction |
|---|---|
| 0 | slice(0, 3) |
| 3 | slice(3, 6) |
| 6 | slice(6, 9) |
| 9 | slice(9, 12) |

```javascript
function chunk(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Chunk size must be a positive safe integer");
    }
    const result = [];
    for (let i = 0; i < array.length; i += size) {
        result[result.length] = array.slice(i, i + size);
    }
    return result;
}
console.log(chunk([1, 2, 3, 4, 5, 6, 7], 2)); // [[1,2],[3,4],[5,6],[7]]
```

### Without array methods

```javascript
function manualChunk(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid chunk size");
    }
    const result = [];
    for (let start = 0; start < array.length; start += size) {
        const group = [];
        const end = Math.min(start + size, array.length);
        for (let i = start; i < end; i++) {
            group[group.length] = array[i];
        }
        result[result.length] = group;
    }
    return result;
}
console.log(manualChunk([10, 20, 30, 40, 50, 60, 70], 3));
// [[10,20,30],[40,50,60],[70]]
```

Each element is copied once, so the nested loops perform O(n) copying work.

### One loop

```javascript
function chunkSingleLoop(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid chunk size");
    }
    const result = [];
    let group = [];
    for (let i = 0; i < array.length; i++) {
        group[group.length] = array[i];
        if (group.length === size) {
            result[result.length] = group;
            group = [];
        }
    }
    if (group.length > 0) {
        result[result.length] = group;
    }
    return result;
}
console.log(chunkSingleLoop([1, 2, 3, 4, 5], 2)); // [[1,2],[3,4],[5]]
```

| Value | Current group | Completed groups |
|---|---|---|
| 1 | [1] | [] |
| 2 | [] | [[1,2]] |
| 3 | [3] | [[1,2]] |
| 4 | [] | [[1,2],[3,4]] |
| 5 | [5] | [[1,2],[3,4]] |
| After loop | Final group appended | [[1,2],[3,4],[5]] |

## 9. Overlapping ranges and windows

```javascript
const numbers = [10, 20, 30, 40, 50, 60];
const windows = [];
const size = 3;
for (let i = 0; i <= numbers.length - size; i++) {
    windows[windows.length] = numbers.slice(i, i + size);
}
console.log(windows);
// [[10,20,30],[20,30,40],[30,40,50],[40,50,60]]
```

The final valid starting position is n - k. Number of complete windows: max(0, n - k + 1).

```javascript
function createWindows(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid window size");
    }
    const result = [];
    for (let start = 0; start <= array.length - size; start++) {
        const window = [];
        for (let offset = 0; offset < size; offset++) {
            window[offset] = array[start + offset];
        }
        result[result.length] = window;
    }
    return result;
}
console.log(createWindows([1, 2, 3, 4, 5], 3)); // [[1,2,3],[2,3,4],[3,4,5]]
```

Copying complexity is O((n-k+1)k) for 1 <= k <= n.

### Moving averages

```javascript
const temperatures = [12, 18, 24, 30, 36];
const averages = [];
const size = 3;
for (let start = 0; start <= temperatures.length - size; start++) {
    let sum = 0;
    for (let i = start; i < start + size; i++) {
        sum += temperatures[i];
    }
    averages[averages.length] = sum / size;
}
console.log(averages); // [18, 24, 30]
```

### Optimized sliding window

```javascript
function movingAverage(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid window size");
    }
    if (size > array.length) {
        return [];
    }
    const result = [];
    let sum = 0;
    for (let i = 0; i < size; i++) {
        sum += array[i];
    }
    result[result.length] = sum / size;
    for (let i = size; i < array.length; i++) {
        sum -= array[i - size];
        sum += array[i];
        result[result.length] = sum / size;
    }
    return result;
}
console.log(movingAverage([12, 18, 24, 30, 36], 3)); // [18, 24, 30]
```

| Window | Outgoing | Incoming | Sum | Average |
|---|---|---|---|---|
| [12,18,24] | - | - | 54 | 18 |
| [18,24,30] | 12 | 30 | 72 | 24 |
| [24,30,36] | 18 | 36 | 90 | 30 |

Subtract the outgoing element and add the incoming element. Assumes finite numerical values. O(n) time, O(1) auxiliary space excluding output.

## 10. Accessing elements with at()

```javascript
const numbers = [10, 20, 30, 40, 50];
console.log(numbers.at(0)); // 10
console.log(numbers.at(2)); // 30
console.log(numbers.at(-1)); // 50
console.log(numbers.at(-2)); // 40
```

`numbers[-1]` accesses a property named "-1", not a relative position.

```javascript
const numbers = [10, 20, 30];
console.log(numbers[-1]); // undefined
console.log(numbers.at(-1)); // 30
numbers[-1] = 100;
console.log(numbers[-1]); // 100
console.log(numbers.at(-1)); // 30
console.log(numbers.length); // 3
```

### Manual implementation

```javascript
function manualAt(array, index = 0) {
    const length = array.length;
    const integer = toInteger(index);
    const position = integer < 0 ? length + integer : integer;
    if (position < 0 || position >= length) {
        return undefined;
    }
    return array[position];
}
const numbers = [10, 20, 30, 40, 50];
console.log(manualAt(numbers, -1)); // 50
console.log(manualAt(numbers, -3)); // 30
console.log(manualAt(numbers, 2)); // 30
console.log(manualAt(numbers, 100)); // undefined
```

For `[10,20,30]`:

| Expression | Result |
|---|---|
| at() | 10 |
| at(0) | 10 |
| at(-0) | 10 |
| at(-1) | 30 |
| at(-3) | 10 |
| at(-4) | undefined |
| at(3) | undefined |
| at(1.9) | 20 |
| at(-1.9) | 30 |
| at(NaN) | 10 |
| at(Infinity) | undefined |

```javascript
const numbers = [10, 20, 30, 40, 50];
for (let i = -1; i >= -numbers.length; i--) {
    console.log(numbers.at(i));
}
// 50, 40, 30, 20, 10 (separate lines)
```

Equivalent: traverse from `numbers.length - 1` down to 0.

## 11. Circular indexing and traversal

`at()` does not wrap repeatedly. The remainder operator allows wrapping: 0 % 4 = 0, 1 % 4 = 1, 2 % 4 = 2, 3 % 4 = 3, 4 % 4 = 0, 5 % 4 = 1.

```javascript
const directions = ["North", "East", "South", "West"];
for (let i = 0; i < 10; i++) {
    const index = i % directions.length;
    console.log(directions[index]);
}
```

Output: North, East, South, West, North, East, South, West, North, East (separate lines).

JavaScript remainder can be negative: -1 % 4 = -1. Normalize using `((index % length) + length) % length`.

```javascript
function circularAt(array, index) {
    if (array.length === 0) {
        return undefined;
    }
    if (!Number.isSafeInteger(index)) {
        throw new RangeError("Invalid index");
    }
    const length = array.length;
    const position = ((index % length) + length) % length;
    return array[position];
}
const directions = ["North", "East", "South", "West"];
console.log(circularAt(directions, 4)); // North
console.log(circularAt(directions, 5)); // East
console.log(circularAt(directions, -1)); // West
console.log(circularAt(directions, -5)); // West
```

### Rotating left

```javascript
const numbers = [10, 20, 30, 40, 50];
const result = [];
const positions = 2;
for (let i = 0; i < numbers.length; i++) {
    const source = (i + positions) % numbers.length;
    result[i] = numbers[source];
}
console.log(result); // [30, 40, 50, 10, 20]
```

| Destination | Calculation | Source | Value |
|---|---|---|---|
| 0 | (0+2)%5 | 2 | 30 |
| 1 | (1+2)%5 | 3 | 40 |
| 2 | (2+2)%5 | 4 | 50 |
| 3 | (3+2)%5 | 0 | 10 |
| 4 | (4+2)%5 | 1 | 20 |

Equivalent: `numbers.slice(2).concat(numbers.slice(0, 2))`.

```javascript
function rotateLeft(array, positions) {
    if (!Number.isSafeInteger(positions)) {
        throw new RangeError("Invalid rotation");
    }
    const length = array.length;
    if (length === 0) {
        return [];
    }
    const shift = ((positions % length) + length) % length;
    const result = [];
    for (let i = 0; i < length; i++) {
        const source = (i + shift) % length;
        result[i] = array[source];
    }
    return result;
}
const numbers = [10, 20, 30, 40, 50];
console.log(rotateLeft(numbers, 2)); // [30,40,50,10,20]
console.log(rotateLeft(numbers, 7)); // [30,40,50,10,20]
console.log(rotateLeft(numbers, -2)); // [40,50,10,20,30]
```

## 12. Traversing arrays with forEach()

```javascript
array.forEach(callback, thisArg);
```

Callback receives element, index and original array. Optional thisArg supplies the callback this value. Returns undefined on normal completion.

```javascript
const instruments = ["Piano", "Violin", "Flute"];
instruments.forEach(function (instrument) {
    console.log(instrument);
});
instruments.forEach(instrument => {
    console.log(instrument);
});
for (let i = 0; i < instruments.length; i++) {
    console.log(instruments[i]);
}
```

Each traversal prints Piano, Violin, Flute on separate lines.

```javascript
const planets = ["Mercury", "Venus", "Earth", "Mars"];
planets.forEach((planet, index) => {
    console.log(`${index + 1}. ${planet}`);
});
```

Output: 1. Mercury, 2. Venus, 3. Earth, 4. Mars (separate lines).

```javascript
const mountains = ["Everest", "Kilimanjaro", "Denali"];
mountains.forEach((mountain, index, array) => {
    console.log(`${index + 1}/${array.length}: ${mountain}`);
});
mountains.forEach((mountain, index, array) => {
    console.log(array === mountains);
});
```

Output: 1/3: Everest, 2/3: Kilimanjaro, 3/3: Denali, true, true, true.

### Modifying elements

```javascript
const numbers = [10, 20, 30];
numbers.forEach((number, index, array) => {
    array[index] = number * 2;
});
console.log(numbers); // [20, 40, 60]
```

Reassigning `number = number * 2` alone does not replace the original element.

### Accumulating values

```javascript
const distances = [12, 18, 24, 16];
let total = 0;
distances.forEach(distance => {
    total += distance;
});
console.log(total); // 70
```

Equivalent: `for (const distance of distances) { total += distance; }` with total initialized to zero.

### Callback returns are ignored

```javascript
const numbers = [1, 2, 3];
console.log(numbers.forEach(number => number * 2)); // undefined
console.log(numbers.map(number => number * 2)); // [2,4,6]
```

### Skipping elements

```javascript
const numbers = [10, -5, 20, -8, 30];
numbers.forEach(number => {
    if (number < 0) {
        return;
    }
    console.log(number);
});
```

Output: 10, 20, 30. Return exits only the current callback, like continue in a plain loop. Using break inside the callback without an enclosing loop is a SyntaxError. Use a plain loop for early termination.

### Sparse arrays

```javascript
const numbers = [10, , 30, undefined];
numbers.forEach((number, index) => {
    console.log(index, number);
});
```

```text
0 10
2 30
3 undefined
```

An ordinary indexed loop also visits index 1 and reads undefined. To skip absent positions, check `if (i in numbers)`.

### Adding elements during traversal

```javascript
const numbers = [10, 20, 30];
numbers.forEach((number, index, array) => {
    console.log(number);
    if (index === 0) {
        array.push(40);
    }
});
console.log(numbers);
```

```text
10
20
30
[10, 20, 30, 40]
```

forEach captures initial length. A plain loop checking current length visits the appended 40.

### Deleting elements

```javascript
const numbers = [10, 20, 30, 40];
numbers.forEach((number, index, array) => {
    console.log(number);
    if (index === 0) {
        delete array[2];
    }
});
```

Output: 10, 20, 40.

### Updating unvisited elements

```javascript
const numbers = [10, 20, 30];
numbers.forEach((number, index, array) => {
    if (index === 0) {
        array[2] = 99;
    }
    console.log(number);
});
```

Output: 10, 20, 99. Initial length is captured, not a snapshot of values.

### Asynchronous callbacks

```javascript
async function processItems(items) {
    items.forEach(async item => {
        await processItem(item);
    });
    console.log("Finished");
}
```

Finished may appear before processing completes. Sequential alternative:

```javascript
async function processItems(items) {
    for (const item of items) {
        await processItem(item);
    }
    console.log("Finished");
}
```

Concurrent alternative:

```javascript
async function processItems(items) {
    await Promise.all(items.map(item => processItem(item)));
    console.log("Finished");
}
```

Assumes processItem is defined elsewhere. Async execution will be covered later.

## 13. Implementing forEach() using plain loops

```javascript
function manualForEach(array, callback) {
    for (let i = 0; i < array.length; i++) {
        callback(array[i], i, array);
    }
}
const colors = ["Red", "Green", "Blue"];
manualForEach(colors, (color, index) => {
    console.log(`${index}: ${color}`);
});
```

Output: 0: Red, 1: Green, 2: Blue.

### Improved implementation

```javascript
function manualForEach(array, callback, thisArg) {
    const length = array.length;
    if (typeof callback !== "function") {
        throw new TypeError("Callback must be a function");
    }
    for (let i = 0; i < length; i++) {
        if (i in array) {
            callback.call(thisArg, array[i], i, array);
        }
    }
}
const formatter = {
    prefix: "Sample",
    display(value, index) {
        console.log(`${this.prefix} ${index + 1}: ${value}`);
    }
};
const readings = [24, 28, 31];
manualForEach(readings, formatter.display, formatter);
```

```text
Sample 1: 24
Sample 2: 28
Sample 3: 31
```

Native equivalent: `readings.forEach(formatter.display, formatter)`. The first argument to call sets this, remaining arguments are callback parameters. Arrow functions retain lexical this. This implementation is educational, not a complete native polyfill.

## 14. Array keys(), values() and entries()

These methods return iterator objects, not arrays.

```javascript
const colors = ["Red", "Green", "Blue"];
for (const index of colors.keys()) {
    console.log(index);
}
for (const color of colors.values()) {
    console.log(color);
}
for (const entry of colors.entries()) {
    console.log(entry);
}
```

```text
0
1
2
Red
Green
Blue
[0, "Red"]
[1, "Green"]
[2, "Blue"]
```

The default array iterator is normally values():

```javascript
console.log(Array.prototype[Symbol.iterator] === Array.prototype.values); // true
for (const [index, color] of colors.entries()) {
    console.log(`${index}: ${color}`);
}
```

Equivalent indexed loop uses i for index and colors[i] for value.

### iterator.next()

```javascript
const colors = ["Red", "Green", "Blue"];
const iterator = colors.values();
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

```text
{ value: "Red", done: false }
{ value: "Green", done: false }
{ value: "Blue", done: false }
{ value: undefined, done: true }
```

### Consuming with while

```javascript
const iterator = ["Red", "Green", "Blue"].values();
let current = iterator.next();
while (!current.done) {
    console.log(current.value);
    current = iterator.next();
}
```

Output: Red, Green, Blue. Full iterator protocol and closing behavior are deferred.

### Converting iterators to arrays

```javascript
const colors = ["Red", "Green", "Blue"];
console.log([...colors.keys()]); // [0,1,2]
console.log([...colors.values()]); // ["Red","Green","Blue"]
console.log([...colors.entries()]); // [[0,"Red"],[1,"Green"],[2,"Blue"]]
const indexes = Array.from(colors.keys());
const values = Array.from(colors.values());
const entries = Array.from(colors.entries());
```

### Sparse arrays

```javascript
const numbers = [10, , 30];
console.log([...numbers.keys()]); // [0,1,2]
console.log([...numbers.values()]); // [10,undefined,30]
console.log([...numbers.entries()]); // [[0,10],[1,undefined],[2,30]]
```

Unlike forEach, these iterators do not skip holes.

### Modification during iteration

```javascript
const numbers = [10, 20];
const iterator = numbers.values();
console.log(iterator.next());
numbers.push(30);
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

```text
{ value: 10, done: false }
{ value: 20, done: false }
{ value: 30, done: false }
{ value: undefined, done: true }
```

An exhausted iterator does not restart after appending.

| Technique | Index | Value | Skips holes | Early exit |
|---|---|---|---|---|
| for | Yes | Yes | No by default | Yes |
| while | Manually | Yes | No by default | Yes |
| for...of | Not directly | Yes | No | Yes |
| forEach | Yes | Yes | Yes | No normal break |
| keys | Yes | No | No | Yes in loop |
| values | No | Yes | No | Yes in loop |
| entries | Yes | Yes | No | Yes in loop |

## 15. Advanced traversal patterns

### Adjacent pairs

```javascript
const measurements = [12, 18, 15, 24, 30];
const differences = [];
for (let i = 1; i < measurements.length; i++) {
    const difference = measurements[i] - measurements[i - 1];
    differences[differences.length] = difference;
}
console.log(differences); // [6,-3,9,6]
```

### Adjacent triples

```javascript
const numbers = [10, 25, 15, 40, 20, 30, 10];
const peaks = [];
for (let i = 1; i < numbers.length - 1; i++) {
    const previous = numbers[i - 1];
    const current = numbers[i];
    const next = numbers[i + 1];
    if (current > previous && current > next) {
        peaks[peaks.length] = current;
    }
}
console.log(peaks); // [25,40,30]
```

First and last positions have no two immediate neighbors.

### Both ends

```javascript
const letters = ["A", "B", "C", "D", "E", "F"];
for (let left = 0, right = letters.length - 1;
    left < right; left++, right--) {
    console.log(letters[left], letters[right]);
}
```

Output: A F, B E, C D.

### Symmetry

```javascript
function isSymmetrical(array) {
    let left = 0;
    let right = array.length - 1;
    while (left < right) {
        if (array[left] !== array[right]) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
console.log(isSymmetrical([1, 2, 3, 2, 1])); // true
console.log(isSymmetrical([1, 2, 3, 4])); // false
```

Strict equality does not consider NaN equal to itself.

### Alternating from opposite ends

```javascript
const numbers = [10, 20, 30, 40, 50, 60, 70];
const result = [];
let left = 0;
let right = numbers.length - 1;
while (left <= right) {
    result[result.length] = numbers[left];
    left++;
    if (left <= right) {
        result[result.length] = numbers[right];
        right--;
    }
}
console.log(result); // [10,70,20,60,30,50,40]
```

Second condition prevents copying the middle element twice.

### Cumulative threshold

```javascript
const supplies = [12, 18, 25, 16, 30];
const selected = [];
let total = 0;
for (const amount of supplies) {
    selected[selected.length] = amount;
    total += amount;
    if (total >= 50) {
        break;
    }
}
console.log(selected); // [12,18,25]
console.log(total); // 55
```

Finds the first qualifying nonempty prefix; if none reaches the threshold, returns all elements and final total.

### Consecutive runs

```javascript
const signals = ["A", "A", "A", "B", "B", "C", "A", "A"];
const groups = [];
let current = [];
for (let i = 0; i < signals.length; i++) {
    if (current.length > 0 && signals[i] !== current[current.length - 1]) {
        groups[groups.length] = current;
        current = [];
    }
    current[current.length] = signals[i];
}
if (current.length > 0) {
    groups[groups.length] = current;
}
console.log(groups); // [["A","A","A"],["B","B"],["C"],["A","A"]]
```

Separate runs of A remain separate. Useful for run-length encoding and streak analysis.

## 16. Performance and complexity

| Operation | Typical time | Output space |
|---|---|---|
| Access / at() | O(1) | O(1) |
| Copy array | O(n) | O(n) |
| Extract k elements | O(k) | O(k) |
| Concatenate | O(n+m) | O(n+m) |
| Interleave | O(n+m) | O(n+m) |
| Sorted merge | O(n+m) | O(n+m) |
| Chunk | O(n) | O(n) |
| forEach | O(n) | O(1), excluding callback work |
| Rotate with copy | O(n) | O(n) |
| Symmetry | O(n) | O(1) |
| Optimized moving average | O(n) | O(n) |

These are estimates for ordinary arrays, not ECMAScript guarantees.

For n=1000, k=100, naive window sums require (1000-100+1)*100 = 90,100 additions. Running sum requires 100+2*(1000-100) = 1,900 additions/subtractions. This is about 47.4 times fewer arithmetic operations, not a measured speed benchmark.

### Avoid repeated concatenation

```javascript
let result = [];
for (let i = 0; i < 1000; i++) {
    result = result.concat(i);
}
```

Repeatedly copying the growing array causes quadratic copying work. Instead:

```javascript
const result = [];
for (let i = 0; i < 1000; i++) {
    result[result.length] = i;
}
```

Or use `result.push(i)`. Both typically provide linear total construction time.

## 17. Common mistakes

1. Confusing end index with count: `[10,20,30,40,50].slice(2,2)` returns `[]`; use `slice(2,4)` for `[30,40]`.
2. Expecting slice to mutate: store its returned array.
3. Expecting concat to mutate: store its returned array.
4. Wrong destination index: copying from index 2 into `result[i]` creates leading holes; use `result[i-2]` or `result[result.length]`.
5. Forgetting chunk-size validation: a zero step causes an infinite loop for a nonempty array.
6. Expecting forEach to return transformed elements: use map or a transformation loop.
7. Treating an iterator as an array: iterator[0] and iterator.length are normally undefined; use next() or spread, which consumes remaining values.
8. Negative remainder: -1 % 5 is -1; use ((-1 % 5) + 5) % 5 for 4.
9. Assuming shallow copies are deep: nested objects remain shared.
10. Creating incomplete windows: use `i <= array.length - size` instead of `i < array.length` for complete windows only.

## 18. Exercises and selected solutions

Attempt each exercise using plain loops first. Where applicable, solve it again using array methods and compare.

### Level 1 - Copying and extraction

1. Copy an array without using array methods.
2. Extract the first five elements.
3. Extract the last four elements.
4. Extract indexes 3 through 7 inclusively.
5. Extract a selected range in reverse order.
6. Extract every second element.
7. Extract every third element within a selected range.
8. Copy an array while excluding a specified range.
9. Implement negative index normalization.
10. Implement a sparse-aware version of slice().

### Level 2 - Combining collections

11. Combine two arrays without concat() or spread syntax.
12. Combine any number of arrays using nested loops.
13. Combine arrays and individual values.
14. Implement a simplified concat() method.
15. Interleave two equally sized arrays.
16. Interleave arrays of different lengths.
17. Zip two arrays into index-value or corresponding-value pairs.
18. Unzip paired values into two arrays.
19. Merge two sorted arrays without using sort().
20. Combine three arrays in alternating round-robin order.

### Level 3 - Chunks and windows

21. Divide an array into groups of four.
22. Implement chunking without slice().
23. Implement chunking using only one traversal.
24. Create every consecutive window of size three.
25. Create windows using a configurable step size.
26. Calculate the sum of every window.
27. Calculate moving averages using a running sum.
28. Find the maximum sum of any fixed-size window.
29. Count windows whose sums exceed a threshold.
30. Extract only complete chunks and discard incomplete chunks.

### Level 4 - Traversal and indexing

31. Implement a simplified at() method.
32. Implement circular indexing.
33. Rotate an array three positions to the left.
34. Rotate an array three positions to the right.
35. Traverse an array alternately from both ends.
36. Check whether an array is symmetrical.
37. Calculate differences between adjacent values.
38. Find all local minimum values.
39. Implement forEach() with callback arguments and thisArg.
40. Consume an array iterator using a while loop.

### Level 5 - Advanced problem-solving

41. Extract multiple independent ranges into one array.
42. Merge three already sorted arrays.
43. Find the longest consecutive run of equal values.
44. Group consecutive equal elements.
45. Implement run-length encoding.
46. Find the first prefix reaching a cumulative threshold.
47. Create circular windows that wrap around the array.
48. Extract overlapping windows in reverse order.
49. Merge sorted arrays while removing duplicate values.
50. Rotate an array in place using constant auxiliary space.

### Selected solution 20: Round-robin merging

```javascript
function roundRobin(arrays) {
    const result = [];
    let maxLength = 0;
    for (const array of arrays) {
        if (array.length > maxLength) {
            maxLength = array.length;
        }
    }
    for (let i = 0; i < maxLength; i++) {
        for (const array of arrays) {
            if (i < array.length) {
                result[result.length] = array[i];
            }
        }
    }
    return result;
}
console.log(roundRobin([["A", "B", "C"], [1, 2], ["X", "Y", "Z", "W"]]));
```

```text
["A", 1, "X", "B", 2, "Y", "C", "Z", "W"]
```

Outer loop controls position; inner loop visits arrays. O(kL) position checks for k arrays and longest length L.

### Selected solution 28: Maximum fixed-size window sum

```javascript
function maxWindowSum(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid window size");
    }
    if (size > array.length) {
        return undefined;
    }
    let sum = 0;
    for (let i = 0; i < size; i++) {
        sum += array[i];
    }
    let maximum = sum;
    for (let i = size; i < array.length; i++) {
        sum -= array[i - size];
        sum += array[i];
        if (sum > maximum) {
            maximum = sum;
        }
    }
    return maximum;
}
console.log(maxWindowSum([2, 1, 5, 1, 3, 2], 3)); // 9
```

| Window | Sum | Maximum |
|---|---|---|
| [2,1,5] | 8 | 8 |
| [1,5,1] | 7 | 8 |
| [5,1,3] | 9 | 9 |
| [1,3,2] | 6 | 9 |

Initializing maximum from the first window supports negative values.

### Selected solution 45: Run-length encoding

```javascript
function runLengthEncode(array) {
    if (array.length === 0) {
        return [];
    }
    const result = [];
    let current = array[0];
    let count = 1;
    for (let i = 1; i < array.length; i++) {
        if (array[i] === current) {
            count++;
        } else {
            result[result.length] = [current, count];
            current = array[i];
            count = 1;
        }
    }
    result[result.length] = [current, count];
    return result;
}
console.log(runLengthEncode(["A", "A", "A", "B", "B", "C", "A", "A"]));
```

```text
[["A",3],["B",2],["C",1],["A",2]]
```

Final append is essential because the last run may not encounter a different value.

### Selected solution 47: Circular windows

```javascript
function circularWindows(array, size) {
    if (!Number.isSafeInteger(size) || size <= 0) {
        throw new RangeError("Invalid window size");
    }
    const length = array.length;
    const result = [];
    for (let start = 0; start < length; start++) {
        const window = [];
        for (let offset = 0; offset < size; offset++) {
            const index = (start + offset) % length;
            window[offset] = array[index];
        }
        result[result.length] = window;
    }
    return result;
}
console.log(circularWindows([10, 20, 30, 40], 3));
```

```text
[[10,20,30],[20,30,40],[30,40,10],[40,10,20]]
```

Combines nested loops, circular indexing, destination indexing and extraction.

### Selected solution 50: In-place rotation

```javascript
function reverseRange(array, left, right) {
    while (left < right) {
        const temporary = array[left];
        array[left] = array[right];
        array[right] = temporary;
        left++;
        right--;
    }
}
function rotateLeftInPlace(array, positions) {
    if (!Number.isSafeInteger(positions)) {
        throw new RangeError("Invalid rotation");
    }
    const length = array.length;
    if (length === 0) {
        return array;
    }
    const shift = ((positions % length) + length) % length;
    if (shift === 0) {
        return array;
    }
    reverseRange(array, 0, shift - 1);
    reverseRange(array, shift, length - 1);
    reverseRange(array, 0, length - 1);
    return array;
}
const numbers = [10, 20, 30, 40, 50];
rotateLeftInPlace(numbers, 2);
console.log(numbers); // [30,40,50,10,20]
```

```text
Original:           [10,20,30,40,50]
Reverse first two:  [20,10,30,40,50]
Reverse remainder:  [20,10,50,40,30]
Reverse entire:     [30,40,50,10,20]
```

O(n) time, O(1) auxiliary space. Assumes an ordinary writable dense array.

## 19. Coverage tracker

| Concept | Status |
|---|---|
| Manual array copying | Covered |
| Shallow copying behavior | Covered |
| slice() | Covered |
| Negative index normalization | Covered |
| Manual slice() | Covered |
| Sparse-aware copying | Covered |
| Forward and backward extraction | Covered |
| Strided extraction | Covered |
| Multiple range extraction | Covered |
| concat() | Covered |
| Manual concatenation | Covered |
| Symbol.isConcatSpreadable | Covered |
| Interleaving | Covered |
| Zipping and unzipping | Covered |
| Sorted merging | Covered |
| Two-pointer introduction | Covered |
| Chunking | Covered |
| Overlapping windows | Covered |
| Optimized sliding windows | Covered |
| at() | Covered |
| Circular indexing | Covered |
| Array rotation | Covered |
| forEach() | Covered |
| Callback traversal behavior | Covered |
| Manual forEach() | Covered |
| keys() | Covered |
| values() | Covered |
| entries() | Covered |
| Iterator consumption introduction | Covered |
| Adjacent-element analysis | Covered |
| Consecutive grouping | Covered |
| Performance analysis | Covered |

### Capabilities reference

| Problem | Array method approach | Plain-loop approach |
|---|---|---|
| Copy array | slice() | Indexed copying |
| Extract range | slice() | Bounded traversal |
| Extract in reverse | slice() + reverse() | Backward traversal |
| Combine arrays | concat() | Sequential copying |
| Interleave arrays | Combined operations | Multiple source traversal |
| Zip arrays | map() | Corresponding indexes |
| Merge sorted arrays | concat() + sort() | Two pointers |
| Chunk array | Loop + slice() | Nested loops or state tracking |
| Create windows | Loop + slice() | Overlapping traversal |
| Moving average | Multiple operations | Running sum |
| Access relative position | at() | Index normalization |
| Circular indexing | No dedicated array method | Remainder normalization |
| Rotate array | slice() + concat() | Circular indexing or reversal |
| Traverse elements | forEach() | for, while, for...of |
| Traverse indexes | keys() | Counter-controlled loop |
| Traverse values | values() | for...of |
| Traverse index-value pairs | entries() | Indexed loop |
| Analyze neighboring values | Multiple operations | Offset indexing |
| Consecutive grouping | Custom reduction | State tracking |
| Conditional termination | Searching/testing methods | break or return |

### References

- [JavaScript.info - Array methods](https://javascript.info/array-methods)
- [MDN - Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN - Loops and iteration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration)
- [MDN - Iterators and generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_generators)
- [ECMAScript - Indexed collections](https://tc39.es/ecma262/#sec-indexed-collections)

The explanations of slice(), concat(), Symbol.isConcatSpreadable and forEach() follow the subject matter of the supplied Array Methods notes. Manual implementations, additional methods, traversal algorithms, complexity analysis and exercises are expansions. Manual implementations are educational, not complete standards-compliant polyfills. Five of the 50 exercises have selected solutions.

## Next: Part 4 - Searching arrays and advanced search algorithms

- indexOf() and lastIndexOf()
- includes()
- find() and findLast()
- findIndex() and findLastIndex()
- Strict equality, Object.is() and SameValueZero
- Manual searching implementations
- Forward and backward linear search
- Range search and object search
- Multiple matches, first, last and nth occurrences
- All matching indexes and consecutive sequences
- Binary search and insertion positions
- Lower and upper bounds
- Searching rotated sorted arrays
- Search optimization and complexity
- Progressive exercises and solutions

[Previous: Part 2](array-methods-and-loops-part-2.md) | [Next: Part 4](array-methods-and-loops-part-4.md)
