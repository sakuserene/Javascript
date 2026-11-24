# JavaScript Array Methods â€” Part 1

[Previous: Arrays](arrays.md) | Next: Callbacks, iteration and searching (forthcoming)

## Table of contents

1. Understanding array methods
2. Adding and removing elements
3. `splice()`
4. `slice()`
5. `concat()`
6. Summary and exercises

## 1. Understanding array methods

Array methods are functions associated with arrays. They help us process collections. For example, extracting passing scores using a plain loop:

```javascript
const scores = [45, 82, 36, 91, 67, 28];
const passingScores = [];
for (let i = 0; i < scores.length; i++) {
    if (scores[i] >= 50) {
        passingScores.push(scores[i]);
    }
}
console.log(passingScores);
// [82, 91, 67]
```

The equivalent method:

```javascript
const passingScores = scores.filter(score => score >= 50);
```

General syntax: `array.method(arguments)`.

| Method | Return value |
|---|---|
| includes | Boolean |
| indexOf | Number |
| slice | Array |
| join | String |
| find | Matching value or undefined |
| forEach | undefined |
| reduce | Accumulator result |

Mutating methods include push, pop, shift, unshift, splice, sort, reverse, fill and copyWithin. Non-mutating methods include slice, concat, filter, map, find, toSorted, toReversed, with and toSpliced. Callbacks can nevertheless mutate data.

## 2. Adding and removing elements

### push()

Appends values and returns the new length.

```javascript
const tasks = ["Read", "Exercise"];
console.log(tasks.push("Study", "Cook")); // 4
console.log(tasks); // ["Read", "Exercise", "Study", "Cook"]
```

```javascript
function customPush(arr, ...items) {
    for (let i = 0; i < items.length; i++) {
        arr[arr.length] = items[i];
    }
    return arr.length;
}
```

The rest parameter collects arguments. Each assignment at `arr.length` extends the array. No arguments means the current length is returned.

### pop()

Removes and returns the last value, or undefined when empty.

```javascript
function customPop(arr) {
    if (arr.length === 0) return undefined;
    const lastIndex = arr.length - 1;
    const removed = arr[lastIndex];
    arr.length = lastIndex;
    return removed;
}
const tasks = ["Read", "Exercise", "Study"];
console.log(customPop(tasks)); // Study
console.log(tasks); // ["Read", "Exercise"]
```

Save the value before truncating the array.

### unshift()

Prepends values and returns the new length.

```javascript
function customUnshift(arr, ...items) {
    const count = items.length;
    for (let i = arr.length - 1; i >= 0; i--) {
        arr[i + count] = arr[i];
    }
    for (let i = 0; i < count; i++) {
        arr[i] = items[i];
    }
    return arr.length;
}
const numbers = [30, 40, 50];
console.log(customUnshift(numbers, 10, 20)); // 5
console.log(numbers); // [10, 20, 30, 40, 50]
```

Move right from the last index first to avoid overwriting unprocessed values. Source indexes 2, 1, 0 move to 4, 3, 2 respectively.

### shift()

Removes and returns the first value, or undefined when empty.

```javascript
function customShift(arr) {
    if (arr.length === 0) return undefined;
    const removed = arr[0];
    for (let i = 1; i < arr.length; i++) {
        arr[i - 1] = arr[i];
    }
    arr.length--;
    return removed;
}
const numbers = [10, 20, 30, 40];
console.log(customShift(numbers)); // 10
console.log(numbers); // [20, 30, 40]
```

The intermediate states are `[20,20,30,40]`, `[20,30,30,40]`, `[20,30,40,40]`, then `[20,30,40]` after truncation.

These educational implementations assume ordinary dense arrays, not every ECMAScript edge case.

## 3. splice()

`delete arr[2]` leaves a hole and does not reduce length. `splice()` removes, inserts or replaces elements while shifting remaining values.

```javascript
array.splice(start, deleteCount, ...items);
```

