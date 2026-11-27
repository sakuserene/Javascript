# Lesson 19 - Frequency Counting and Grouping

[Previous: Working With Strings](18-working-with-strings.md) | Next: Generating Patterns (planned)

In previous lessons, we learned to count qualifying elements, remove duplicates, search arrays and maintain multiple accumulators. Now we need a separate counter or collection for every distinct category encountered.

## Table of contents

- [Frequency tables](#1-frequency-tables)
- [Grouping values](#2-grouping-values)
- [Aggregating groups](#3-aggregating-groups)
- [Tracking previously seen values](#4-tracking-previously-seen-values)
- [Summary](#5-summary)
- [Integrated exercises](#6-integrated-exercises)

## 1. Frequency tables

A frequency is the number of occurrences of a value. A frequency table associates each distinct value with its count. For grades A, B, A, C, B, A, the required counts are A: 3, B: 2, C: 1. The sum of the counts must equal the number of processed elements.

### From one counter to multiple counters

If the only possible grades are A and B, separate variables are sufficient. Each begins at zero and is incremented only when its grade occurs. But if categories are discovered during traversal, we cannot know how many variables to declare. We need a structure that grows with the discovered categories.

### Parallel arrays

Maintain one array of distinct values and another of counts. Corresponding indexes describe the same category. Search the distinct array before updating: a missing category needs a new entry with count one; an existing category needs its counter incremented.

```javascript
const grades = ["A", "B", "A", "C", "B", "A"];
const keys = [];
const counts = [];
for (let i = 0; i < grades.length; i++) {
  let position = -1;
  for (let j = 0; j < keys.length; j++) {
    if (keys[j] === grades[i]) {
      position = j;
      break;
    }
  }
  if (position === -1) {
    keys.push(grades[i]);
    counts.push(1);
  } else {
    counts[position]++;
  }
}
console.log(keys.join(","), counts.join(","));
```

Output: `A,B,C 3,2,1`. The `join(separator)` array method returns a string containing the elements separated by the supplied string; it does not modify the array. It is used here only to display compact output.

| Input | Keys | Counts |
|---|---|---|
| A | A | 1 |
| B | A,B | 1,1 |
| A | A,B | 2,1 |
| C | A,B,C | 2,1,1 |
| B | A,B,C | 2,2,1 |
| A | A,B,C | 3,2,1 |

Reset position to -1 for every input. Otherwise a failed search can reuse a previous successful position. The two arrays must always have equal lengths. This solution preserves first occurrence order but repeatedly searches previously discovered categories.

### Direct indexing for bounded integers

Suppose ratings are integers from 1 through 5. Each rating can identify its own counter index. Reserve index zero and initialize every counter before processing.

```javascript
const ratings = [5, 3, 5, 1, 3, 5];
const counts = [];
for (let i = 0; i <= 5; i++) counts.push(0);
for (let i = 0; i < ratings.length; i++) counts[ratings[i]]++;
for (let rating = 1; rating <= 5; rating++) console.log(rating, counts[rating]);
```

Output:

```text
1 1
2 0
3 2
4 0
5 3
```

The expression counts[ratings[i]] first retrieves the rating, then selects its counter. For the first input, ratings[0] is 5, so counts[5] changes from 0 to 1. Unlike parallel arrays, no search is necessary. Unobserved categories retain zero counts.

Incorrect initialization: `const counts = []; counts[5]++;` produces NaN at index 5 because the old value is undefined. Incrementing does not automatically initialize a counter.

### Negative ranges and invalid values

For integer temperatures from -2 through 2, use an offset: index = value - minimum. The reverse mapping is value = index + minimum. This avoids treating negative property names as array indexes.

Number.isInteger(value) returns true only for numbers that are integers, not numeric strings, fractions, NaN or Infinity. Validate before indexing.

```javascript
const readings = [-2, 0, 2, -2, 3, 0.5];
const minimum = -2;
const maximum = 2;
const counts = [];
let rejected = 0;
for (let n = minimum; n <= maximum; n++) counts.push(0);
for (let i = 0; i < readings.length; i++) {
  const value = readings[i];
  if (!Number.isInteger(value) || value < minimum || value > maximum) {
    rejected++;
    continue;
  }
  counts[value - minimum]++;
}
console.log(counts.join(","), rejected);
```

Output: `2,0,1,0,1 2`. The sum of the counts plus rejected equals six. A very wide range with few observed values wastes storage; use a key-based table instead.

### Practice

1. Count ratings [1,5,2,5,1,5]. Expected counts for 1 through 5: [2,1,0,0,3].
2. Count integers from -3 through 1 in [-3,1,0,-3,2]. Reject out-of-range values. Expected counts [2,0,0,1,1], rejected 1.
3. Explain why a direct-index table is inappropriate for two identifiers 1 and 1000000000.
4. Trace the parallel-array solution on [4,4,2,4]. Expected keys [4,2], counts [3,1].

### Objects as frequency tables

An object stores properties identified by keys. Unlike array positions, keys can be descriptive strings. An object literal uses braces; each property has a key and value separated by a colon. Dot notation uses a fixed property name; bracket notation evaluates an expression to obtain the key.

```javascript
const counts = { A: 2, B: 1 };
const grade = "A";
console.log(counts.A, counts[grade], counts.grade);
counts[grade]++;
counts.C = 1;
console.log(counts.A, counts.C);
```

Output:

```text
2 2 undefined
3 1
```

counts.grade means the literal key "grade", not the contents of the variable. Missing properties normally return undefined, but ordinary objects inherit properties from a prototype (an object supplying shared properties). Names such as constructor and toString can therefore already exist even when we have never counted them.

Object.hasOwn(object, key) tests whether the property belongs directly to the object. Object.create(null) creates an object without a prototype, avoiding inherited-name collisions and the special inherited __proto__ setter. Use both for a robust string-key table.

```javascript
const words = ["cat", "dog", "cat", "constructor", "__proto__", "dog"];
const counts = Object.create(null);
const keys = [];
for (let i = 0; i < words.length; i++) {
  const key = words[i];
  if (!Object.hasOwn(counts, key)) {
    counts[key] = 0;
    keys.push(key);
  }
  counts[key]++;
}
for (let i = 0; i < keys.length; i++) console.log(keys[i], counts[keys[i]]);
```

Output:

```text
cat 2
dog 2
constructor 1
__proto__ 1
```

The keys array records discovery order. Alternatively, Object.keys(object) returns an array of its own enumerable string keys. Enumerable means eligible for ordinary property enumeration; properties assigned as above are enumerable. Integer-index-like keys appear in numeric order, not necessarily discovery order.

```javascript
const counts = Object.create(null);
counts[10] = 1;
counts[2] = 2;
counts.cat = 3;
const keys = Object.keys(counts);
console.log(keys.join(","));
```

Output: `2,10,cat`.

Object property keys are strings or symbols. Numeric keys are converted to strings. Consequently 1 and "1" identify the same property. If the requirements distinguish them, an object table is unsuitable.

### Map-based frequency tables

A Map is a collection of key-value associations. `new Map()` creates an empty collection. The new operator constructs an instance of a built-in type. A Map does not convert numeric keys to strings.

- map.set(key, value) inserts or replaces an association and returns the Map itself.
- map.get(key) retrieves the associated value, or undefined if absent.
- map.has(key) returns a Boolean indicating whether the key exists.
- map.delete(key) removes an association and returns whether one existed.
- map.size is the number of associations, not the sum of their values.

```javascript
const counts = new Map();
counts.set(1, 2);
counts.set("1", 3);
console.log(counts.get(1), counts.get("1"), counts.size);
console.log(counts.has(2), counts.get(2));
console.log(counts.delete(1), counts.size);
```

Output:

```text
2 3 2
false undefined
true 1
```

A stored undefined value is not the same as an absent key: has distinguishes them. Map uses SameValueZero equality: NaN matches NaN, positive and negative zero match, and objects match only by identity. Strict equality differs for NaN.

To stay within traditional loops, retain discovered keys in an array rather than introducing iterator loops.

```javascript
const values = [1, "1", 1, NaN, NaN];
const counts = new Map();
const keys = [];
for (let i = 0; i < values.length; i++) {
  const key = values[i];
  if (!counts.has(key)) {
    counts.set(key, 0);
    keys.push(key);
  }
  counts.set(key, counts.get(key) + 1);
}
for (let i = 0; i < keys.length; i++) console.log(typeof keys[i], keys[i], counts.get(keys[i]));
```

Output:

```text
number 1 2
string 1 1
number NaN 2
```

The typeof operator returns a string describing a value's type; here it distinguishes the numeric and string keys in the output. Notice that incrementing the result of get directly would not update the Map; set must store the new count.

### Finding the most frequent value

Build the frequency table first, then search the distinct categories for the largest count. Starting the maximum at zero works because every observed category has a positive count. To handle ties, clear the result when a strictly larger count is found, and append when the count equals the current maximum.

```javascript
const values = [4, 2, 4, 3, 2];
const counts = new Map();
const keys = [];
for (let i = 0; i < values.length; i++) {
  const key = values[i];
  if (!counts.has(key)) { counts.set(key, 0); keys.push(key); }
  counts.set(key, counts.get(key) + 1);
}
let maximum = 0;
const modes = [];
for (let i = 0; i < keys.length; i++) {
  const count = counts.get(keys[i]);
  if (count > maximum) {
    maximum = count;
    modes.length = 0;
  }
  if (count === maximum) modes.push(keys[i]);
}
console.log(maximum, modes.join(","));
```

Output: `2 4,2`. For an empty input, modes remains empty and maximum remains zero. A mode is a most frequently occurring value; this implementation returns every tied mode, including all distinct values when all counts are one.

### Choosing a table

| Structure | Appropriate use | Limitation |
|---|---|---|
| Parallel arrays | Small collections, first-occurrence order | Repeated linear searches |
| Direct-index array | Small bounded integer range | Storage depends on range |
| Null-prototype object | String categories | Numeric keys become strings |
| Map | Dynamic or mixed-type keys | Requires Map operations |

### Practice

1. Count characters in "banana". Expected b:1, a:3, n:2.
2. Explain why an object table combines 1 and "1", but a Map does not.
3. Find all modes of [8,3,8,3,5]. Expected [8,3], frequency 2.
4. Count words ["toString","cat","toString","__proto__"] without inherited-name collisions.
5. Modify the mode example to collect values occurring exactly once. Test empty input and all-identical input.

## 2. Grouping values

Counting retains only the number of items in a category. Grouping retains the items themselves. A group key determines which collection receives the current item.

### Fixed groups

Partition numbers into even and odd groups. Since the categories are known, two arrays suffice.

```javascript
const values = [3, 8, 2, 5, 0, -3];
const even = [];
const odd = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] % 2 === 0) even.push(values[i]);
  else odd.push(values[i]);
}
console.log(even.join(","), odd.join(","));
```

Output: `8,2,0 3,5,-3`. Test oddness by failure of the even test, not remainder === 1, because negative odd numbers have remainder -1.

### Dynamic groups

Group words by their lengths. The possible lengths need not be known in advance. Each key maps to an array rather than a number. Create that array only when the key first occurs.

```javascript
const words = ["cat", "a", "dog", "to", "sun", "I"];
const groups = new Map();
const keys = [];
for (let i = 0; i < words.length; i++) {
  const word = words[i];
  const key = word.length;
  if (!groups.has(key)) {
    groups.set(key, []);
    keys.push(key);
  }
  groups.get(key).push(word);
}
for (let i = 0; i < keys.length; i++) console.log(keys[i], groups.get(keys[i]).join(","));
```

Output:

```text
3 cat,dog,sun
1 a,I
2 to
```

The array retrieved by get is the same array stored in the Map. Mutating it with push updates the group's contents. Do not assign the return value of push to the Map: push returns the new length, not the array.

Another mistake is creating one shared array outside the loop and storing it under every new key. All groups would then refer to the same array. Allocate a fresh array for each new group.

### Grouping records

An object can represent a record with several related properties. An array of records allows each item to carry its category and value together. Access a record using an index, then access its named properties using dot notation.

```javascript
const sales = [
  { region: "East", amount: 30 },
  { region: "West", amount: 20 },
  { region: "East", amount: 50 }
];
const groups = new Map();
for (let i = 0; i < sales.length; i++) {
  const sale = sales[i];
  if (!groups.has(sale.region)) groups.set(sale.region, []);
  groups.get(sale.region).push(sale);
}
console.log(groups.get("East").length, groups.get("East")[1].amount);
```

Output: `2 50`. Grouping stores references to the original record objects. Changing a record through a group also changes that original record. If independent copies are required, create new record objects with the required properties.

### Practice

1. Group [10,-2,0,8,-5,0] into negative, zero and positive arrays. Preserve order.
2. Group ["a","bb","c","ddd","ee"] by length. Expected 1:[a,c], 2:[bb,ee], 3:[ddd].
3. Group records by region, then report the number of records in each group. Test an empty input.
4. Explain why resetting a group to [] on every occurrence loses earlier items.

## 3. Aggregating groups

If only totals are required, storing every original record is unnecessary. Instead, maintain an accumulator for each group. The key selects the accumulator just as it selected the counter in frequency counting.

### Group totals

```javascript
const sales = [
  { region: "East", amount: 30 },
  { region: "West", amount: 20 },
  { region: "East", amount: 50 }
];
const totals = new Map();
const keys = [];
for (let i = 0; i < sales.length; i++) {
  const sale = sales[i];
  if (!totals.has(sale.region)) {
    totals.set(sale.region, 0);
    keys.push(sale.region);
  }
  totals.set(sale.region, totals.get(sale.region) + sale.amount);
}
for (let i = 0; i < keys.length; i++) console.log(keys[i], totals.get(keys[i]));
```

Output:

```text
East 80
West 20
```

The initialization value is zero for summation. For a maximum, zero may be wrong if all amounts are negative; initialize from the first qualifying observation instead.

### Multiple statistics per group

To calculate group averages, keep both sum and count. To calculate extrema, retain minimum and maximum as well. A record object can hold these related state variables together.

```javascript
const sales = [
  { region: "East", amount: 30 },
  { region: "West", amount: 20 },
  { region: "East", amount: 50 },
  { region: "West", amount: 10 },
  { region: "East", amount: 40 }
];
const stats = new Map();
const keys = [];
for (let i = 0; i < sales.length; i++) {
  const sale = sales[i];
  if (!stats.has(sale.region)) {
    stats.set(sale.region, { sum: 0, count: 0, min: sale.amount, max: sale.amount });
    keys.push(sale.region);
  }
  const group = stats.get(sale.region);
  group.sum += sale.amount;
  group.count++;
  if (sale.amount < group.min) group.min = sale.amount;
  if (sale.amount > group.max) group.max = sale.amount;
}
for (let i = 0; i < keys.length; i++) {
  const group = stats.get(keys[i]);
  console.log(keys[i], group.sum, group.count, group.sum / group.count, group.min, group.max);
}
```

Output:

```text
East 120 3 40 30 50
West 30 2 15 10 20
```

The overall average is 150 / 5 = 30, not (40 + 15) / 2 = 27.5. Group averages must be weighted by their observation counts when calculating an overall average.

| Processed sale | East sum/count | West sum/count |
|---|---|---|
| East 30 | 30/1 | absent |
| West 20 | 30/1 | 20/1 |
| East 50 | 80/2 | 20/1 |
| West 10 | 80/2 | 30/2 |
| East 40 | 120/3 | 30/2 |

### Multiple grouping dimensions

Suppose sales must be grouped by region and product. A nested Map avoids ambiguity from concatenating keys with separators that might already occur in the data. The outer key selects a region table, the inner key selects a product total.

```javascript
const sales = [
  { region: "East", product: "Pen", amount: 10 },
  { region: "East", product: "Book", amount: 30 },
  { region: "West", product: "Pen", amount: 20 },
  { region: "East", product: "Pen", amount: 5 }
];
const totals = new Map();
for (let i = 0; i < sales.length; i++) {
  const sale = sales[i];
  if (!totals.has(sale.region)) totals.set(sale.region, new Map());
  const region = totals.get(sale.region);
  if (!region.has(sale.product)) region.set(sale.product, 0);
  region.set(sale.product, region.get(sale.product) + sale.amount);
}
console.log(totals.get("East").get("Pen"), totals.get("East").get("Book"), totals.get("West").get("Pen"));
```

Output: `15 30 20`. Before reading an arbitrary region, check its existence; calling get on undefined would fail. For a complete report, retain region keys and product keys per region using the same discovery-array technique introduced earlier.

### Practice

1. For records A:10, B:5, A:20, B:15, calculate group totals, counts and averages. Expected A:30,2,15; B:20,2,10.
2. Calculate the maximum for each category in A:-8, B:-2, A:-3, B:-7. Expected A:-3, B:-2. Explain why zero initialization fails.
3. Find the group with the highest average in A:10, A:30, B:25. Expected B.
4. Extend the nested Map example to report all region-product totals in discovery order.

## 4. Tracking previously seen values

Sometimes the only question is whether a value has occurred before. A counter or group array stores more information than necessary. A Set stores distinct values without associated counts.

### Set operations

new Set() creates an empty collection. add(value) inserts a value and returns the Set; inserting an existing value does not increase its size. has(value) tests membership. delete(value) removes a value and returns whether it existed. size reports the number of distinct values. clear() removes all values and returns undefined. Like Map, Set uses SameValueZero equality.

```javascript
const seen = new Set();
seen.add(5);
seen.add(5);
seen.add("5");
console.log(seen.size, seen.has(5), seen.has(9));
console.log(seen.delete(5), seen.has(5));
seen.clear();
console.log(seen.size);
```

Output:

```text
2 true false
true false
0
```

### Stable duplicate removal

Check membership before insertion. If the value has not been encountered, append it to the result and mark it seen. The result array preserves order without requiring iteration over the Set.

```javascript
const values = [4, 2, 4, 3, 2];
const seen = new Set();
const unique = [];
for (let i = 0; i < values.length; i++) {
  if (!seen.has(values[i])) {
    unique.push(values[i]);
    seen.add(values[i]);
  }
}
console.log(unique.join(","));
```

Output: `4,2,3`. Checking after add would always report true. Resetting the Set inside the loop would prevent remembering earlier values.

### Repeated occurrences versus distinct repeated values

For [4,2,4,4,2], repeated occurrences after the first are [4,4,2]. Distinct repeated values are [4,2]. The second requirement needs a second Set to remember which repeated values have already been reported.

```javascript
const values = [4, 2, 4, 4, 2];
const seen = new Set();
const reported = new Set();
const repeated = [];
for (let i = 0; i < values.length; i++) {
  const value = values[i];
  if (seen.has(value) && !reported.has(value)) {
    repeated.push(value);
    reported.add(value);
  }
  seen.add(value);
}
console.log(repeated.join(","));
```

Output: `4,2`. This order reflects the first repeated occurrence, which can differ from first appearance order. For first appearance order, build the distinct list and counts first, then select counts greater than one.

### Membership across arrays

Build a Set from the second array using a loop. Then test each first-array value against it. A second Set or deletion from the membership Set can prevent duplicate output.

```javascript
const first = [2, 4, 2, 6, 8];
const second = [8, 2, 10];
const available = new Set();
const common = [];
for (let i = 0; i < second.length; i++) available.add(second[i]);
for (let i = 0; i < first.length; i++) {
  if (available.has(first[i])) {
    common.push(first[i]);
    available.delete(first[i]);
  }
}
console.log(common.join(","));
```

Output: `2,8`. Deleting is appropriate because each value may be reported only once and the membership Set is temporary. If it must be reused, retain it and track reported values separately.

### Practice

1. Remove duplicates from [3,1,3,2,1]. Expected [3,1,2].
2. Find distinct repeated values in [1,2,2,1,2]. Expected [2,1] in repeat-discovery order.
3. Find distinct common values of [5,2,5,8] and [8,5,5]. Expected [5,8].
4. Explain why a Set alone cannot determine the most frequent value.
5. Test stable duplicate removal on [], [7], [7,7,7] and [1,2,3].

## 5. Summary

Choose the state according to the question: a Set answers membership, a frequency table answers how many, a group array retains items, and group accumulators summarize items. Initialize state once per newly discovered key, not once per observation. Decide key equality, output order, invalid-input policy and tie handling explicitly. Verify that counts sum to the accepted observation count and that group totals sum to the overall total. All traversals in this chapter use traditional loops; iterator-based traversal is deferred.

## 6. Integrated exercises

1. **Grade report.** Count ["A","B","A","C","B","A","D"]. Report counts in first appearance order and all most frequent grades. Expected A:3 B:2 C:1 D:1; mode A.
2. **Bounded histogram.** Accept integer scores 0 through 4 from [0,4,2,4,-1,2,1,5]. Expected counts [1,1,2,0,2], rejected 2.
3. **Word groups.** Group ["red","blue","cat","a","green"] by length. Expected 3:[red,cat], 4:[blue], 1:[a], 5:[green].
4. **Sales report.** Records North:20, South:50, North:40, South:10, North:30. Report sum, count, average, minimum and maximum per region. North:90,3,30,20,40; South:60,2,30,10,50. Overall average 30.
5. **Distinct and singleton values.** Input [4,2,4,3,2,8]. Distinct [4,2,3,8]; singleton [3,8]; repeated distinct [4,2].
6. **Mixed keys.** Count [1,"1",1,"1",2]. Preserve types. Expected numeric 1:2, string 1:2, numeric 2:1.
7. **Debugging.** A programmer initializes counts[key] = 0 on every iteration before incrementing. Predict counts for ["a","a","b"], explain and correct. Correct counts a:2, b:1.
8. **Grouped validation.** Accept records with integer marks 0 through 100. Input A:80, B:-5, A:40, B:100, A:120. Expected A count 2 average 60; B count 1 average 100; rejected 2.
9. **Unique intersection and difference.** For [1,2,2,3,4] and [2,4,5], return distinct common [2,4] and distinct first-only [1,3]. Preserve first-array order.
10. **Independent design.** Group transactions by account and maintain deposit and withdrawal totals separately. Input A deposit 50, B deposit 20, A withdrawal 15, B withdrawal 5. Expected A balance 35, B balance 15, assuming initial balances zero. Decide how to reject unknown operation names and negative amounts before updating state.

[Previous: Working With Strings](18-working-with-strings.md) | Next: Generating Patterns (planned)
