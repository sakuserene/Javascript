# Lesson 21 - Advanced Loop Techniques

## Part 1 - Two-Pointer and Multi-Pointer Techniques

[Previous lesson](20-generating-patterns.md) | [Next part (planned)](21-part-02-prefix-suffix-and-sliding-window.md)

## Contents

- [1. Understanding multiple pointers](#1-understanding-multiple-pointers)
- [2. Traversing from opposite ends](#2-traversing-from-opposite-ends)
- [3. Reversing collections](#3-reversing-collections)
- [4. Comparing collections from opposite ends](#4-comparing-collections-from-opposite-ends)
- [5. Searching for target pairs](#5-searching-for-target-pairs)
- [6. Read and write pointers](#6-read-and-write-pointers)
- [7. Merging sorted collections](#7-merging-sorted-collections)
- [8. Finding common elements](#8-finding-common-elements)
- [9. Multi-pointer techniques](#9-multi-pointer-techniques)
- [10. Practice exercises and worked solutions](#10-practice-exercises-and-worked-solutions)
- [11. Summary](#11-summary)

### Learning objectives

Track multiple positions, derive their initialization and movement, reverse collections, verify palindromes, eliminate candidates in sorted searches, compact arrays, remove duplicates, merge and intersect sorted collections, and explain correctness and termination. Examples assume dense arrays of finite numbers and exact arithmetic for the small integers used here.

## 1. Understanding multiple pointers

A familiar traversal uses one index:

```javascript
const values = [10, 20, 30, 40];
for (let i = 0; i < values.length; i++) {
  console.log(values[i]);
}
```

Output: 10, 20, 30, 40 on separate lines. The index identifies the current element. But comparing the first and last elements, then the second and second-last, requires tracking two positions. A pointer in these algorithms is an ordinary numeric index, not a memory address. Pointers can move inward, move forward at different rates, traverse different collections, or represent range boundaries. Their movement follows the problem requirements.

## 2. Traversing from opposite ends

Given [10, 20, 30, 40, 50, 60], generate pairs 10 60, 20 50, 30 40. Initially the indexes are zero and five. After each pair the left index increases and the right index decreases. The last valid index is length minus one: 6 - 1 = 5. Since the positions must be distinct, continue only while left < right.

```javascript
const values = [10, 20, 30, 40, 50, 60];
let left = 0;
let right = values.length - 1;
while (left < right) {
  console.log(values[left], values[right]);
  left++;
  right--;
}
```

```text
10 60
20 50
30 40
```

| Iteration | left | right | Output |
|---|---:|---:|---|
| 1 | 0 | 5 | 10 60 |
| 2 | 1 | 4 | 20 50 |
| 3 | 2 | 3 | 30 40 |
| Stop | 3 | 2 | None |

During iteration two, console.log(values[left], values[right]) retrieves values[1] and values[4], namely 20 and 50. The pointers cross after the third pair.

### Odd-length arrays

With five elements the pointers meet at index two. Handle that unpaired position separately if required.

```javascript
const values = [10, 20, 30, 40, 50];
let left = 0;
let right = values.length - 1;
while (left < right) {
  console.log(values[left], values[right]);
  left++;
  right--;
}
if (left === right) {
  console.log("Middle:", values[left]);
}
```

Output: 10 50, 20 40, Middle: 30. With <= instead of <, the middle element would be paired with itself. That is appropriate only when the requirements allow it. Empty arrays start with right = -1 and neither operation executes.

### Practice 2.1 - Opposite-end sums

Given [3,6,9,12,15,18,21], print each pair's sum and report the middle separately. Expected: 24, 24, 24, Middle: 12.

#### Worked solution

```javascript
const values = [3, 6, 9, 12, 15, 18, 21];
let left = 0;
let right = values.length - 1;
while (left < right) {
  const sum = values[left] + values[right];
  console.log(sum);
  left++;
  right--;
}
if (left === right) console.log("Middle:", values[left]);
```

The traversal is unchanged. The first sum retrieves values[0] and values[6], then adds 3 + 21 = 24. After three iterations both pointers equal three, so the final condition retrieves 12.

## 3. Reversing collections

### Separate result array

```javascript
const values = [10, 20, 30, 40, 50];
const reversed = [];
for (let i = values.length - 1; i >= 0; i--) {
  reversed.push(values[i]);
}
console.log(reversed);
```

Output: [50,40,30,20,10]. Backward traversal preserves the original but requires a separate result array.

### In-place reversal

In place means modifying the original collection. First consider an incorrect exchange:

```javascript
const values = [10, 50];
values[0] = values[1];
values[1] = values[0];
console.log(values);
```

Output: [50,50]. The first assignment destroys 10 before the second can retrieve it. Preserve the original left value in a temporary variable:

```javascript
const values = [10, 50];
const temporary = values[0];
values[0] = values[1];
values[1] = temporary;
console.log(values);
```

Output: [50,10]. Combine this exchange with inward traversal:

```javascript
const values = [10, 20, 30, 40, 50];
let left = 0;
let right = values.length - 1;
while (left < right) {
  const temporary = values[left]; // (1)
  values[left] = values[right];   // (2)
  values[right] = temporary;      // (3)
  left++;
  right--;
}
console.log(values);
```

Output: [50,40,30,20,10]. At (1) preserve 10. At (2) copy 50 into index zero. At (3) copy the preserved 10 into index four.

```text
Initial:       10 20 30 40 50
After copy:    50 20 30 40 50
After restore: 50 20 30 40 10
Next exchange: 50 40 30 20 10
```

The reversed destination of originalIndex is values.length - 1 - originalIndex. For the middle element this is 5 - 1 - 2 = 2. It need not move. Each exchange fixes two positions permanently.

### Reversing a selected range

```javascript
const values = [10, 20, 30, 40, 50, 60];
let left = 1;
let right = 4;
while (left < right) {
  const temporary = values[left];
  values[left] = values[right];
  values[right] = temporary;
  left++;
  right--;
}
console.log(values);
```

Output: [10,50,40,30,20,60]. Only the initial boundaries change. Assume valid inclusive indexes. Outside elements are untouched.

### Practice 3.1 - Reverse indexes two through five

Given [2,4,6,8,10,12,14], expected [2,4,12,10,8,6,14].

```javascript
const values = [2, 4, 6, 8, 10, 12, 14];
let left = 2;
let right = 5;
while (left < right) {
  const temporary = values[left];
  values[left] = values[right];
  values[right] = temporary;
  left++;
  right--;
}
console.log(values);
```

First preserve values[2] = 6, copy values[5] = 12 to index two, then restore 6 at index five. The intermediate array is [2,4,12,8,10,6,14]. The next exchange fixes indexes three and four. The pointers cross; indexes zero, one and six are untouched.

## 4. Comparing collections from opposite ends

A palindrome reads identically forward and backward. For radar, the outer r characters match, then the a characters match. A single mismatch disproves the property.

```javascript
const text = "radar";
let left = 0;
let right = text.length - 1;
let isPalindrome = true;
while (left < right) {
  if (text[left] !== text[right]) {
    isPalindrome = false;
    break;
  }
  left++;
  right--;
}
console.log(isPalindrome);
```

Output: true. The flag means no mismatch has been found so far. Initially text[0] !== text[4] becomes "r" !== "r", false. For robot the first comparison is "r" !== "t", true; set the flag false and break immediately. Empty and single-character strings have no mismatching pair.

### Ignoring case

The string method toLowerCase() returns a lowercase string without modifying the original.

```javascript
const original = "Level";
const lowercase = original.toLowerCase();
console.log(original);
console.log(lowercase);
```

Output: Level, then level. Normalize before comparison:

```javascript
const input = "Level";
const text = input.toLowerCase();
let left = 0;
let right = text.length - 1;
let isPalindrome = true;
while (left < right) {
  if (text[left] !== text[right]) {
    isPalindrome = false;
    break;
  }
  left++;
  right--;
}
console.log(isPalindrome);
```

Output: true. The algorithm remains unchanged; only the representation changes.

### Ignoring spaces

```javascript
const text = "never odd or even";
let left = 0;
let right = text.length - 1;
let isPalindrome = true;
while (left < right) {
  if (text[left] === " ") {
    left++;
    continue;
  }
  if (text[right] === " ") {
    right--;
    continue;
  }
  if (text[left] !== text[right]) {
    isPalindrome = false;
    break;
  }
  left++;
  right--;
}
console.log(isPalindrome);
```

Output: true. When the left character is a space, increment left and continue without comparing or moving right. The right character remains available for a meaningful comparison. The symmetric rule applies to right. Every nonterminating iteration advances at least one pointer. This ignores literal spaces, not every kind of whitespace. These examples compare UTF-16 code units and are intended for ordinary English text, not general Unicode grapheme comparisons.

### Practice 4.1 - Trace abca

Answer: first compare indexes 0 and 3, a and a. They match. Next compare indexes 1 and 2, b and c. They differ. The flag becomes false and the loop breaks on iteration two.

### Practice 4.2 - Ignore case and spaces

Determine whether Never Odd Or Even is a palindrome under both rules. Expected true.

```javascript
const input = "Never Odd Or Even";
const text = input.toLowerCase();
let left = 0;
let right = text.length - 1;
let isPalindrome = true;
while (left < right) {
  if (text[left] === " ") { left++; continue; }
  if (text[right] === " ") { right--; continue; }
  if (text[left] !== text[right]) { isPalindrome = false; break; }
  left++;
  right--;
}
console.log(isPalindrome);
```

Lowercase conversion removes case differences; skipping spaces independently aligns the meaningful characters. Empty input, one character and all-space input return true; abc returns false.

## 5. Searching for target pairs

Given sorted [1,2,4,7,11,15], find two distinct positions whose values sum to 15. Expected 4 11. A nested-loop solution works even for unsorted data:

```javascript
const values = [1, 2, 4, 7, 11, 15];
const target = 15;
let found = false;
for (let i = 0; i < values.length - 1; i++) {
  for (let j = i + 1; j < values.length; j++) {
    const sum = values[i] + values[j];
    if (sum === target) {
      console.log(values[i], values[j]);
      found = true;
      break;
    }
  }
  if (found) break;
}
if (!found) console.log("No matching pair");
```

The inner start i + 1 prevents self-pairing and reversed duplicate pairs. But sorted order lets us eliminate candidates. Start with 1 + 15 = 16. Every other possible partner for 15 is at least 1, so 15 cannot work. Eliminate it. Now 1 + 11 = 12. Every remaining partner for 1 is at most 11, so 1 cannot work. Eliminate it. Continue until a match or no distinct positions remain.

```javascript
const values = [1, 2, 4, 7, 11, 15];
const target = 15;
let left = 0;
let right = values.length - 1;
let found = false;
while (left < right) {
  const sum = values[left] + values[right];
  if (sum === target) {
    console.log(values[left], values[right]);
    found = true;
    break;
  }
  if (sum < target) left++;
  else right--;
}
if (!found) console.log("No matching pair");
```

| left | right | Values | Sum | Decision |
|---:|---:|---|---:|---|
| 0 | 5 | 1,15 | 16 | right-- |
| 0 | 4 | 1,11 | 12 | left++ |
| 1 | 4 | 2,11 | 13 | left++ |
| 2 | 4 | 4,11 | 15 | Found |

The sum expression first retrieves two elements, then adds them. Initially values[0] + values[5] = 1 + 15 = 16. The equality fails and 16 < 15 is false, so right decreases. Only the justified candidate is eliminated. Moving both pointers would fail for [1,3,4,6,8,10] with target 13: the first sum is 11, but discarding 10 loses the valid pair 3 and 10. Unsorted [1,8,2,7,4] with target 15 also fails: sums 5,12,6,11 never reveal 8 and 7. Sorted order is essential. Equal values at distinct positions are allowed: [2,4,4,7] has a pair 4 and 4 for target 8. Using <= for the loop could incorrectly reuse a single element.

### Practice 5.1 - Trace target 19

```javascript
const values = [2, 5, 7, 9, 12, 16];
const target = 19;
let left = 0;
let right = values.length - 1;
let found = false;
while (left < right) {
  const sum = values[left] + values[right];
  if (sum === target) {
    console.log(values[left], values[right]);
    found = true;
    break;
  }
  if (sum < target) left++;
  else right--;
}
if (!found) console.log("No matching pair");
```

Output: 7 12. Trace: (0,5) sum 18, increment left; (1,5) sum 21, decrement right; (1,4) sum 17, increment left; (2,4) sum 19, found. At the second comparison 5 + 16 exceeds 19, so 16 cannot match any remaining left candidate.

### Practice 5.2 - Boundary behavior

| Input | Target | Answer and reason |
|---|---:|---|
| [] | 10 | No pair; right = -1 |
| [5] | 10 | No pair; pointers equal |
| [5,5] | 10 | 5 and 5 at distinct indexes |
| [1,2,3] | 10 | No pair; largest pair too small |
| [-5,-2,3,8] | 1 | -2 and 3; sums 3,-2,1 |

Negative numbers do not invalidate sorted candidate elimination.

## 6. Read and write pointers

Remove zeros from [0,4,0,7,2,0,9], preserving order. A separate result is straightforward:

```javascript
const values = [0, 4, 0, 7, 2, 0, 9];
const result = [];
for (let i = 0; i < values.length; i++) {
  if (values[i] !== 0) result.push(values[i]);
}
console.log(result);
```

Output: [4,7,2,9]. To modify the original, compact retained elements toward the beginning. Read identifies the next original element; write identifies the next retained position. Read advances every iteration, write only on retention.

```javascript
const values = [0, 4, 0, 7, 2, 0, 9];
let write = 0;
for (let read = 0; read < values.length; read++) {
  if (values[read] !== 0) {
    values[write] = values[read]; // (1)
    write++; // (2)
  }
}
values.length = write; // (3)
console.log(values);
```

| Read | Write afterward | Array afterward |
|---:|---:|---|
| 0 | 0 | 0,4,0,7,2,0,9 |
| 1 | 1 | 4,4,0,7,2,0,9 |
| 2 | 1 | 4,4,0,7,2,0,9 |
| 3 | 2 | 4,7,0,7,2,0,9 |
| 4 | 3 | 4,7,2,7,2,0,9 |
| 5 | 3 | 4,7,2,7,2,0,9 |
| 6 | 4 | 4,7,2,9,2,0,9 |

At read=3, write=1, line (1) becomes values[1] = values[3], copying 7. Line (2) advances the destination and counts another retained element. Before truncation the first four elements are correct but stale suffix values remain. Line (3) sets length to four, removing that suffix. Output: [4,7,2,9]. Before processing each element, write <= read because each examined element produces at most one retained element. Thus no unread element is overwritten. Do not truncate inside the loop: that would destroy unprocessed elements.

### Sorted duplicate removal

```javascript
const values = [1, 1, 2, 2, 2, 4, 5, 5];
let write = 0;
for (let read = 0; read < values.length; read++) {
  if (write === 0 || values[read] !== values[write - 1]) {
    values[write] = values[read];
    write++;
  }
}
values.length = write;
console.log(values);
```

Output: [1,2,4,5]. Equal sorted values occur consecutively. write - 1 identifies the last retained position; when write=2 it is index one. If write=0, logical OR short-circuits, avoiding access to a nonexistent previous retained value. Otherwise retain only a different value. Unsorted input requires another strategy, such as a Set.

### Practice 6.1 - Retain even integers

Given [3,8,5,12,0,-4,7], expected [8,12,0,-4].

```javascript
const values = [3, 8, 5, 12, 0, -4, 7];
let write = 0;
for (let read = 0; read < values.length; read++) {
  if (values[read] % 2 === 0) {
    values[write] = values[read];
    write++;
  }
}
values.length = write;
console.log(values);
```

The condition retrieves the current integer, computes the remainder after division by two, and compares it to zero. Negative four produces negative zero, strictly equal to zero. Zero is retained too. Four retained elements mean write=4 at truncation.

### Practice 6.2 - Retain at most two copies

Given sorted [1,1,1,2,2,3,3,3], expected [1,1,2,2,3,3]. Compare with the value two retained positions earlier.

```javascript
const values = [1, 1, 1, 2, 2, 3, 3, 3];
let write = 0;
for (let read = 0; read < values.length; read++) {
  if (write < 2 || values[read] !== values[write - 2]) {
    values[write] = values[read];
    write++;
  }
}
values.length = write;
console.log(values);
```

At the third one, read=2 and write=2. The condition becomes false || 1 !== 1, false. Skip it. The first two differs from the retained one and is accepted. For a positive integer limit k, replace both occurrences of 2 with k. Sorted order makes the backward comparison sufficient.

## 7. Merging sorted collections

Given first=[1,4,7] and second=[2,3,8], appending one array to the other gives [1,4,7,2,3,8], which is not sorted. Instead compare the smallest unprocessed element of each array, select the smaller, and advance only its pointer. The other candidate remains available. Start i=j=0 and merged=[]. Both positions must be valid for a comparison, so use i < first.length && j < second.length.

### Incomplete implementation

```javascript
const first = [1, 4, 7];
const second = [2, 3, 8];
const merged = [];
let i = 0;
let j = 0;
while (i < first.length && j < second.length) {
  if (first[i] <= second[j]) { merged.push(first[i]); i++; }
  else { merged.push(second[j]); j++; }
}
console.log(merged);
```

Output: [1,2,3,4,7]. After selecting 7, i=3 and j=2. The condition becomes false && true, so the loop correctly stops comparing, but 8 remains. Append leftovers separately.

### Complete implementation

```javascript
const first = [1, 4, 7];
const second = [2, 3, 8];
const merged = [];
let i = 0;
let j = 0;
while (i < first.length && j < second.length) {
  if (first[i] <= second[j]) {
    merged.push(first[i]);
    i++;
  } else {
    merged.push(second[j]);
    j++;
  }
}
while (i < first.length) { merged.push(first[i]); i++; }
while (j < second.length) { merged.push(second[j]); j++; }
console.log(merged);
```

| i | j | Candidates | Selected | Result afterward |
|---:|---:|---|---:|---|
| 0 | 0 | 1,2 | 1 | 1 |
| 1 | 0 | 4,2 | 2 | 1,2 |
| 1 | 1 | 4,3 | 3 | 1,2,3 |
| 1 | 2 | 4,8 | 4 | 1,2,3,4 |
| 2 | 2 | 7,8 | 7 | 1,2,3,4,7 |
| 3 | 2 | exhausted,8 | 8 | 1,2,3,4,7,8 |

At the final loop j=2, so merged.push(second[j]) becomes merged.push(second[2]), then merged.push(8). Increment j to 3 and stop. Every selected element is the smallest remaining value, so the result stays sorted. Each pointer uses its own array length. For [2,9] and [1,3,4,7,10], the result is [1,2,3,4,7,9,10]. For [1,3,3,7] and [2,3,5], the result is [1,2,3,3,3,5,7]. Equal values are retained; <= selects the first array on ties, whereas < selects the second.

### Merging records

```javascript
const first = [{name:"Alice",score:70},{name:"Brian",score:80}];
const second = [{name:"Carol",score:70},{name:"David",score:90}];
const merged = [];
let i = 0;
let j = 0;
while (i < first.length && j < second.length) {
  if (first[i].score <= second[j].score) {
    merged.push(first[i]); i++;
  } else { merged.push(second[j]); j++; }
}
while (i < first.length) { merged.push(first[i]); i++; }
while (j < second.length) { merged.push(second[j]); j++; }
for (let index = 0; index < merged.length; index++) {
  console.log(merged[index].name, merged[index].score);
}
```

Output: Alice 70, Carol 70, Brian 80, David 90 on separate lines. first[i].score first retrieves the record at i, then its score property. At i=0 this is Alice's score 70. Compare scores but append the entire record, not just its score, to preserve the name. Equality favors the first array; order within each input is preserved. Objects are shared references, not deep copies.

### Unique merge (sorted union)

A union contains every distinct value from either collection. Sorted merging makes equal values consecutive, so compare each selected value with the last appended value. A single loop can handle selection and duplicate prevention together.

```javascript
const first = [1, 1, 3, 5];
const second = [1, 2, 3, 6];
const merged = [];
let i = 0;
let j = 0;
while (i < first.length || j < second.length) {
  let value;
  if (i < first.length && (j >= second.length || first[i] <= second[j])) {
    value = first[i]; i++;
  } else { value = second[j]; j++; }
  if (merged.length === 0 || value !== merged[merged.length - 1]) {
    merged.push(value);
  }
}
console.log(merged);
```

Output: [1,2,3,5,6]. OR continues while either input has elements. The selection condition first checks availability in first; AND short-circuits if it is exhausted. If second is exhausted, OR short-circuits before comparing its nonexistent element. For i=3,j=4 with both lengths four, the condition is true && (true || ...), true. The remaining first value is selected. If merged=[1,2,3], merged.length-1 = 2 and merged[2]=3. A selected 3 is rejected, a selected 5 is retained. Pointer movement happens even when a duplicate is rejected.

Three loops separate comparison and leftover handling clearly. A single loop centralizes processing when every selected value needs filtering or transformation. Ordinary merge preserves occurrences; unique merge removes duplicates; record merge compares properties. Both inputs must use the same sorted order.

| First | Second | Ordinary result |
|---|---|---|
| [] | [] | [] |
| [] | [1,2] | [1,2] |
| [3,4] | [] | [3,4] |
| [1] | [2] | [1,2] |
| [2] | [2] | [2,2] |
| [-5,0] | [-3,4] | [-5,-3,0,4] |

## 8. Finding common elements

Intersection contains values common to both collections. For sorted [1,3,5,7,9] and [2,3,6,7,10], compare 1 and 2. Since all remaining second values are at least 2, discard 1. Compare 3 and 2; discard 2. Compare 3 and 3; record the match and advance both. Stop when either collection is exhausted, since no further match is possible.

```javascript
const first = [1, 3, 5, 7, 9];
const second = [2, 3, 6, 7, 10];
const common = [];
let i = 0;
let j = 0;
while (i < first.length && j < second.length) {
  if (first[i] === second[j]) {
    common.push(first[i]); i++; j++;
  } else if (first[i] < second[j]) { i++; }
  else { j++; }
}
console.log(common);
```

Output: [3,7].

| i | j | Values | Decision |
|---:|---:|---|---|
| 0 | 0 | 1,2 | i++ |
| 1 | 0 | 3,2 | j++ |
| 1 | 1 | 3,3 | Append 3; both++ |
| 2 | 2 | 5,6 | i++ |
| 3 | 2 | 7,6 | j++ |
| 3 | 3 | 7,7 | Append 7; both++ |
| 4 | 4 | 9,10 | i++ |
| 5 | 4 | exhausted,10 | Stop |

With [1,2,2,2,5] and [2,2,4,5], the ordinary result is [2,2,5], pairing occurrences. To retain distinct matches, compare against the last retained value.

```javascript
const first = [1, 2, 2, 2, 5];
const second = [2, 2, 4, 5];
const common = [];
let i = 0;
let j = 0;
while (i < first.length && j < second.length) {
  if (first[i] === second[j]) {
    const value = first[i];
    if (common.length === 0 || value !== common[common.length - 1]) {
      common.push(value);
    }
    i++; j++;
  } else if (first[i] < second[j]) { i++; }
  else { j++; }
}
console.log(common);
```

Output: [2,5]. Both pointers advance even when the duplicate is rejected. Placing advancement inside the retention condition could cause an infinite loop. Processing and retaining are different operations.

## 9. Multi-pointer techniques

For three sorted arrays [1,7], [2,5], [3,4,9], track three independent indexes. Select the smallest available head. The first candidate must exist and be no greater than either other available candidate. An exhausted collection does not participate in comparison.

```javascript
const first = [1, 7];
const second = [2, 5];
const third = [3, 4, 9];
const merged = [];
let i = 0;
let j = 0;
let k = 0;
while (i < first.length || j < second.length || k < third.length) {
  if (i < first.length &&
      (j >= second.length || first[i] <= second[j]) &&
      (k >= third.length || first[i] <= third[k])) {
    merged.push(first[i]); i++;
  } else if (j < second.length &&
             (k >= third.length || second[j] <= third[k])) {
    merged.push(second[j]); j++;
  } else { merged.push(third[k]); k++; }
}
console.log(merged);
```

Output: [1,2,3,4,5,7,9]. The second branch need not compare with first again: the first branch already failed, so if second is available and no greater than third, second is an appropriate minimum. This depends on branch order and ordinary comparable finite numbers.

| i | j | k | Candidates | Selected |
|---:|---:|---:|---|---:|
| 0 | 0 | 0 | 1,2,3 | 1 |
| 1 | 0 | 0 | 7,2,3 | 2 |
| 1 | 1 | 0 | 7,5,3 | 3 |
| 1 | 1 | 1 | 7,5,4 | 4 |
| 1 | 1 | 2 | 7,5,9 | 5 |
| 1 | 2 | 2 | 7,exhausted,9 | 7 |
| 2 | 2 | 2 | exhausted,exhausted,9 | 9 |

### Arbitrary number of collections

Explicit conditions become cumbersome for many arrays. Store arrays inside collections and their pointers inside positions. collections[1][0] first retrieves [2,5], then 2. With group=1 and positions=[1,0,2], collections[group][positions[group]] becomes collections[1][0]. Equivalently retrieve collection, position, then value in three statements. Scan all available heads, tracking the index of the smallest candidate in selected. Initialize selected=-1 because no valid collection index is negative.

```javascript
const collections = [[1,7],[2,5],[3,4,9]];
const positions = [];
const merged = [];
for (let group = 0; group < collections.length; group++) positions.push(0);
while (true) {
  let selected = -1;
  for (let group = 0; group < collections.length; group++) {
    const position = positions[group];
    if (position >= collections[group].length) continue;
    const value = collections[group][position];
    if (selected === -1 || value < collections[selected][positions[selected]]) {
      selected = group;
    }
  }
  if (selected === -1) break;
  const position = positions[selected];
  merged.push(collections[selected][position]);
  positions[selected]++;
}
console.log(merged);
```

Output: [1,2,3,4,5,7,9]. The availability check skips exhausted collections. If selected=-1, OR short-circuits and the first available candidate is accepted. Otherwise, with selected=0, positions=[1,0,0], value=2, the comparison becomes 2 < collections[0][1], or 2 < 7, true. Set selected to the current group. After scanning, append its current value and advance only its pointer. positions[selected]++ with selected=1 has the same final effect as positions[1] = positions[1] + 1. Reset selected on every outer iteration. If all collections are exhausted, selected remains -1 and break terminates the loop. The preceding trace also applies to this implementation, with i,j,k represented by positions[0], positions[1], positions[2].

Explicit pointers are direct for a small fixed number of arrays. Repeated two-array merges reuse a familiar algorithm but create intermediate arrays. An array of pointers handles arbitrary collection counts but scans every collection head for each output element. More advanced candidate-selection structures are outside this lesson.

## 10. Practice exercises and worked solutions

### Exercise 10.1 - Unequal lengths

Merge [2,6,10] and [1,3,5,7,9,11] without built-in sorting. Expected [1,2,3,5,6,7,9,10,11].

```javascript
const first = [2,6,10], second = [1,3,5,7,9,11], merged = [];
let i = 0, j = 0;
while (i < first.length && j < second.length) {
  if (first[i] <= second[j]) { merged.push(first[i]); i++; }
  else { merged.push(second[j]); j++; }
}
while (i < first.length) { merged.push(first[i]); i++; }
while (j < second.length) { merged.push(second[j]); j++; }
console.log(merged);
```

Initially 2 <= 1 is false, so append 1 and advance j. Next 2 <= 3 is true, so append 2 and advance i. After selecting 10, i=3 and j=5. The remaining-element loop appends second[5]=11. The result has nine elements, the sum of the input lengths.

### Exercise 10.2 - Debug OR without availability checks

Suppose a merge uses while (i < first.length || j < second.length) but directly compares first[i] <= second[j] and advances only the selected pointer. Explain failure for [1,3] and [2].

Answer: after selecting 1 and 2, i=1,j=1. The condition is true || false. The comparison becomes 3 <= undefined, false, because the second array is exhausted. The else branch appends undefined and increments j. i never changes, so the loop can run indefinitely. Do not execute the faulty loop unbounded. Correct it by checking availability before comparison:

```javascript
const first = [1,3], second = [2], merged = [];
let i = 0, j = 0;
while (i < first.length || j < second.length) {
  if (i < first.length && (j >= second.length || first[i] <= second[j])) {
    merged.push(first[i]); i++;
  } else { merged.push(second[j]); j++; }
}
console.log(merged);
```

Output: [1,2,3]. When j=1, j >= second.length is true and OR skips the invalid comparison. The remaining first element is selected.

### Exercise 10.3 - Descending merge

Merge descending [9,6,2] and [10,7,3,1]. Expected [10,9,7,6,3,2,1]. Select the larger available head rather than the smaller.

```javascript
const first = [9,6,2], second = [10,7,3,1], merged = [];
let i = 0, j = 0;
while (i < first.length && j < second.length) {
  if (first[i] >= second[j]) { merged.push(first[i]); i++; }
  else { merged.push(second[j]); j++; }
}
while (i < first.length) { merged.push(first[i]); i++; }
while (j < second.length) { merged.push(second[j]); j++; }
console.log(merged);
```

9 >= 10 is false, append 10. Then 9 >= 7 is true, append 9. Every selected value is no greater than its predecessor. The boundaries and leftover loops are unchanged.

### Exercise 10.4 - Distinct common values

Given [1,2,2,4,6,6,9] and [2,2,3,6,8,9,9], expected [2,6,9]. Combine intersection with duplicate prevention.

```javascript
const first = [1,2,2,4,6,6,9], second = [2,2,3,6,8,9,9];
const common = [];
let i = 0, j = 0;
while (i < first.length && j < second.length) {
  const a = first[i], b = second[j];
  if (a === b) {
    if (common.length === 0 || a !== common[common.length - 1]) common.push(a);
    i++; j++;
  } else if (a < b) { i++; }
  else { j++; }
}
console.log(common);
```

a and b store the current values, avoiding repeated indexed access. The first matching 2 is appended because common is empty. The second match evaluates 2 !== 2, false, but both pointers still advance. The later matches 6 and 9 differ from the last retained value.

### Exercise 10.5 - Values belonging only to the first array

Given distinct sorted inputs [1,3,5,7,9] and [2,3,6,7], expected [1,5,9]. If the first value is smaller, retain it because it cannot match remaining second values. If larger, advance second. If equal, exclude and advance both. When second ends, retain remaining first values.

```javascript
const first = [1,3,5,7,9], second = [2,3,6,7], result = [];
let i = 0, j = 0;
while (i < first.length && j < second.length) {
  if (first[i] < second[j]) { result.push(first[i]); i++; }
  else if (first[i] > second[j]) { j++; }
  else { i++; j++; }
}
while (i < first.length) { result.push(first[i]); i++; }
console.log(result);
```

1 < 2 retains 1. The next first value 3 exceeds 2, so advance second. Equal 3 values are excluded. Similarly retain 5 and exclude 7. After second is exhausted, append 9. No leftover loop for second is needed because only first-array values belong in the result. The distinct-input assumption matters: repeated first values would need additional handling.

### Exercise 10.6 - Multiple collections including an empty array

Merge [[1,8],[],[2,4,9],[3,5,6]]. Expected [1,2,3,4,5,6,8,9]. Use a pointer array and expand the comparison into intermediate variables.

```javascript
const collections = [[1,8],[],[2,4,9],[3,5,6]];
const positions = [], merged = [];
for (let group = 0; group < collections.length; group++) positions.push(0);
while (true) {
  let selected = -1;
  for (let group = 0; group < collections.length; group++) {
    const position = positions[group];
    const collection = collections[group];
    if (position >= collection.length) continue;
    const value = collection[position];
    if (selected === -1) { selected = group; }
    else {
      const selectedPosition = positions[selected];
      const selectedValue = collections[selected][selectedPosition];
      if (value < selectedValue) selected = group;
    }
  }
  if (selected === -1) break;
  const collection = collections[selected];
  const position = positions[selected];
  merged.push(collection[position]);
  positions[selected]++;
}
console.log(merged);
```

At group=1, collection=[] and position=0. The availability test 0 >= 0 is true, so continue skips it. The selectedPosition variable retrieves the pointer of the current best collection, and selectedValue retrieves its head. Comparing value < selectedValue decides whether to replace the candidate. Expanded statements make intermediate values easier to inspect while debugging. The first available head becomes the candidate without an invalid access. Every successful outer iteration consumes one element; the final scan finds none and terminates.

## 11. Summary

| Problem | Pointer strategy |
|---|---|
| Opposite-end pairs | Both inward |
| Reversal | Exchange and move inward |
| Palindrome | Compare corresponding positions |
| Sorted target pair | Eliminate one justified candidate |
| Compaction | Independent read and write positions |
| Sorted duplicate removal | Compare retained values |
| Merge | Select smallest available head |
| Intersection | Advance smaller head, both on equality |
| Difference | Retain values that cannot match |
| Multiple collections | One pointer per collection |

Establish what each pointer represents, where it starts, which positions remain unprocessed, what justifies movement, whether one or several pointers advance, how exhaustion is handled, why termination is guaranteed, and which input properties are required. Understanding these relationships is more important than memorizing implementations.

[Previous lesson](20-generating-patterns.md) | [Next part (planned)](21-part-02-prefix-suffix-and-sliding-window.md)