Returns an array of removed elements and mutates the original. Negative start indexes count from the end. Omitted deleteCount removes through the end; explicitly passing undefined deletes zero. With no arguments, nothing is removed.

```javascript
const languages = ["JavaScript", "Python", "PHP", "Java"];
console.log(languages.splice(2, 1)); // ["PHP"]
console.log(languages); // ["JavaScript", "Python", "Java"]
```

Each example below starts with `[10,20,30,40,50]`.

| Operation | Modified array | Removed |
|---|---|---|
| splice() | [10,20,30,40,50] | [] |
| splice(0) | [] | [10,20,30,40,50] |
| splice(2) | [10,20] | [30,40,50] |
| splice(2, undefined) | [10,20,30,40,50] | [] |
| splice(2, 0) | [10,20,30,40,50] | [] |
| splice(2, -1) | [10,20,30,40,50] | [] |
| splice(2, 100) | [10,20] | [30,40,50] |
| splice(-100, 1) | [20,30,40,50] | [10] |
| splice(100, 1) | [10,20,30,40,50] | [] |
| splice(100, 0, 60) | [10,20,30,40,50,60] | [] |
| splice(1, 2, 99) | [10,99,40,50] | [20,30] |
| splice(-2, 1) | [10,20,30,50] | [40] |

Fractional indexes and counts truncate toward zero. New length = old length âˆ’ actual deletions + insertions.

### Insertion and replacement

```javascript
const playlist = ["Song A", "Song C", "Song D"];
playlist.splice(1, 0, "Song B");
// ["Song A", "Song B", "Song C", "Song D"]
const colors = ["Red", "Yellow", "Blue"];
console.log(colors.splice(1, 1, "Green")); // ["Yellow"]
// colors: ["Red", "Green", "Blue"]
```

### Plain-loop deletion

```javascript
const numbers = [10, 20, 30, 40, 50, 60];
const start = 1;
const deleteCount = 2;
const removed = [];
for (let i = start; i < start + deleteCount; i++) {
    removed.push(numbers[i]);
}
for (let i = start + deleteCount; i < numbers.length; i++) {
    numbers[i - deleteCount] = numbers[i];
}
numbers.length -= deleteCount;
console.log(numbers); // [10, 40, 50, 60]
console.log(removed); // [20, 30]
```

Preserve removed values, shift left, then truncate. This assumes valid indexes and counts.

### Plain-loop insertion

```javascript
const numbers = [10, 40, 50];
const start = 1;
const items = [20, 30];
for (let i = numbers.length - 1; i >= start; i--) {
    numbers[i + items.length] = numbers[i];
}
for (let i = 0; i < items.length; i++) {
    numbers[start + i] = items[i];
}
console.log(numbers); // [10, 20, 30, 40, 50]
```

Deletion shifts left using forward iteration; insertion shifts right using backward iteration.

### Simplified splice implementation

```javascript
function customSplice(arr, start, deleteCount, ...items) {
    const originalLength = arr.length;
    start = Math.trunc(start);
    start = start < 0 ? Math.max(originalLength + start, 0) : Math.min(start, originalLength);
    deleteCount = Math.max(0, Math.min(Math.trunc(deleteCount), originalLength - start));
    const removed = [];
    for (let i = 0; i < deleteCount; i++) {
        removed.push(arr[start + i]);
    }
    const difference = items.length - deleteCount;
    if (difference > 0) {
        for (let i = originalLength - 1; i >= start + deleteCount; i--) {
            arr[i + difference] = arr[i];
        }
    } else if (difference < 0) {
        for (let i = start + deleteCount; i < originalLength; i++) {
            arr[i + difference] = arr[i];
        }
    }
    for (let i = 0; i < items.length; i++) {
        arr[start + i] = items[i];
    }
    arr.length = originalLength + difference;
    return removed;
}
const numbers = [10, 20, 30, 40, 50];
console.log(customSplice(numbers, 1, 2, 100, 200, 300)); // [20, 30]
console.log(numbers); // [10, 100, 200, 300, 40, 50]
```

