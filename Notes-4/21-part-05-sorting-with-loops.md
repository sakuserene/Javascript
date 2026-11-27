# Lesson 21 - Part 5: Sorting With Loops

[Previous: Binary Search](21-part-04-reducing-search-space-and-binary-search.md) | [Next: Integrated Exercises (planned)](21-part-06-integrated-exercises.md)

## Table of contents

- [Sorting fundamentals](#sorting-fundamentals)
- [Bubble sort](#bubble-sort)
- [Selection sort](#selection-sort)
- [Insertion sort](#insertion-sort)
- [Comparing algorithms](#comparing-algorithms)
- [Integrated practice](#integrated-practice)

## Sorting fundamentals

Sorting rearranges elements according to an ordering rule. Ascending numerical order means each element is no greater than its successor; descending order reverses that relationship. Sorting must preserve every original occurrence, including duplicates. Merely producing an ordered array is not sufficient if values have been lost.

For example, [4, 1, 3, 1] becomes [1, 1, 3, 4]. The result [1, 3, 4] is ordered but is not a correct sort because one occurrence disappeared.

### Checking order before sorting

To check ascending order, compare each element with its predecessor. A single adjacent inversion disproves sortedness. Start at index 1 because index 0 has no predecessor.

```javascript
const values = [1, 4, 3, 7];
let sorted = true;
for (let i = 1; i < values.length; i++) {
  if (values[i - 1] > values[i]) {
    sorted = false;
    break;
  }
}
console.log(sorted);
```

Expected output:

```text
false
```

At i = 2, i - 1 evaluates to 1. The comparison retrieves 4 and 3. Since 4 > 3, the flag becomes false. Empty and singleton arrays are already sorted because there is no adjacent pair violating the rule.

### Exchanges and preservation

An exchange must preserve both original values. Assigning a[0] = a[1] followed by a[1] = a[0] loses the original first value. Preserve it before overwriting.

```javascript
const a = [8, 2];
const temporary = a[0];
a[0] = a[1];
a[1] = temporary;
console.log(a.join(','));
```

Expected output:

```text
2,8
```

The array method join(separator) returns a string containing the elements separated by the supplied string. Here join(',') makes output easy to verify. It does not change the array. For instance [1,2].join('-') returns '1-2'. We use it only to display results, not to implement sorting.

### Records, keys and stability

A key is the value used to determine order. A student record can be sorted by score without losing its name. A stable sort preserves the original relative order of records whose keys compare equal. For example, A:70, B:60, C:70 becomes B:60, A:70, C:70 in a stable ascending sort. Stability matters when equal-key records already have a meaningful order.

All numerical examples below assume dense arrays of finite numbers, with no changes to length during sorting. JavaScript Number comparisons are not suitable for NaN without a separately defined ordering rule.

## Bubble sort

Imagine moving through [5, 2, 4, 1] from left to right. Compare neighboring elements and exchange them if they are in the wrong order.

| Comparison | Array after comparison |
|---|---|
| 5 and 2 | 2,5,4,1 |
| 5 and 4 | 2,4,5,1 |
| 5 and 1 | 2,4,1,5 |

The largest value has reached its final position. The other values are not necessarily sorted. Another pass is needed.

### Deriving the nested loops

A pass is one traversal of the remaining unsorted region. The inner loop compares indexes j and j + 1. If the last unsorted index is end, j must be strictly smaller than end; otherwise j + 1 would exceed the region.

After each pass, the largest remaining value is at end. Decrease end by one. Stop when only one unsorted position remains.

```javascript
const a = [5, 2, 4, 1];
for (let end = a.length - 1; end > 0; end--) {
  for (let j = 0; j < end; j++) {
    if (a[j] > a[j + 1]) {
      const temporary = a[j];
      a[j] = a[j + 1];
      a[j + 1] = temporary;
    }
  }
}
console.log(a.join(','));
```

Expected output:

```text
1,2,4,5
```

For end = 3 and j = 2, j + 1 is 3, so the comparison is between the final two elements of the unsorted region. When end decreases to 2, index 3 is excluded because it already contains its final value.

| End | Array after pass | Comparisons |
|---|---|---|
| 3 | 2,4,1,5 | 3 |
| 2 | 2,1,4,5 | 2 |
| 1 | 1,2,4,5 | 1 |

The sorted suffix grows from the right. After each pass every element in that suffix is in its final position. This maintained fact is a loop invariant: a property that remains true at a specified point in each iteration. It explains why shrinking the boundary is safe.

### Early termination

If a complete pass makes no exchanges, all adjacent pairs in the unsorted region are already ordered. The sorted suffix is already correct, so the whole array is sorted. A Boolean flag can record whether a pass changed anything.

```javascript
const a = [1, 2, 4, 3, 5];
let passes = 0;
for (let end = a.length - 1; end > 0; end--) {
  let swapped = false;
  passes++;
  for (let j = 0; j < end; j++) {
    if (a[j] > a[j + 1]) {
      const temporary = a[j];
      a[j] = a[j + 1];
      a[j + 1] = temporary;
      swapped = true;
    }
  }
  if (!swapped) break;
}
console.log(a.join(','));
console.log(passes);
```

Expected output:

```text
1,2,3,4,5
2
```

The first pass exchanges 4 and 3. The second pass discovers no inversions and stops. Reset swapped inside the outer loop; resetting it inside the inner loop would forget earlier exchanges. Checking it before the inner loop completes could stop before reaching an inversion.

### Descending order and stable records

To sort descending, exchange when the left value is smaller than the right value. For ascending records, compare their keys, but exchange entire objects. Strict > preserves the relative order of equal keys because equal neighboring records never cross.

```javascript
const a = [{name:'A',score:70},{name:'B',score:60},{name:'C',score:70}];
for (let end = a.length - 1; end > 0; end--) {
  for (let j = 0; j < end; j++) {
    if (a[j].score > a[j + 1].score) {
      const temporary = a[j];
      a[j] = a[j + 1];
      a[j + 1] = temporary;
    }
  }
}
for (let i = 0; i < a.length; i++) console.log(a[i].name, a[i].score);
```

Expected output:

```text
B 60
A 70
C 70
```

The expression a[j + 1].score first calculates the neighboring index, retrieves the record, then retrieves its score. Exchanging only scores would incorrectly associate scores with different students.

### Practice: descending bubble sort

Sort [2, 7, 1, 7] descending. Preserve both sevens. Count exchanges.

#### Worked solution

```javascript
const a = [2, 7, 1, 7];
let swaps = 0;
for (let end = a.length - 1; end > 0; end--) {
  for (let j = 0; j < end; j++) {
    if (a[j] < a[j + 1]) {
      const temporary = a[j];
      a[j] = a[j + 1];
      a[j + 1] = temporary;
      swaps++;
    }
  }
}
console.log(a.join(','));
console.log(swaps);
```

Expected output:

```text
7,7,2,1
3
```

The first pass exchanges 2 with 7 and 1 with 7, producing [7,2,7,1]. The second pass exchanges 2 with 7. Equal sevens are not exchanged. The counter increases only when a real exchange occurs.

## Selection sort

Bubble sort moves a large value gradually through adjacent exchanges. Another strategy is to locate the smallest remaining value first, then move it directly into its final position.

For [5, 2, 4, 1], search the entire array for its minimum. The minimum is 1 at index 3. Exchange indexes 0 and 3. The array becomes [1, 2, 4, 5]. Index 0 is now final. Repeat the search starting at index 1.

### Deriving the implementation

The outer index start identifies the first unsorted position. Initialize minIndex to start, not zero: the already sorted prefix must not participate in the search. Examine subsequent positions, updating minIndex whenever a smaller value is found. Only after completing the search should the exchange occur.

```javascript
const a = [5, 2, 4, 1];
for (let start = 0; start < a.length - 1; start++) {
  let minIndex = start;
  for (let j = start + 1; j < a.length; j++) {
    if (a[j] < a[minIndex]) minIndex = j;
  }
  if (minIndex !== start) {
    const temporary = a[start];
    a[start] = a[minIndex];
    a[minIndex] = temporary;
  }
}
console.log(a.join(','));
```

Expected output:

```text
1,2,4,5
```

During the first search, minIndex begins at 0 (value 5), becomes 1 (value 2), remains 1 when 4 is examined, and becomes 3 (value 1). The assignment minIndex = j records a position, not the value itself. We need the position to perform the later exchange.

The condition minIndex !== start avoids unnecessary self-exchanges. It is not required for numerical correctness but reduces writes.

### A less convenient input

| Start | Minimum index | Array after exchange |
|---|---|---|
| 0 | 2 | 1,4,6,2 |
| 1 | 3 | 1,2,6,4 |
| 2 | 3 | 1,2,4,6 |

This trace begins with [6,4,1,2]. The sorted prefix grows from the left. Each pass finds the smallest element of the remaining suffix, so the prefix contains the smallest values in their final order.

### Why ordinary selection sort is not stable

Consider records A:2, B:2, C:1. The first exchange moves C into index 0 and A into index 2. The result is C:1, B:2, A:2. A and B have reversed their original relative order even though we never deliberately exchanged equal keys.

A stable variation removes the selected minimum by shifting intervening elements one position to the right, then placing the minimum at start. This preserves their relative order.

```javascript
const a = [{name:'A',key:2},{name:'B',key:2},{name:'C',key:1}];
for (let start = 0; start < a.length - 1; start++) {
  let minIndex = start;
  for (let j = start + 1; j < a.length; j++) {
    if (a[j].key < a[minIndex].key) minIndex = j;
  }
  const minimum = a[minIndex];
  for (let j = minIndex; j > start; j--) {
    a[j] = a[j - 1];
  }
  a[start] = minimum;
}
for (let i = 0; i < a.length; i++) console.log(a[i].name);
```

Expected output:

```text
C
A
B
```

The minimum is saved before shifting. Shifting from right to left avoids destroying unread elements: a[2] = a[1] happens before a[1] = a[0]. The strict comparison selects the first occurrence of the minimum. Stability is gained at the cost of potentially many additional writes.

### Practice: sort only a selected range

Sort indexes 1 through 4 of [99, 8, 3, 6, 2, 88] ascending, leaving the other positions unchanged.

#### Worked solution

```javascript
const a = [99, 8, 3, 6, 2, 88];
const left = 1;
const right = 4;
for (let start = left; start < right; start++) {
  let minIndex = start;
  for (let j = start + 1; j <= right; j++) {
    if (a[j] < a[minIndex]) minIndex = j;
  }
  if (minIndex !== start) {
    const temporary = a[start];
    a[start] = a[minIndex];
    a[minIndex] = temporary;
  }
}
console.log(a.join(','));
```

Expected output:

```text
99,2,3,6,8,88
```

The first search finds 2 at index 4 and exchanges it with 8 at index 1. The next searches find 3 and 6 already correctly positioned. The inner condition j <= right includes index 4, while the outer condition start < right avoids a redundant final search. The boundaries are assumed valid integers within the array.

## Insertion sort

Insertion sort builds a sorted prefix by inserting one new element at a time. Imagine arranging playing cards in your hand: the cards already held are ordered, and each new card is inserted into the appropriate position.

Start with [5,2,4,1]. The first element alone is sorted. Insert 2 before 5, producing [2,5,4,1]. Next insert 4 between 2 and 5. Finally insert 1 before every element in the prefix.

### Creating room without losing values

Suppose the sorted prefix is [2,5] and the next value is 4. Save 4 in a variable before shifting 5 right. The array temporarily contains [2,5,5,1]. Place the saved 4 into index 1. The temporary duplicate is harmless because the displaced value has been preserved.

### Deriving the loop

The outer index i identifies the next value to insert. Start at 1 because the prefix containing only index 0 is already sorted. Save a[i] as key. Set j = i - 1 to examine the last element of the sorted prefix. While j is valid and a[j] is greater than key, shift that element to j + 1 and decrease j. Finally place key at j + 1.

```javascript
const a = [5, 2, 4, 1];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
console.log(a.join(','));
```

Expected output:

```text
1,2,4,5
```

During i = 2, key is 4 and j is 1. The condition 1 >= 0 && 5 > 4 is true. The assignment a[2] = a[1] copies 5 to index 2. Decreasing j gives 0. Now 2 > 4 is false, so the loop stops. The final assignment a[0 + 1] = 4 inserts the saved value into index 1.

During i = 3, key is 1. All three prefix elements shift right. Eventually j becomes -1. The condition j >= 0 is false, so logical AND short-circuits before reading a[-1]. The final insertion index is -1 + 1 = 0.

| i | Key | Shifted values (in order) | Array after insertion |
|---|---|---|---|
| 1 | 2 | 5 | 2,5,4,1 |
| 2 | 4 | 5 | 2,4,5,1 |
| 3 | 1 | 5,4,2 | 1,2,4,5 |

The invariant is that indexes 0 through i - 1 are sorted before each outer iteration. Inserting key extends that sorted prefix by one element.

### Common mistakes

Using a[j] = a[j + 1] shifts in the wrong direction and destroys a prefix value. Forgetting to save key means a[i] may be overwritten before insertion. Using a[j] = key after the while loop places the key one position too far left; j identifies the last value that did not need shifting, or -1 if every value shifted.

Using >= instead of > shifts equal values and can reverse equal-key records. Strict > preserves stability.

### Inserting into an already sorted array

Sometimes we need to insert one new value rather than sort an entire collection. Append the new value to create room, then apply a single insertion pass.

```javascript
const a = [2, 4, 7, 9];
const key = 5;
a.push(key);
let j = a.length - 2;
while (j >= 0 && a[j] > key) {
  a[j + 1] = a[j];
  j--;
}
a[j + 1] = key;
console.log(a.join(','));
```

Expected output:

```text
2,4,5,7,9
```

After push, length is 5. Subtracting 2 gives index 3, the last original element. Shift 9 and 7, stop at 4, and insert at index 2. This also works for an initially empty array: j becomes -1 and the key is assigned at index 0.

### Practice: stable descending insertion sort

Sort records A:50, B:80, C:50, D:90 by descending score. Preserve the order of equal scores.

#### Worked solution

```javascript
const a = [{name:'A',score:50},{name:'B',score:80},{name:'C',score:50},{name:'D',score:90}];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j].score < key.score) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
for (let i = 0; i < a.length; i++) console.log(a[i].name, a[i].score);
```

Expected output:

```text
D 90
B 80
A 50
C 50
```

The comparison is reversed because larger scores belong earlier. When C is inserted, A has the same score, so 50 < 50 is false and C remains after A. The final insertion of D shifts all three earlier records without changing their relative order.

## Comparing algorithms

For n elements, ordinary bubble sort and selection sort perform (n - 1) + (n - 2) + ... + 1 comparisons, which equals n * (n - 1) / 2. For four elements this is 3 + 2 + 1 = 6. Insertion sort performs a similar number of key comparisons in reverse-sorted input, but on already sorted input it needs only one key comparison per insertion.

| Algorithm | Already sorted input | Reverse sorted input | Stable ordinary version | Movement |
|---|---|---|---|---|
| Bubble with early exit | n - 1 comparisons | n(n-1)/2 comparisons | Yes | Adjacent exchanges |
| Selection | n(n-1)/2 comparisons | n(n-1)/2 comparisons | No | At most n - 1 exchanges |
| Insertion | n - 1 key comparisons | n(n-1)/2 key comparisons | Yes | Shifts and insertions |

All three ordinary implementations use a fixed number of extra variables, independent of array length. Their worst-case comparison counts grow quadratically: doubling a large input approximately quadruples the work. Formal complexity analysis is developed later in the course.

Selection sort can be useful when exchanges are expensive. Insertion sort is particularly useful for small or nearly sorted collections. Bubble sort is valuable for understanding adjacent comparisons, but is rarely the best general-purpose choice.

## Integrated practice

### Exercise 1: preserve the original array

Sort [4,1,4,2] ascending using insertion sort without modifying the original array.

#### Worked solution

Create a separate array using the established copying loop, then sort that copy.

```javascript
const original = [4, 1, 4, 2];
const a = [];
for (let i = 0; i < original.length; i++) a.push(original[i]);
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
console.log(original.join(','));
console.log(a.join(','));
```

Expected output:

```text
4,1,4,2
1,2,4,4
```

Assigning const a = original would not create a new array; both variables would refer to the same collection. Copying each number into a new array avoids that problem. For object elements, this is a shallow copy: rearranging references is independent, but changing object properties would still affect shared objects.

### Exercise 2: select the three smallest values

Given [8,3,7,1,5,2], obtain the three smallest values in ascending order without sorting the entire array.

#### Worked solution

Selection sort establishes one final position per pass. Stop after three passes and copy the resulting prefix.

```javascript
const a = [8, 3, 7, 1, 5, 2];
const count = 3;
const result = [];
for (let start = 0; start < count; start++) {
  let minIndex = start;
  for (let j = start + 1; j < a.length; j++) {
    if (a[j] < a[minIndex]) minIndex = j;
  }
  const temporary = a[start];
  a[start] = a[minIndex];
  a[minIndex] = temporary;
  result.push(a[start]);
}
console.log(result.join(','));
```

Expected output:

```text
1,2,3
```

The first pass selects 1, the second selects 2, and the third selects 3. No remaining value can be smaller than the selected prefix. The suffix need not be sorted. This implementation assumes count is an integer between zero and the array length inclusive; zero produces an empty result. It modifies the input array.

### Exercise 3: diagnose a boundary error

A bubble sort uses j <= end rather than j < end. Explain the problem and correct it.

#### Worked answer

When j equals end, j + 1 equals end + 1. On the first pass this is outside the array, and a[j + 1] is undefined. For finite numbers, comparison with undefined is false, so the extra comparison does not help. On subsequent passes it compares with the already sorted suffix, which is unnecessary. Use j < end to ensure both indexes belong to the unsorted region. The outer loop should use end > 0 because a single remaining position needs no comparison.

### Exercise 4: verify edge cases

Predict the results of sorting [], [7], [2,2], [-1,-5,0], and [3,2,1] ascending. Explain why no special handling is needed.

#### Worked answer

The results are [], [7], [2,2], [-5,-1,0], and [1,2,3]. For empty and singleton arrays the outer conditions are false immediately. Strict comparisons preserve equal values without unnecessary movement in bubble and insertion sort. Negative numbers obey the same numerical ordering rules. Reverse order requires the most shifting in insertion sort, but every inner iteration decreases j, ensuring termination.

## Summary

Sorting requires both ordered output and preservation of original elements. Bubble sort establishes a sorted suffix, selection sort establishes a final sorted prefix by selecting minima, and insertion sort extends a sorted prefix by shifting elements. Derive boundaries from the region being processed, preserve values before overwriting them, and choose comparisons according to the required order and stability.

[Previous: Binary Search](21-part-04-reducing-search-space-and-binary-search.md) | [Next: Integrated Exercises (planned)](21-part-06-integrated-exercises.md)