Positive difference means expand, negative means shrink, zero means replace directly. Assumes dense arrays and explicitly supplied finite numeric arguments.

### Practical applications

Remove a task by ID:

```javascript
const tasks = [{id: 101, title: "Read chapter"}, {id: 102, title: "Submit assignment"}, {id: 103, title: "Attend meeting"}];
for (let i = 0; i < tasks.length; i++) {
    if (tasks[i].id === 102) {
        tasks.splice(i, 1);
        break;
    }
}
// Remaining IDs: 101, 103
```

Move a chapter to the end:

```javascript
const chapters = ["Chapter 1", "Chapter 2", "Chapter 3", "Chapter 4"];
const [chapter] = chapters.splice(1, 1);
chapters.push(chapter);
// ["Chapter 1", "Chapter 3", "Chapter 4", "Chapter 2"]
```

Remove all negative measurements in place:

```javascript
const measurements = [12, -4, -8, 25, -3, 18];
for (let i = measurements.length - 1; i >= 0; i--) {
    if (measurements[i] < 0) measurements.splice(i, 1);
}
console.log(measurements); // [12, 25, 18]
```

Backward iteration prevents skipping elements after deletion.

## 4. slice()

Extracts a portion without modifying the original. Returns a shallow copy.

```javascript
array.slice(start, end);
```

Start is inclusive (default 0); end is exclusive (default length). Negative indexes count from the end. Nested objects remain shared.

```javascript
const numbers = [10, 20, 30, 40, 50];
console.log(numbers.slice(1, 4)); // [20, 30, 40]
console.log(numbers.slice(2)); // [30, 40, 50]
console.log(numbers.slice(-2)); // [40, 50]
console.log(numbers.slice(-4, -1)); // [20, 30, 40]
console.log(numbers.slice() === numbers); // false
```

| Capability | Expression | Result |
|---|---|---|
| Copy | slice() | [10,20,30,40,50] |
| First two | slice(0,2) | [10,20] |
| Last two | slice(-2) | [40,50] |
| Exclude first | slice(1) | [20,30,40,50] |
| Exclude last | slice(0,-1) | [10,20,30,40] |
| Exclude both ends | slice(1,-1) | [20,30,40] |
| Middle | slice(2,3) | [30] |
| Empty | slice(3,3) | [] |
| Reversed bounds | slice(4,1) | [] |
| Beyond length | slice(2,100) | [30,40,50] |

There is no step parameter. Use a loop with `i += 2` to select every second element.

### Plain-loop implementation

```javascript
function customSlice(arr, start = 0, end = arr.length) {
    const length = arr.length;
    start = Math.trunc(start);
    end = Math.trunc(end);
    start = start < 0 ? Math.max(length + start, 0) : Math.min(start, length);
    end = end < 0 ? Math.max(length + end, 0) : Math.min(end, length);
    const result = [];
    for (let i = start; i < end; i++) {
        result.push(arr[i]);
    }
    return result;
}
console.log(customSlice([10,20,30,40,50], 1, 4)); // [20,30,40]
```

Normalize boundaries, then copy. Native slice preserves holes; this simplified implementation assumes dense arrays and finite numeric arguments.

### Pagination

```javascript
const books = ["Book 1", "Book 2", "Book 3", "Book 4", "Book 5", "Book 6", "Book 7", "Book 8", "Book 9", "Book 10"];
const page = 2;
const pageSize = 3;
const start = (page - 1) * pageSize;
const end = start + pageSize;
console.log(books.slice(start, end)); // ["Book 4", "Book 5", "Book 6"]
```

### Chunking

```javascript
const participants = ["Alice", "Brian", "Carol", "David", "Emma", "Frank", "Grace", "Henry", "Irene", "James"];
const groups = [];
const groupSize = 3;
for (let i = 0; i < participants.length; i += groupSize) {
    groups.push(participants.slice(i, i + groupSize));
}
console.log(groups);
// [["Alice","Brian","Carol"],["David","Emma","Frank"],["Grace","Henry","Irene"],["James"]]
```

### slice versus splice

| Feature | slice | splice |
|---|---|---|
| Purpose | Extract | Modify |
| Mutates | No | Yes |
| Second argument | End index | Deletion count |
| Returns | Selected elements | Removed elements |
| Inserts | No | Yes |

## 5. concat()

Combines arrays and values into a new shallow array without modifying the originals.

```javascript
const first = [10, 20];
const second = [30, 40];
console.log(first.concat(second, [50, 60])); // [10,20,30,40,50,60]
console.log(first.concat(30, [40, 50], 60)); // [10,20,30,40,50,60]
console.log(first.concat([[30,40],[50,60]])); // [10,20,[30,40],[50,60]]
```

Ordinary arrays expand one level; objects are appended individually unless they opt in to spreading.

```javascript
const data = {0: 30, 1: 40, length: 2, [Symbol.isConcatSpreadable]: true};
console.log([10,20].concat(data)); // [10,20,30,40]
const second = [30,40];
second[Symbol.isConcatSpreadable] = false;
console.log([10,20].concat(second)); // [10,20,[30,40]] (conceptual)
```

| Feature | concat | Spread |
|---|---|---|
| New shallow array | Yes | Yes |
| Arbitrary iterables | Not automatically | Yes |
| Symbol.isConcatSpreadable | Yes | No |
| Preserves ordinary holes | Yes | No |

```javascript
const extra = new Set([30,40]);
console.log([10,20,...extra]); // [10,20,30,40]
console.log([10,20].concat(extra)); // [10,20,Set(2) {30,40}]
```

### Plain-loop implementation

```javascript
function customConcat(arr, ...values) {
    const result = [];
    for (let i = 0; i < arr.length; i++) {
        result.push(arr[i]);
    }
    for (let i = 0; i < values.length; i++) {
        const current = values[i];
        if (Array.isArray(current)) {
            for (let j = 0; j < current.length; j++) {
                result.push(current[j]);
            }
        } else {
            result.push(current);
        }
    }
    return result;
}
console.log(customConcat([10,20], [30,40], 50, [60,70])); // [10,20,30,40,50,60,70]
```

This simplified version does not implement symbols, subclass construction or sparse-array preservation.

### Combining records

```javascript
const morning = [{patient: "Alice", time: "09:00"}, {patient: "Brian", time: "10:00"}];
const afternoon = [{patient: "Carol", time: "14:00"}, {patient: "David", time: "15:00"}];
const appointments = morning.concat(afternoon);
console.log(appointments);
```

The result contains all four appointments. Nested objects remain shared.

## Summary

| Method | Capability | Mutates | Returns |
|---|---|---|---|
| push | Append | Yes | New length |
| pop | Remove last | Yes | Removed value |
| unshift | Prepend | Yes | New length |
| shift | Remove first | Yes | Removed value |
| splice | Insert, delete, replace | Yes | Removed array |
| slice | Extract, copy | No | New array |
| concat | Combine | No | New array |

## Practice exercises

- [ ] Implement push without built-in array methods.
- [ ] Implement pop using bracket notation and length.
- [ ] Implement unshift using backward iteration.
- [ ] Implement shift using forward iteration.
- [ ] Remove three consecutive middle elements.
- [ ] Insert multiple elements without deleting anything.
- [ ] Replace two elements with four.
- [ ] Move the first element to the end.
- [ ] Move the last two elements to the beginning.
- [ ] Remove all occurrences of a value using splice.
- [ ] Extract first five and last five elements separately.
- [ ] Divide an array into approximately equal halves.
- [ ] Extract every third element with a loop.
- [ ] Implement reusable pagination.
- [ ] Divide 23 records into batches of five.
- [ ] Combine three arrays without modifying originals.
- [ ] Combine arrays and individual values.
- [ ] Compare concat and spread with a Set.
- [ ] Demonstrate shallow copying with concat.
- [ ] Implement simplified splice supporting insertion, deletion and replacement.

Next: Section 6 â€” Callbacks, followed by forEach and searching methods.
