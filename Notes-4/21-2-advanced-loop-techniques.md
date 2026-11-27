# Lesson 21 - Advanced Loop Techniques

[Previous lesson](20-generating-patterns.md) | Next lesson: Converting and Refactoring Loops (planned)

## Table of contents

- [Part 1: Two Pointer And Multi Pointer Techniques](#part-1)
- [Part 2: Prefix Suffix Aggregates And Sliding Window](#part-2)
- [Part 3: Consecutive Values Circular Iteration And Chunks](#part-3)
- [Part 4: Reducing Search Space And Binary Search](#part-4)
- [Part 5: Sorting With Loops](#part-5)
- [Part 6: Integrated Exercises](#part-6)


<a id="part-1"></a>

<a id="p1-lesson-21---advanced-loop-techniques"></a>
# Lesson 21 - Advanced Loop Techniques

<a id="p1-part-1---two-pointer-and-multi-pointer-techniques"></a>
## Part 1 - Two-Pointer and Multi-Pointer Techniques

[Previous lesson](20-generating-patterns.md) | [Next part (planned)](#part-2)

<a id="p1-contents"></a>
## Contents

- [1. Understanding multiple pointers](#p1-1-understanding-multiple-pointers)
- [2. Traversing from opposite ends](#p1-2-traversing-from-opposite-ends)
- [3. Reversing collections](#p1-3-reversing-collections)
- [4. Comparing collections from opposite ends](#p1-4-comparing-collections-from-opposite-ends)
- [5. Searching for target pairs](#p1-5-searching-for-target-pairs)
- [6. Read and write pointers](#p1-6-read-and-write-pointers)
- [7. Merging sorted collections](#p1-7-merging-sorted-collections)
- [8. Finding common elements](#p1-8-finding-common-elements)
- [9. Multi-pointer techniques](#p1-9-multi-pointer-techniques)
- [10. Practice exercises and worked solutions](#p1-10-practice-exercises-and-worked-solutions)
- [11. Summary](#p1-11-summary)

<a id="p1-learning-objectives"></a>
### Learning objectives

Track multiple positions, derive their initialization and movement, reverse collections, verify palindromes, eliminate candidates in sorted searches, compact arrays, remove duplicates, merge and intersect sorted collections, and explain correctness and termination. Examples assume dense arrays of finite numbers and exact arithmetic for the small integers used here.

<a id="p1-1-understanding-multiple-pointers"></a>
## 1. Understanding multiple pointers

A familiar traversal uses one index:

```javascript
const values = [10, 20, 30, 40];
for (let i = 0; i < values.length; i++) {
  console.log(values[i]);
}
```

Output: 10, 20, 30, 40 on separate lines. The index identifies the current element. But comparing the first and last elements, then the second and second-last, requires tracking two positions. A pointer in these algorithms is an ordinary numeric index, not a memory address. Pointers can move inward, move forward at different rates, traverse different collections, or represent range boundaries. Their movement follows the problem requirements.

<a id="p1-2-traversing-from-opposite-ends"></a>
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

<a id="p1-odd-length-arrays"></a>
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

<a id="p1-practice-21---opposite-end-sums"></a>
### Practice 2.1 - Opposite-end sums

Given [3,6,9,12,15,18,21], print each pair's sum and report the middle separately. Expected: 24, 24, 24, Middle: 12.

<a id="p1-worked-solution"></a>
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

<a id="p1-3-reversing-collections"></a>
## 3. Reversing collections

<a id="p1-separate-result-array"></a>
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

<a id="p1-in-place-reversal"></a>
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

<a id="p1-reversing-a-selected-range"></a>
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

<a id="p1-practice-31---reverse-indexes-two-through-five"></a>
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

<a id="p1-4-comparing-collections-from-opposite-ends"></a>
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

<a id="p1-ignoring-case"></a>
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

<a id="p1-ignoring-spaces"></a>
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

<a id="p1-practice-41---trace-abca"></a>
### Practice 4.1 - Trace abca

Answer: first compare indexes 0 and 3, a and a. They match. Next compare indexes 1 and 2, b and c. They differ. The flag becomes false and the loop breaks on iteration two.

<a id="p1-practice-42---ignore-case-and-spaces"></a>
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

<a id="p1-5-searching-for-target-pairs"></a>
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

<a id="p1-practice-51---trace-target-19"></a>
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

<a id="p1-practice-52---boundary-behavior"></a>
### Practice 5.2 - Boundary behavior

| Input | Target | Answer and reason |
|---|---:|---|
| [] | 10 | No pair; right = -1 |
| [5] | 10 | No pair; pointers equal |
| [5,5] | 10 | 5 and 5 at distinct indexes |
| [1,2,3] | 10 | No pair; largest pair too small |
| [-5,-2,3,8] | 1 | -2 and 3; sums 3,-2,1 |

Negative numbers do not invalidate sorted candidate elimination.

<a id="p1-6-read-and-write-pointers"></a>
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

<a id="p1-sorted-duplicate-removal"></a>
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

<a id="p1-practice-61---retain-even-integers"></a>
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

<a id="p1-practice-62---retain-at-most-two-copies"></a>
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

<a id="p1-7-merging-sorted-collections"></a>
## 7. Merging sorted collections

Given first=[1,4,7] and second=[2,3,8], appending one array to the other gives [1,4,7,2,3,8], which is not sorted. Instead compare the smallest unprocessed element of each array, select the smaller, and advance only its pointer. The other candidate remains available. Start i=j=0 and merged=[]. Both positions must be valid for a comparison, so use i < first.length && j < second.length.

<a id="p1-incomplete-implementation"></a>
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

<a id="p1-complete-implementation"></a>
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

<a id="p1-merging-records"></a>
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

<a id="p1-unique-merge-sorted-union"></a>
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

<a id="p1-8-finding-common-elements"></a>
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

<a id="p1-9-multi-pointer-techniques"></a>
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

<a id="p1-arbitrary-number-of-collections"></a>
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

<a id="p1-10-practice-exercises-and-worked-solutions"></a>
## 10. Practice exercises and worked solutions

<a id="p1-exercise-101---unequal-lengths"></a>
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

<a id="p1-exercise-102---debug-or-without-availability-checks"></a>
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

<a id="p1-exercise-103---descending-merge"></a>
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

<a id="p1-exercise-104---distinct-common-values"></a>
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

<a id="p1-exercise-105---values-belonging-only-to-the-first-array"></a>
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

<a id="p1-exercise-106---multiple-collections-including-an-empty-array"></a>
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

<a id="p1-11-summary"></a>
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

[Previous lesson](20-generating-patterns.md) | [Next part (planned)](#part-2)


<a id="part-2"></a>

<a id="p2-lesson-21---advanced-loop-techniques"></a>
# Lesson 21 - Advanced Loop Techniques

<a id="p2-part-2---prefix-and-suffix-aggregates-and-sliding-window"></a>
## Part 2 - Prefix and Suffix Aggregates and Sliding Window

[Previous: Part 1](#part-1) | [Contents](#p2-table-of-contents) | Next: Part 3 (planned)

<a id="p2-introduction"></a>
## Introduction

In Part 1, we learned to maintain several positions rather than treating every iteration as an independent operation. Here we extend that idea to ranges. When two ranges overlap, much of the information calculated for one range may be useful for the next. Instead of repeatedly calculating everything, we can preserve cumulative information or update an aggregate as its boundaries move.

By the end of this part, you should be able to derive prefix and suffix aggregates, answer range queries, calculate fixed and variable window results, justify boundary updates, recognize when a window strategy is invalid, and verify solutions against edge cases.

We use dense arrays of numbers and traditional loops. Unless otherwise stated, examples use small integers so that arithmetic is exact. A contiguous range contains consecutive indexes, with no skipped elements. A range query asks for information about a specified range without necessarily modifying it.

<a id="p2-table-of-contents"></a>
## Table of contents

- [1. From repeated calculations to prefix sums](#p2-1-from-repeated-calculations-to-prefix-sums)
- [2. Range queries using prefix sums](#p2-2-range-queries-using-prefix-sums)
- [3. Other prefix aggregates](#p2-3-other-prefix-aggregates)
- [4. Suffix aggregates](#p2-4-suffix-aggregates)
- [5. Combining prefix and suffix information](#p2-5-combining-prefix-and-suffix-information)
- [6. Fixed-size sliding windows](#p2-6-fixed-size-sliding-windows)
- [7. Fixed-window variations](#p2-7-fixed-window-variations)
- [8. Variable-size sliding windows](#p2-8-variable-size-sliding-windows)
- [9. Window validity and limitations](#p2-9-window-validity-and-limitations)
- [10. Integrated exercises](#p2-10-integrated-exercises)
- [11. Summary](#p2-11-summary)

<a id="p2-1-from-repeated-calculations-to-prefix-sums"></a>
## 1. From repeated calculations to prefix sums

Suppose a shop records daily sales as `[4, 7, 2, 9, 3]`. A manager asks for the total from day one through day three (indexes 0 through 2), then the total from indexes 1 through 3. We already know how to accumulate a range.

```javascript
const sales = [4, 7, 2, 9, 3];
let total = 0;
for (let i = 0; i <= 2; i++) {
  total += sales[i];
}
console.log(total);
```

Expected output:

```text
13
```

The next query requires `7 + 2 + 9`. Notice that both queries include 7 and 2. Repeating the loop for every query is correct, but it repeatedly processes overlapping information.

A prefix is the beginning portion of a sequence. We can store the sum of every prefix once, then use those stored totals later. The prefix containing no elements has sum zero. The prefix containing one element has sum 4. The prefix containing two elements has sum 11, and so on.

```text
Original index:       0   1   2   3   4
Original value:       4   7   2   9   3
Prefix boundary:  0   1   2   3   4   5
Prefix sum:       0   4  11  13  22  25
```

The prefix array contains one more element than the original. Its index represents a boundary, or equivalently the number of original elements included. Thus `prefix[3]` includes indexes 0, 1 and 2, not index 3.

<a id="p2-building-the-prefix-array"></a>
### Building the prefix array

Each new total is the previous total plus the next original value. We do not need a nested loop.

```javascript
const sales = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < sales.length; i++) {
  prefix.push(prefix[i] + sales[i]);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 4 11 13 22 25
```

Here `join(' ')` returns a string containing the array elements separated by spaces. It does not change the array. For example, `[4, 7].join(' ')` returns `'4 7'`. We use it only to make expected console output consistent.

The central statement combines three operations. `prefix[i]` retrieves the total of the first `i` elements. `sales[i]` retrieves the next element. Addition produces the new total, and `push` appends it. At `i = 2`, the expression becomes `prefix[2] + sales[2]`, then `11 + 2`, then `13`. Appending creates `prefix[3]`.

| i | Previous total | Next value | Appended total |
|---:|---:|---:|---:|
| 0 | 0 | 4 | 4 |
| 1 | 4 | 7 | 11 |
| 2 | 11 | 2 | 13 |
| 3 | 13 | 9 | 22 |
| 4 | 22 | 3 | 25 |

An equivalent implementation maintains a separate running total. This can be easier to read when learning.

```javascript
const values = [4, -7, 2, 0];
const prefix = [0];
let total = 0;
for (let i = 0; i < values.length; i++) {
  total += values[i];
  prefix.push(total);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 4 -3 -1 -1
```

Negative numbers and zeros are valid. Prefix sums need not increase. For an empty input the result is `[0]`, representing the empty prefix.

<a id="p2-practice-1---construct-and-trace"></a>
### Practice 1 - Construct and trace

Construct prefix sums for `[3, -2, 5, -6]`. Explain the third appended value.

<a id="p2-worked-solution"></a>
#### Worked solution

```javascript
const values = [3, -2, 5, -6];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
console.log(prefix.join(' '));
```

Expected output:

```text
0 3 1 6 0
```

At index 2 the previous total is 1, because 3 plus negative 2 equals 1. Adding 5 gives 6. This becomes boundary 3. The final total returns to zero, but that does not mean the array was empty.

<a id="p2-2-range-queries-using-prefix-sums"></a>
## 2. Range queries using prefix sums

Suppose we need the total from index 1 through index 3 inclusive. The prefix ending immediately after index 3 includes `4 + 7 + 2 + 9`. The prefix ending immediately before index 1 includes only `4`. Subtracting the second from the first removes the unwanted beginning.

```text
prefix[4] = 4 + 7 + 2 + 9 = 22
prefix[1] = 4             =  4
                            ----
Desired sum                 18
```

For an inclusive range `[start, end]`, the formula is `prefix[end + 1] - prefix[start]`. The addition of one converts the last included index into the boundary immediately after it.

```javascript
const values = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
const start = 1;
const end = 3;
console.log(prefix[end + 1] - prefix[start]);
```

Expected output:

```text
18
```

<a id="p2-half-open-ranges"></a>
### Half-open ranges

A half-open range includes its start but excludes its end. For `[start, stop)`, the formula becomes `prefix[stop] - prefix[start]`. For example, `[1, 4)` includes indexes 1, 2 and 3. This convention matches the familiar loop condition `i < stop`. An empty range has equal boundaries and sum zero.

<a id="p2-multiple-queries-and-validation"></a>
### Multiple queries and validation

The prefix array can be reused for many queries. Store each query as a pair of boundaries. We use `Number.isInteger(value)` to check whether a value is an integer: it returns true for 3 and false for 3.5 or the string `'3'`. A valid half-open range has integer boundaries satisfying `0 <= start <= stop <= length`. JavaScript does not interpret chained comparisons mathematically, so we express each comparison separately.

```javascript
const values = [4, 7, 2, 9, 3];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
const queries = [[1, 4], [0, 5], [2, 2], [4, 5], [-1, 2], [2, 6]];
for (let q = 0; q < queries.length; q++) {
  const start = queries[q][0];
  const stop = queries[q][1];
  if (!Number.isInteger(start) || !Number.isInteger(stop) ||
      start < 0 || stop < start || stop > values.length) {
    console.log('Invalid range');
    continue;
  }
  console.log(prefix[stop] - prefix[start]);
}
```

Expected output:

```text
18
25
0
3
Invalid range
Invalid range
```

`queries[q][0]` first retrieves the query array and then its first boundary. The validation prevents accessing missing prefix entries. Without it, an out-of-range access can produce `undefined`, and subtraction can produce `NaN`.

<a id="p2-mistakes-and-trade-offs"></a>
### Mistakes and trade-offs

Using `prefix[end] - prefix[start]` for inclusive boundaries excludes the final element. For indexes 1 through 3, it gives `13 - 4 = 9`, not 18. Another mistake is forgetting the initial zero: the boundary interpretation then changes and the formula no longer works as written.

Building the prefix array requires one traversal and storage for `n + 1` totals, where `n` is the number of input elements. Each subsequent query uses two indexed reads and one subtraction, regardless of range length. For a single short query, direct accumulation may be simpler. If original values change, stored totals become stale. For example, replacing the first value affects every nonempty prefix. Rebuild or update affected totals before answering more queries.

<a id="p2-practice-2---inclusive-and-empty-ranges"></a>
### Practice 2 - Inclusive and empty ranges

For `[8, -3, 4, 6]`, find the sum of indexes 1 through 3 inclusive, the sum of index 2 alone, and the sum of the empty half-open range `[2, 2)`.

<a id="p2-worked-solution-1"></a>
#### Worked solution

```javascript
const values = [8, -3, 4, 6];
const prefix = [0];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
console.log(prefix[4] - prefix[1]);
console.log(prefix[3] - prefix[2]);
console.log(prefix[2] - prefix[2]);
```

Expected output:

```text
7
4
0
```

The boundaries contain `[0, 8, 5, 9, 15]`. The first answer is 15 minus 8. A single-element range has adjacent boundaries, so the second answer is 9 minus 5. Equal boundaries cancel to zero.

<a id="p2-3-other-prefix-aggregates"></a>
## 3. Other prefix aggregates

An aggregate summarizes several values. Addition is not the only useful aggregation operation. We can maintain counts, products, minimums and maximums. However, not all aggregates support range extraction by subtracting two prefixes.

<a id="p2-conditional-counts"></a>
### Conditional counts

Suppose a sensor reports `[12, 5, 18, 7, 20]`. We want to count readings of at least 10 in any range. Instead of accumulating the reading itself, add one when the reading qualifies and zero otherwise.

```javascript
const readings = [12, 5, 18, 7, 20];
const counts = [0];
for (let i = 0; i < readings.length; i++) {
  let contribution = 0;
  if (readings[i] >= 10) {
    contribution = 1;
  }
  counts.push(counts[i] + contribution);
}
console.log(counts.join(' '));
console.log(counts[4] - counts[1]);
```

Expected output:

```text
0 1 1 2 2 3
1
```

The range `[1, 4)` contains 5, 18 and 7. Only 18 qualifies. The difference `2 - 1` removes the qualifying reading before the range. This is a prefix sum of indicator values (zeros and ones), not of the original readings.

<a id="p2-prefix-minimum-and-maximum"></a>
### Prefix minimum and maximum

For every position, suppose we want the greatest value encountered up to that position. We preserve the best value and update it when a larger one arrives. Unlike the sum, a maximum has no ordinary numeric empty-prefix value. For this example, store one result per original index, initializing the best value from the first element only when the input is nonempty.

```javascript
const values = [-8, -3, -6, 2, 1];
const maxima = [];
if (values.length > 0) {
  let best = values[0];
  for (let i = 0; i < values.length; i++) {
    if (values[i] > best) {
      best = values[i];
    }
    maxima.push(best);
  }
}
console.log(maxima.join(' '));
```

Expected output:

```text
-8 -3 -3 2 2
```

Here `maxima[i]` includes original index `i`. This differs from the boundary-indexed sum convention. Initializing `best` to zero would incorrectly report zero for the negative-only beginning. For a minimum, reverse the comparison. Neither minimum nor maximum can generally be recovered for an arbitrary range by subtracting prefix extrema: the earlier maximum may hide every value in the later range.

<a id="p2-prefix-products"></a>
### Prefix products

Multiplication has identity one: multiplying by one preserves a value. A product prefix therefore starts with one. With nonzero factors and exact arithmetic, dividing two prefix products removes the earlier factors.

```javascript
const values = [2, 3, 4, 5];
const products = [1];
for (let i = 0; i < values.length; i++) {
  products.push(products[i] * values[i]);
}
console.log(products.join(' '));
console.log(products[3] / products[1]);
```

Expected output:

```text
1 2 6 24 120
12
```

A zero destroys this division strategy: after a zero, every ordinary prefix product is zero. Dividing zero by zero yields `NaN`. One solution is to maintain a separate prefix count of zeros and a product of nonzero factors. A range containing any zero has product zero; otherwise divide the nonzero products. Large products can overflow or lose precision, so this is not a universally safe numeric solution.

```javascript
const values = [2, 0, 3, 4];
const zeros = [0];
const products = [1];
for (let i = 0; i < values.length; i++) {
  if (values[i] === 0) {
    zeros.push(zeros[i] + 1);
    products.push(products[i]);
  } else {
    zeros.push(zeros[i]);
    products.push(products[i] * values[i]);
  }
}
const ranges = [[0, 3], [2, 4], [1, 1]];
for (let i = 0; i < ranges.length; i++) {
  const start = ranges[i][0];
  const stop = ranges[i][1];
  if (zeros[stop] - zeros[start] > 0) {
    console.log(0);
  } else {
    console.log(products[stop] / products[start]);
  }
}
```

Expected output:

```text
0
12
1
```

The empty product is one. For `[2, 4)`, the zero count difference is zero, and the nonzero product ratio is `24 / 2 = 12`.

<a id="p2-practice-3---count-failures-and-find-running-minimums"></a>
### Practice 3 - Count failures and find running minimums

For `[6, 2, 8, 1, 4]`, count values below 5 in `[1, 5)` and produce the running minimum at every index.

<a id="p2-worked-solution-2"></a>
#### Worked solution

```javascript
const values = [6, 2, 8, 1, 4];
const counts = [0];
const minima = [];
let best = values[0];
for (let i = 0; i < values.length; i++) {
  let contribution = 0;
  if (values[i] < 5) contribution = 1;
  counts.push(counts[i] + contribution);
  if (values[i] < best) best = values[i];
  minima.push(best);
}
console.log(counts[5] - counts[1]);
console.log(minima.join(' '));
```

Expected output:

```text
3
6 2 2 1 1
```

The qualifying values are 2, 1 and 4. The minimum changes only when 2 and then 1 are encountered. These are independent accumulators, even though they share a traversal. The given input is nonempty; for reusable code, guard initialization as in the earlier maximum example.

<a id="p2-4-suffix-aggregates"></a>
## 4. Suffix aggregates

A suffix is an ending portion of a sequence. Suppose a warehouse wants to know the total demand remaining from each day onward. For `[4, 7, 2, 9, 3]`, the demand from index 3 onward is 12, while the demand from index 2 onward is 14.

Rather than adding the same ending values repeatedly, traverse backward. Define `suffix[i]` as the sum from index `i` through the end. The boundary at `length` represents an empty suffix and has sum zero.

```javascript
const demand = [4, 7, 2, 9, 3];
const suffix = [];
suffix[demand.length] = 0;
for (let i = demand.length - 1; i >= 0; i--) {
  suffix[i] = demand[i] + suffix[i + 1];
}
console.log(suffix.join(' '));
```

Expected output:

```text
25 21 14 12 3 0
```

Assigning to index 5 of an empty array temporarily creates holes at indexes 0 through 4. The backward loop fills all of them before the array is used. Alternatively, initialize all positions to zero with a loop first. At `i = 3`, `suffix[i + 1]` retrieves `suffix[4]`, which already contains 3. Adding `demand[3]` gives 12. Forward traversal would attempt to read totals that have not yet been calculated.

| i | Current value | Known suffix to right | New suffix |
|---:|---:|---:|---:|
| 4 | 3 | 0 | 3 |
| 3 | 9 | 3 | 12 |
| 2 | 2 | 12 | 14 |
| 1 | 7 | 14 | 21 |
| 0 | 4 | 21 | 25 |

A half-open range `[start, stop)` can also be calculated as `suffix[start] - suffix[stop]`. For `[1, 4)`, this gives `21 - 3 = 18`.

<a id="p2-suffix-maximums-and-future-comparisons"></a>
### Suffix maximums and future comparisons

Suppose we want to identify days whose sales exceed every later day's sales. Maintain the maximum of the already processed suffix. The last day qualifies because no later day exists. Equal values do not qualify under the strict requirement.

```javascript
const sales = [8, 3, 7, 7, 2];
const leaders = [];
if (sales.length > 0) {
  let best = sales[sales.length - 1];
  leaders.push(best);
  for (let i = sales.length - 2; i >= 0; i--) {
    if (sales[i] > best) {
      leaders.push(sales[i]);
      best = sales[i];
    }
  }
}
console.log(leaders.join(' '));
```

Expected output:

```text
2 7 8
```

The results are in reverse traversal order. The rightmost 7 qualifies, but the earlier 7 does not because it equals a later value. If original order is required, traverse `leaders` backward into a new array or reverse it using the technique from Part 1. We need only a running suffix maximum here, not a stored array of all suffix maximums.

<a id="p2-practice-4---remaining-demand"></a>
### Practice 4 - Remaining demand

For `[5, 0, 8, 2]`, construct suffix sums and find demand from index 1 onward and demand strictly after index 2.

<a id="p2-worked-solution-3"></a>
#### Worked solution

```javascript
const values = [5, 0, 8, 2];
const suffix = [];
suffix[values.length] = 0;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] + suffix[i + 1];
}
console.log(suffix.join(' '));
console.log(suffix[1]);
console.log(suffix[2 + 1]);
```

Expected output:

```text
15 10 10 2 0
10
2
```

The phrase strictly after excludes the current index, so we add one to the index before accessing the suffix.

<a id="p2-5-combining-prefix-and-suffix-information"></a>
## 5. Combining prefix and suffix information

<a id="p2-sum-excluding-each-element"></a>
### Sum excluding each element

For `[4, 7, 2, 9]`, produce the sum of all other elements for every position. The values before index `i` have sum `prefix[i]`; the values after it have sum `suffix[i + 1]`. Adding them excludes the current element exactly once.

```javascript
const values = [4, 7, 2, 9];
const prefix = [0];
const suffix = [];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] + values[i]);
}
suffix[values.length] = 0;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] + suffix[i + 1];
}
const result = [];
for (let i = 0; i < values.length; i++) {
  result.push(prefix[i] + suffix[i + 1]);
}
console.log(result.join(' '));
```

Expected output:

```text
18 15 20 13
```

At index 1, the left sum is 4 and the right sum is 11, giving 15. For sums alone, calculating the total once and subtracting each current value is simpler. The combined technique becomes more valuable when subtraction or division is unavailable or unsuitable.

<a id="p2-product-excluding-each-element-without-division"></a>
### Product excluding each element without division

Given `[2, 0, 3, 4]`, calculate the product of every element except the current one. The result at the zero position should be 24; every other result should be zero. Dividing the total product by the current value fails because the total is zero and the zero position requires division by zero.

```javascript
const values = [2, 0, 3, 4];
const prefix = [1];
const suffix = [];
for (let i = 0; i < values.length; i++) {
  prefix.push(prefix[i] * values[i]);
}
suffix[values.length] = 1;
for (let i = values.length - 1; i >= 0; i--) {
  suffix[i] = values[i] * suffix[i + 1];
}
const result = [];
for (let i = 0; i < values.length; i++) {
  result.push(prefix[i] * suffix[i + 1]);
}
console.log(result.join(' '));
```

Expected output:

```text
0 24 0 0
```

| i | Product before i | Product after i | Result |
|---:|---:|---:|---:|
| 0 | 1 | 0 | 0 |
| 1 | 2 | 12 | 24 |
| 2 | 0 | 4 | 0 |
| 3 | 0 | 1 | 0 |

The identity one makes the first and last positions work without special branches. A single-element array produces `[1]` because the product of no other elements is one. Two or more zeros produce all-zero results. An empty array produces an empty result.

We can avoid storing both aggregate arrays. First store the product strictly before each index in the result. Then traverse backward, multiplying by a running product strictly after each index.

```javascript
const values = [2, 3, 4, 5];
const result = [];
let before = 1;
for (let i = 0; i < values.length; i++) {
  result.push(before);
  before *= values[i];
}
let after = 1;
for (let i = values.length - 1; i >= 0; i--) {
  result[i] *= after;
  after *= values[i];
}
console.log(result.join(' '));
```

Expected output:

```text
60 40 30 24
```

The order of operations is essential. `result.push(before)` must occur before multiplying by the current value, otherwise the stored product includes the excluded element. Similarly, `result[i] *= after` must occur before updating `after`. The compound assignment means multiply the existing result at index `i` by the current suffix product and store it back.

<a id="p2-practice-5---balance-position"></a>
### Practice 5 - Balance position

Find every index where the sum strictly before the index equals the sum strictly after it. For `[1, 3, 2, 2, 2]`, the expected index is 2.

<a id="p2-worked-solution-4"></a>
#### Worked solution

```javascript
const values = [1, 3, 2, 2, 2];
let total = 0;
for (let i = 0; i < values.length; i++) total += values[i];
let left = 0;
const positions = [];
for (let i = 0; i < values.length; i++) {
  const right = total - left - values[i];
  if (left === right) positions.push(i);
  left += values[i];
}
console.log(positions.join(' '));
```

Expected output:

```text
2
```

At index 2, the total is 10, the left sum is 4, and the current value is 2. Subtracting both from the total gives 4. Update the left sum only after comparing, so the current element is excluded.

<a id="p2-6-fixed-size-sliding-windows"></a>
## 6. Fixed-size sliding windows

A window is a contiguous portion of a collection. A fixed-size window always contains the same number of elements. Suppose we need the greatest total sales over any three consecutive days in `[4, 7, 2, 9, 3, 6]`.

The possible windows are:

```text
Indexes 0..2: 4 7 2 = 13
Indexes 1..3: 7 2 9 = 18
Indexes 2..4: 2 9 3 = 14
Indexes 3..5: 9 3 6 = 18
```

We require the greatest sum and its earliest starting index. The expected answer is 18 at index 1.

<a id="p2-direct-calculation"></a>
### Direct calculation

```javascript
const values = [4, 7, 2, 9, 3, 6];
const k = 3;
let best = 0;
let bestStart = -1;
for (let start = 0; start <= values.length - k; start++) {
  let sum = 0;
  for (let i = start; i < start + k; i++) {
    sum += values[i];
  }
  if (bestStart === -1 || sum > best) {
    best = sum;
    bestStart = start;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
18 1
```

The last possible start is `length - k`: for six values and a window of three, index 3 is the last valid start. The inner loop visits exactly `k` elements. `bestStart === -1` ensures the first valid sum becomes the best even if it is negative. This solution is straightforward but adds the shared elements again for every window.

<a id="p2-deriving-the-rolling-sum"></a>
### Deriving the rolling sum

Moving a window one position to the right removes its first value and adds the next value. All other values remain unchanged.

```text
Original window: [4 7 2] 9 3 6   sum = 13
Remove 4:          7 2          sum = 9
Add 9:            [7 2 9]       sum = 18
```

We can calculate the first window normally, then maintain its sum using one subtraction and one addition per shift.

```javascript
const values = [4, 7, 2, 9, 3, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) {
  sum += values[i];
}
let best = sum;
let bestStart = 0;
for (let right = k; right < values.length; right++) {
  const outgoing = values[right - k];
  const incoming = values[right];
  sum = sum - outgoing + incoming;
  const start = right - k + 1;
  if (sum > best) {
    best = sum;
    bestStart = start;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
18 1
```

The variable `right` identifies the incoming element, not the old window's right boundary. Initially it equals 3. The outgoing index is `right - k`, which evaluates to `3 - 3 = 0`. The incoming value is `values[3]`, or 9. The updated sum is `13 - 4 + 9 = 18`. The new start is `3 - 3 + 1 = 1`. Adding one is necessary because the incoming index is included in the new window.

| Incoming index | Outgoing value | Incoming value | Updated sum | Start |
|---:|---:|---:|---:|---:|
| Initial | - | - | 13 | 0 |
| 3 | 4 | 9 | 18 | 1 |
| 4 | 7 | 3 | 14 | 2 |
| 5 | 2 | 6 | 18 | 3 |

Strict `sum > best` preserves the earliest tied result. Using `>=` instead selects the latest tied result.

<a id="p2-validating-window-size"></a>
### Validating window size

A nonempty fixed window requires an integer `k` between one and the array length. A zero-size window could be defined separately, but is excluded here. Validate before initializing the first sum.

```javascript
const values = [-8, -2, -5];
const k = 2;
if (!Number.isInteger(k) || k < 1 || k > values.length) {
  console.log('Invalid window size');
} else {
  let sum = 0;
  for (let i = 0; i < k; i++) sum += values[i];
  let best = sum;
  for (let right = k; right < values.length; right++) {
    sum += values[right] - values[right - k];
    if (sum > best) best = sum;
  }
  console.log(best);
}
```

Expected output:

```text
-7
```

Initializing `best` to zero would produce an impossible result because both valid windows have negative sums. With `k = length`, the second loop does not execute; the first window is the only candidate. With `k = 1`, each element is a separate window.

<a id="p2-practice-6---minimum-window-and-ties"></a>
### Practice 6 - Minimum window and ties

Find the minimum sum of two consecutive elements in `[5, -2, 3, -2, 6]`, reporting the latest tied start.

<a id="p2-worked-solution-5"></a>
#### Worked solution

```javascript
const values = [5, -2, 3, -2, 6];
const k = 2;
let sum = values[0] + values[1];
let best = sum;
let bestStart = 0;
for (let right = k; right < values.length; right++) {
  sum += values[right] - values[right - k];
  if (sum <= best) {
    best = sum;
    bestStart = right - k + 1;
  }
}
console.log(best, bestStart);
```

Expected output:

```text
1 2
```

The sums are 3, 1, 1 and 4. The inclusive comparison updates the result at both occurrences of 1, retaining start 2. The initial sum is written explicitly because the specified window size is two; a general implementation should use the initialization loop.

<a id="p2-7-fixed-window-variations"></a>
## 7. Fixed-window variations

<a id="p2-moving-averages"></a>
### Moving averages

A moving average summarizes the most recent fixed number of observations. For temperatures `[20, 22, 24, 26, 28]`, calculate every complete three-reading average. The sum changes as before; divide each completed sum by the constant window size.

```javascript
const values = [20, 22, 24, 26, 28];
const k = 3;
let sum = 0;
const averages = [];
for (let i = 0; i < values.length; i++) {
  sum += values[i];
  if (i >= k) {
    sum -= values[i - k];
  }
  if (i >= k - 1) {
    averages.push(sum / k);
  }
}
console.log(averages.join(' '));
```

Expected output:

```text
22 24 26
```

This single-loop variation grows the initial window before sliding it. At index 2, three values have arrived and the first average is ready. At index 3, adding 26 temporarily creates a four-element sum; subtracting `values[0]` restores the required size. The condition `i >= k - 1` prevents reporting incomplete windows. If partial averages were required, divide by the actual number of elements instead during initialization.

<a id="p2-counting-qualifying-elements-in-a-window"></a>
### Counting qualifying elements in a window

Find how many windows of size three in `[1, 0, 1, 1, 0, 1]` contain at least two ones. Maintain a count rather than a sum of arbitrary readings. Each incoming one increases the count; each outgoing one decreases it.

```javascript
const values = [1, 0, 1, 1, 0, 1];
const k = 3;
let ones = 0;
let qualifying = 0;
for (let right = 0; right < values.length; right++) {
  if (values[right] === 1) ones++;
  if (right >= k && values[right - k] === 1) ones--;
  if (right >= k - 1 && ones >= 2) qualifying++;
}
console.log(qualifying);
```

Expected output:

```text
4
```

There are two separate counts. `ones` describes the current window; `qualifying` describes the number of completed windows that satisfy the requirement. Resetting `ones` on every iteration would lose the retained information.

<a id="p2-window-frequency-tables-and-distinct-values"></a>
### Window frequency tables and distinct values

Suppose we need the number of distinct values in each window of size three in `[1, 2, 1, 3, 2]`. A set alone is insufficient for removal: when one occurrence leaves, another occurrence of the same value may remain. We need occurrence counts.

Recall that a `Map` associates keys with values. `new Map()` creates an empty map; `has(key)` tests presence, `get(key)` retrieves the associated value (or undefined when absent), `set(key, value)` stores a value, `delete(key)` removes a key, and the `size` property counts keys. For example, setting key 1 to count 2 means two occurrences, not two keys. We keep only keys whose counts are positive.

```javascript
const values = [1, 2, 1, 3, 2];
const k = 3;
const counts = new Map();
const result = [];
for (let right = 0; right < values.length; right++) {
  const incoming = values[right];
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  if (right >= k) {
    const outgoing = values[right - k];
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) {
      counts.delete(outgoing);
    } else {
      counts.set(outgoing, remaining);
    }
  }
  if (right >= k - 1) result.push(counts.size);
}
console.log(result.join(' '));
```

Expected output:

```text
2 3 3
```

For the first window, the map stores 1 -> 2 and 2 -> 1. When 3 enters, it receives count one. When the first 1 leaves, its count decreases from two to one, so it must not be deleted. The new map has three keys. Keeping zero-count keys would make `size` overstate the number of distinct values.

<a id="p2-practice-7---moving-average-and-frequency"></a>
### Practice 7 - Moving average and frequency

For `[2, 4, 2, 8]`, report the average and number of distinct values for each complete window of size two.

<a id="p2-worked-solution-6"></a>
#### Worked solution

```javascript
const values = [2, 4, 2, 8];
const k = 2;
let sum = 0;
const counts = new Map();
for (let i = 0; i < values.length; i++) {
  const incoming = values[i];
  sum += incoming;
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  if (i >= k) {
    const outgoing = values[i - k];
    sum -= outgoing;
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) counts.delete(outgoing);
    else counts.set(outgoing, remaining);
  }
  if (i >= k - 1) console.log(sum / k, counts.size);
}
```

Expected output:

```text
3 2
3 2
5 2
```

Both aggregates describe the same window. When an incoming value equals the outgoing value, adding and removing one occurrence leaves the final count unchanged. The sum likewise remains unchanged. The two aggregate updates must finish before reporting.

<a id="p2-8-variable-size-sliding-windows"></a>
## 8. Variable-size sliding windows

A variable-size window changes its length according to a condition. Rather than advancing both boundaries together, we expand the right boundary and shrink the left boundary when necessary. The appropriate shrinking rule depends on the problem.

<a id="p2-longest-range-with-sum-at-most-a-limit"></a>
### Longest range with sum at most a limit

Given nonnegative values `[2, 1, 4, 1, 1, 3]` and limit 5, find the longest contiguous range whose sum does not exceed 5. Report the earliest start among ties. The expected length is 3 at start 3 (values 1, 1, 3).

We begin with an empty window and sum zero. Adding a value cannot decrease the sum because all values are nonnegative. If the sum exceeds the limit, remove values from the left until it becomes valid again. Removing a nonnegative value cannot increase the sum. These properties justify the direction of pointer movement.

```javascript
const values = [2, 1, 4, 1, 1, 3];
const limit = 5;
let left = 0;
let sum = 0;
let bestLength = 0;
let bestStart = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (sum > limit) {
    sum -= values[left];
    left++;
  }
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
console.log(bestLength, bestStart);
```

Expected output:

```text
3 3
```

| right | Added | Sum before shrinking | Removed | left after shrinking | Valid sum | Length |
|---:|---:|---:|---|---:|---:|---:|
| 0 | 2 | 2 | none | 0 | 2 | 1 |
| 1 | 1 | 3 | none | 0 | 3 | 2 |
| 2 | 4 | 7 | 2 | 1 | 5 | 2 |
| 3 | 1 | 6 | 1 | 2 | 5 | 2 |
| 4 | 1 | 6 | 4 | 3 | 2 | 2 |
| 5 | 3 | 5 | none | 3 | 5 | 3 |

At right index 2, adding 4 makes the sum 7. Subtracting `values[left]` removes 2. Incrementing `left` changes the represented window from `[0, 2]` to `[1, 2]`. The length expression `right - left + 1` counts both included endpoints. At the final iteration it is `5 - 3 + 1 = 3`.

The inner loop is a `while`, not an `if`, because removing one element may be insufficient. For `[1, 1, 10]` with limit 2, all three elements must be removed when 10 enters. The resulting empty window has `left = right + 1` and length zero. With nonnegative values and a nonnegative limit, the empty sum is always valid, so shrinking cannot run beyond the window.

Why is it safe never to move left backward? A discarded start produced a sum greater than the limit. Extending that same range to the right with nonnegative values cannot make it valid later. The algorithm retains the earliest valid start for each right endpoint, giving the longest valid window ending there.

<a id="p2-shortest-range-reaching-a-target"></a>
### Shortest range reaching a target

Now find the shortest nonempty contiguous range whose sum is at least 7 in `[2, 3, 1, 2, 4, 3]`. The expected length is two at start 4.

The condition has changed. Previously we shrank invalid windows until valid. Here we shrink valid windows to discover whether a shorter valid window exists. Record a candidate before removing its leftmost element.

```javascript
const values = [2, 3, 1, 2, 4, 3];
const target = 7;
let left = 0;
let sum = 0;
let bestLength = values.length + 1;
let bestStart = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (left <= right && sum >= target) {
    const length = right - left + 1;
    if (length < bestLength) {
      bestLength = length;
      bestStart = left;
    }
    sum -= values[left];
    left++;
  }
}
if (bestStart === -1) console.log('No matching range');
else console.log(bestLength, bestStart);
```

Expected output:

```text
2 4
```

`values.length + 1` is larger than any possible valid length, so the first candidate replaces it. The explicit `left <= right` guard prevents considering empty ranges. Assume nonnegative values and a positive target. At right index 3 the sum reaches 8, and the length-four window is recorded. Removing 2 leaves 6, so shrinking stops. At right index 4 the sum becomes 10. Removing 3 after recording leaves 7, allowing another valid candidate of length three. At right index 5, the window `[4, 5]` reaches 7 and improves the answer to two.

A discarded valid start cannot improve the shortest result by extending farther right: any future window with that start would be longer than the one already recorded. This is the justification for permanent removal in this problem.

<a id="p2-longest-substring-without-repeated-characters"></a>
### Longest substring without repeated characters

The same strategy can maintain a condition other than a sum. Given `"abcaefbb"`, find the longest contiguous substring with no repeated characters. The expected result is `"bcaef"`, length five.

Use a set of characters in the current window. A set stores distinct values; `new Set()` creates an empty set, `has` tests membership, `add` inserts a value and `delete` removes it. Because the maintained window has no duplicates, deleting a character removes its only occurrence.

```javascript
const text = 'abcaefbb';
const seen = new Set();
let left = 0;
let bestStart = 0;
let bestLength = 0;
for (let right = 0; right < text.length; right++) {
  const incoming = text[right];
  while (seen.has(incoming)) {
    seen.delete(text[left]);
    left++;
  }
  seen.add(incoming);
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
let result = '';
for (let i = bestStart; i < bestStart + bestLength; i++) {
  result += text[i];
}
console.log(result, bestLength);
```

Expected output:

```text
bcaef 5
```

When the second `a` arrives, the set contains `a`, `b`, `c`. Remove the first `a`, move left to index 1, and insert the new `a`. Later, when a repeated `b` arrives, remove characters until the previous `b` has left. The set is updated before calculating the candidate length. This example compares UTF-16 code units, suitable for the given English text.

<a id="p2-practice-8---longest-range-with-zeros"></a>
### Practice 8 - Longest range with zeros

Find the longest contiguous range in `[0, 2, 0, 3, 0, 0]` with sum at most 3. Report the earliest start.

<a id="p2-worked-solution-7"></a>
#### Worked solution

```javascript
const values = [0, 2, 0, 3, 0, 0];
let left = 0;
let sum = 0;
let best = 0;
let start = -1;
for (let right = 0; right < values.length; right++) {
  sum += values[right];
  while (sum > 3) {
    sum -= values[left];
    left++;
  }
  const length = right - left + 1;
  if (length > best) {
    best = length;
    start = left;
  }
}
console.log(best, start);
```

Expected output:

```text
4 2
```

When 3 enters at index 3, the sum is 5. Removing the first zero does not reduce the sum, but advancing left still makes progress. Removing 2 reduces the sum to 3. The final window spans indexes 2 through 5 and includes three zeros. Zero values do not invalidate the monotonicity reasoning.

<a id="p2-practice-9---shortest-qualifying-range-and-no-match"></a>
### Practice 9 - Shortest qualifying range and no match

For `[1, 1, 6, 2]`, find the shortest range with sum at least 6. Then consider target 11.

<a id="p2-worked-solution-8"></a>
#### Worked solution

```javascript
const values = [1, 1, 6, 2];
const targets = [6, 11];
for (let t = 0; t < targets.length; t++) {
  let left = 0;
  let sum = 0;
  let best = values.length + 1;
  for (let right = 0; right < values.length; right++) {
    sum += values[right];
    while (left <= right && sum >= targets[t]) {
      const length = right - left + 1;
      if (length < best) best = length;
      sum -= values[left];
      left++;
    }
  }
  if (best > values.length) console.log('No matching range');
  else console.log(best);
}
```

Expected output:

```text
1
No matching range
```

When 6 arrives, the sum is 8. The algorithm records lengths three, two and one while removing the preceding ones. The entire input totals 10, so no range can reach 11. Reset all window state for each target; otherwise one query contaminates the next.

<a id="p2-9-window-validity-and-limitations"></a>
## 9. Window validity and limitations

<a id="p2-why-negative-values-can-invalidate-variable-sum-windows"></a>
### Why negative values can invalidate variable sum windows

Consider `[5, -4, 2]` with limit 3. The longest valid range is the entire array, whose sum is 3. The earlier longest-window algorithm would discard 5 immediately because it exceeds the limit. When negative 4 arrives, that discarded start would have become valid again. The algorithm cannot recover it because left never moves backward.

The failure is not caused by negative numbers in every sliding-window problem. Fixed-size rolling sums still work with negative values. The failure arises specifically because the variable-window elimination rule assumed that extending a range cannot decrease its sum.

A correct general fallback is to examine all contiguous ranges while maintaining a running sum for each start.

```javascript
const values = [5, -4, 2];
const limit = 3;
let bestLength = 0;
let bestStart = -1;
for (let start = 0; start < values.length; start++) {
  let sum = 0;
  for (let end = start; end < values.length; end++) {
    sum += values[end];
    const length = end - start + 1;
    if (sum <= limit && length > bestLength) {
      bestLength = length;
      bestStart = start;
    }
  }
}
console.log(bestLength, bestStart);
```

Expected output:

```text
3 0
```

Do not break the inner loop when the sum exceeds the limit: a later negative value may restore validity. This approach examines all ranges, but avoids recomputing each range from scratch. More advanced algorithms exist, but require additional techniques beyond this part.

<a id="p2-counting-ranges-with-an-exact-sum-including-negatives"></a>
### Counting ranges with an exact sum, including negatives

For `[1, -1, 1, 0]`, count nonempty contiguous ranges whose sum equals 1. Prefix sums offer another solution. If the current prefix is `p`, an earlier prefix must equal `p - target` for their difference to be the target. We can store how often each earlier prefix occurred in a map.

```javascript
const values = [1, -1, 1, 0];
const target = 1;
const counts = new Map();
counts.set(0, 1);
let prefix = 0;
let answer = 0;
for (let i = 0; i < values.length; i++) {
  prefix += values[i];
  const needed = prefix - target;
  if (counts.has(needed)) answer += counts.get(needed);
  let previousCount = 0;
  if (counts.has(prefix)) previousCount = counts.get(prefix);
  counts.set(prefix, previousCount + 1);
}
console.log(answer);
```

Expected output:

```text
5
```

| i | Current prefix | Needed earlier prefix | New matches | Total matches |
|---:|---:|---:|---:|---:|
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | -1 | 0 | 1 |
| 2 | 1 | 0 | 2 | 3 |
| 3 | 1 | 0 | 2 | 5 |

The initial zero prefix represents the boundary before the first element. Without it, ranges starting at index zero would be missed. Count matches before inserting the current prefix; otherwise a target of zero would count an empty range. Multiple earlier occurrences of the needed prefix correspond to different starting boundaries, so add their count rather than merely one.

<a id="p2-why-maximums-cannot-be-updated-like-sums"></a>
### Why maximums cannot be updated like sums

If a window contains `[9, 2, 4]`, its maximum is 9. When 9 leaves and 3 enters, the new maximum is 4. Knowing only the old maximum and the incoming and outgoing values is insufficient to determine 4. One straightforward solution is to rescan each window. A more advanced solution maintains candidate positions in a special ordered queue, but that structure is outside this part. Do not apply the rolling-sum subtraction rule to a maximum.

<a id="p2-work-and-numerical-correctness"></a>
### Work and numerical correctness

A direct fixed-window scan performs approximately `k` additions per window. A rolling sum performs one initialization traversal and two arithmetic updates per shift. A variable window may contain a nested while loop, but each element enters once and leaves at most once; the total number of pointer advances is bounded by twice the input length. This is why nested syntax alone does not establish computational cost.

JavaScript Number arithmetic can round fractional values or large integers. Repeated addition and subtraction may accumulate error; prefix subtraction can also lose small differences between large totals. For exact monetary calculations, represent amounts in integer minor units when values remain within the safe integer range. Do not assume an exact equality comparison is reliable for arbitrary floating-point totals. Choose a numerical representation appropriate to the requirements.

<a id="p2-practice-10---explain-a-failed-elimination"></a>
### Practice 10 - Explain a failed elimination

Why does the longest-sum-at-most algorithm fail for `[4, -3]` with limit 1? Would a fixed window of size two fail for the same input?

<a id="p2-worked-answer"></a>
#### Worked answer

The variable algorithm discards 4 when the first window exceeds 1. Adding negative 3 would have made the original two-element range valid, but its start was lost. A fixed window of size two calculates `4 + (-3) = 1` correctly. Fixed-size updates do not discard possible starts based on the sign of the sum.

<a id="p2-10-integrated-exercises"></a>
## 10. Integrated exercises

Attempt each problem before consulting its worked solution.

<a id="p2-exercise-101---sales-range-report"></a>
### Exercise 10.1 - Sales range report

Given `[10, 0, 20, 5, 15]`, report total sales and number of positive-sales days for half-open ranges `[0, 3)`, `[2, 5)` and `[3, 3)`.

<a id="p2-worked-solution-9"></a>
#### Worked solution

```javascript
const sales = [10, 0, 20, 5, 15];
const totals = [0];
const days = [0];
for (let i = 0; i < sales.length; i++) {
  totals.push(totals[i] + sales[i]);
  let positive = 0;
  if (sales[i] > 0) positive = 1;
  days.push(days[i] + positive);
}
const ranges = [[0, 3], [2, 5], [3, 3]];
for (let i = 0; i < ranges.length; i++) {
  const start = ranges[i][0];
  const stop = ranges[i][1];
  console.log(totals[stop] - totals[start], days[stop] - days[start]);
}
```

Expected output:

```text
30 2
40 3
0 0
```

Both arrays use the same boundary convention. The total prefix is `[0, 10, 10, 30, 35, 50]`; the positive-day prefix is `[0, 1, 1, 2, 3, 4]`. For the second query, subtract boundary 2 from boundary 5 in both arrays. The empty range cancels both aggregates.

<a id="p2-exercise-102---product-except-self-with-two-zeros"></a>
### Exercise 10.2 - Product except self with two zeros

For `[0, 2, 0, 5]`, calculate every product excluding the current element, without division.

<a id="p2-worked-solution-10"></a>
#### Worked solution

```javascript
const values = [0, 2, 0, 5];
const result = [];
let before = 1;
for (let i = 0; i < values.length; i++) {
  result.push(before);
  before *= values[i];
}
let after = 1;
for (let i = values.length - 1; i >= 0; i--) {
  result[i] *= after;
  after *= values[i];
}
console.log(result.join(' '));
```

Expected output:

```text
0 0 0 0
```

Every excluded position leaves at least one zero among the remaining factors. The forward pass stores `[1, 0, 0, 0]`. The backward pass multiplies each by the product strictly to its right. This handles zeros naturally without special division rules.

<a id="p2-exercise-103---best-three-day-average"></a>
### Exercise 10.3 - Best three-day average

For `[6, 3, 9, 12, 0, 6]`, report the earliest three-day window with greatest average, including its start and average.

<a id="p2-worked-solution-11"></a>
#### Worked solution

```javascript
const values = [6, 3, 9, 12, 0, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) sum += values[i];
let best = sum;
let start = 0;
for (let right = k; right < values.length; right++) {
  sum += values[right] - values[right - k];
  if (sum > best) {
    best = sum;
    start = right - k + 1;
  }
}
console.log(start, best / k);
```

Expected output:

```text
1 8
```

Since all windows have equal size, comparing sums is equivalent to comparing averages. The sums are 18, 24, 21 and 18. Divide only the best sum by three at the end. This avoids unnecessary division during each iteration.

<a id="p2-exercise-104---longest-range-containing-at-most-two-distinct-values"></a>
### Exercise 10.4 - Longest range containing at most two distinct values

Given `[1, 2, 1, 3, 3, 2, 2]`, find the longest contiguous range containing at most two distinct values. Report earliest start and length.

<a id="p2-worked-solution-12"></a>
#### Worked solution

```javascript
const values = [1, 2, 1, 3, 3, 2, 2];
const counts = new Map();
let left = 0;
let bestStart = -1;
let bestLength = 0;
for (let right = 0; right < values.length; right++) {
  const incoming = values[right];
  let count = 0;
  if (counts.has(incoming)) count = counts.get(incoming);
  counts.set(incoming, count + 1);
  while (counts.size > 2) {
    const outgoing = values[left];
    const remaining = counts.get(outgoing) - 1;
    if (remaining === 0) counts.delete(outgoing);
    else counts.set(outgoing, remaining);
    left++;
  }
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
console.log(bestStart, bestLength);
```

Expected output:

```text
3 4
```

When 3 first enters, there are three distinct values. Removing the first 1 leaves another 1, so the number of keys stays three. Removing 2 eliminates its key and restores validity. Later, when 2 returns, removing the remaining 1 restores validity again. The final window `[3, 3, 2, 2]` begins at index 3. Extending a window cannot reduce its distinct count, so discarded starts cannot become valid through rightward extension alone.

<a id="p2-exercise-105---exact-zero-sum-ranges"></a>
### Exercise 10.5 - Exact zero-sum ranges

Count all nonempty zero-sum ranges in `[0, 1, -1, 0]`.

<a id="p2-worked-solution-13"></a>
#### Worked solution

```javascript
const values = [0, 1, -1, 0];
const counts = new Map();
counts.set(0, 1);
let prefix = 0;
let answer = 0;
for (let i = 0; i < values.length; i++) {
  prefix += values[i];
  let previous = 0;
  if (counts.has(prefix)) previous = counts.get(prefix);
  answer += previous;
  counts.set(prefix, previous + 1);
}
console.log(answer);
```

Expected output:

```text
6
```

The boundary prefixes are `[0, 0, 1, 0, 0]`. Every pair of distinct zero-prefix boundaries defines a nonempty zero-sum range. Four such boundaries produce six pairs. The loop counts them incrementally: 1, 0, 2 and 3 new matches. Inserting the current boundary before counting would incorrectly include empty ranges.

<a id="p2-exercise-106---verify-a-rolling-algorithm-independently"></a>
### Exercise 10.6 - Verify a rolling algorithm independently

For `[3, -5, 7, 0, 2]`, calculate every window sum of size three using both direct accumulation and rolling updates. Compare the two result arrays element by element.

<a id="p2-worked-solution-14"></a>
#### Worked solution

```javascript
const values = [3, -5, 7, 0, 2];
const k = 3;
const direct = [];
const rolling = [];
for (let start = 0; start <= values.length - k; start++) {
  let sum = 0;
  for (let i = start; i < start + k; i++) sum += values[i];
  direct.push(sum);
}
let sum = 0;
for (let i = 0; i < values.length; i++) {
  sum += values[i];
  if (i >= k) sum -= values[i - k];
  if (i >= k - 1) rolling.push(sum);
}
let equal = direct.length === rolling.length;
for (let i = 0; i < direct.length && equal; i++) {
  if (direct[i] !== rolling[i]) equal = false;
}
console.log(direct.join(' '));
console.log(rolling.join(' '));
console.log(equal);
```

Expected output:

```text
5 2 9
5 2 9
true
```

The direct implementation serves as an independent reference for the optimized implementation. Check lengths before comparing elements. Testing negative values helps establish that fixed windows do not depend on nonnegative inputs. Additional useful tests include size one, size equal to the array length, all zeros and invalid sizes (which should be rejected before calculation).

<a id="p2-11-summary"></a>
## 11. Summary

| Technique | Retained information | Main requirement |
|---|---|---|
| Prefix sum | Totals before boundaries | Valid range boundaries; unchanged source |
| Suffix sum | Totals from boundaries onward | Backward dependency order |
| Prefix/suffix combination | Information before and after a position | Correct exclusion boundaries |
| Fixed window | Aggregate of current fixed-size range | Valid size; correct removal and addition |
| Variable window | Aggregate and movable boundaries | Justified permanent elimination of starts |
| Prefix frequency map | Counts of earlier prefix totals | Query before inserting current boundary |

When deriving a solution, first identify whether the problem concerns arbitrary range queries, every range of a fixed size, or a range whose size depends on validity. Determine what information can be retained, how it changes when a boundary moves, and why any discarded candidate cannot become useful again. Verify the initial state, boundary cases, update order and tie policy.

[Previous: Part 1](#part-1) | [Back to contents](#p2-table-of-contents) | Next: Part 3 - Processing consecutive values, circular iteration and chunks (planned)


<a id="part-3"></a>

<a id="p3-lesson-21---part-3-consecutive-values-circular-iteration-and-chunks"></a>
# Lesson 21 - Part 3: Consecutive Values, Circular Iteration and Chunks

[Previous](#part-2) | [Next (planned)](#part-4)

<a id="p3-table-of-contents"></a>
## Table of contents

- [Processing consecutive values](#p3-processing-consecutive-values)
- [Circular iteration](#p3-circular-iteration)
- [Processing values in chunks](#p3-processing-values-in-chunks)
- [Integrated practice](#p3-integrated-practice)
- [Summary](#p3-summary)

<a id="p3-processing-consecutive-values"></a>
## Processing consecutive values

A single element does not always provide enough information. To detect a price increase, for example, we must compare a price with its predecessor. To identify a repeated run, we must also remember where the run began. Assume dense arrays of finite numbers unless stated otherwise.

<a id="p3-adjacent-differences"></a>
### Adjacent differences

Given daily readings [12, 15, 11, 18], calculate the signed change between each pair of consecutive days. The first reading has no predecessor, so start at index 1. At index i, the preceding reading occupies index i - 1.

```javascript
const readings = [12, 15, 11, 18];
const changes = [];
for (let i = 1; i < readings.length; i++) {
  changes.push(readings[i] - readings[i - 1]);
}
console.log(changes.join(','));
```

Expected output: `3,-4,7`.

Here join(',') returns a string containing the array elements separated by commas; it is used only to display results consistently. At i = 2, readings[i - 1] retrieves readings[1], or 15. Subtracting it from readings[2], or 11, gives -4. The sign distinguishes a decrease from an increase.

Starting at zero would access readings[-1], which is undefined for this ordinary array. Subtracting undefined produces NaN. Empty and single-element arrays correctly produce no changes.

| i | Previous | Current | Change |
|---|---|---|---|
| 1 | 12 | 15 | 3 |
| 2 | 15 | 11 | -4 |
| 3 | 11 | 18 | 7 |

<a id="p3-tracking-increasing-runs"></a>
### Tracking increasing runs

Find the longest contiguous strictly increasing run in [4, 7, 9, 2, 3, 8, 10, 1]. A contiguous run uses neighboring positions without skipping elements. The desired run starts at index 3 and has length 4.

We need a current start and a best start and length. If a value is not greater than its predecessor, the old run ends and a new run starts at the current index. The current length is i - start + 1 because both endpoints are included.

```javascript
const values = [4, 7, 9, 2, 3, 8, 10, 1];
let start = 0;
let bestStart = 0;
let bestLength = values.length === 0 ? 0 : 1;
for (let i = 1; i < values.length; i++) {
  if (values[i] <= values[i - 1]) {
    start = i;
  }
  const length = i - start + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = start;
  }
}
console.log(bestStart, bestLength);
```

Expected output: `3 4`.

The conditional expression `condition ? a : b` selects a when the condition is true and b otherwise. Here it initializes the best length to zero for empty input and one for nonempty input. It could instead be written as an if/else assignment.

At i = 3, 2 <= 9 is true, so start becomes 3. The new length is 3 - 3 + 1 = 1. At i = 6, the run extends to length 6 - 3 + 1 = 4, exceeding the earlier best of three.

| i | start | length | bestLength |
|---|---|---|---|
| 1 | 0 | 2 | 2 |
| 2 | 0 | 3 | 3 |
| 3 | 3 | 1 | 3 |
| 4 | 3 | 2 | 3 |
| 5 | 3 | 3 | 3 |
| 6 | 3 | 4 | 4 |
| 7 | 7 | 1 | 4 |

Using >= instead of > in the best-length comparison selects the latest tied run rather than the earliest. Changing the reset condition to < permits equal adjacent values, producing nondecreasing runs instead of strictly increasing ones.

<a id="p3-grouping-equal-consecutive-values"></a>
### Grouping equal consecutive values

Given [4, 4, 2, 2, 2, 4], report each consecutive run as a value and count. The two runs of 4 must remain separate. A frequency table would combine them, which is not the requirement.

Start a run at index zero. Continue while the next value equals the run value. When a different value arrives, save the completed run and begin a new one. The final run must also be saved even though no different value follows it.

```javascript
const values = [4, 4, 2, 2, 2, 4];
const runs = [];
if (values.length > 0) {
  let value = values[0];
  let count = 1;
  for (let i = 1; i < values.length; i++) {
    if (values[i] === value) {
      count++;
    } else {
      runs.push({ value: value, count: count });
      value = values[i];
      count = 1;
    }
  }
  runs.push({ value: value, count: count });
}
for (let i = 0; i < runs.length; i++) {
  console.log(runs[i].value, runs[i].count);
}
```

Expected output:

```text
4 2
2 3
4 1
```

The object literal stores a snapshot of the current numeric value and count. Resetting the variables does not change the numbers stored in previously created objects. At index 2, the first run is saved before value changes to 2 and count resets to one. Omitting the final push loses the last run. Initializing count to zero while starting at index one undercounts the first run.

<a id="p3-practice-consecutive-values"></a>
### Practice: consecutive values

**Question 1.** Count decreases in [8, 6, 6, 3, 9, 2]. Equal values are not decreases. Expected: 3.

**Solution.** Compare each element with its predecessor, starting at one. Increment only when the current value is smaller.

```javascript
const values = [8, 6, 6, 3, 9, 2];
let count = 0;
for (let i = 1; i < values.length; i++) {
  if (values[i] < values[i - 1]) count++;
}
console.log(count);
```

Expected output: `3`. The decreases are 8 to 6, 6 to 3, and 9 to 2. Using <= would incorrectly count the equal pair.

**Question 2.** Find the longest run of equal values in [2, 2, 5, 5, 5, 2]. Expected length: 3.

**Solution.** Start with one element in the current run for nonempty input. Increment on equality and reset to one on a mismatch. Update the best after either operation.

```javascript
const values = [2, 2, 5, 5, 5, 2];
let count = values.length === 0 ? 0 : 1;
let best = count;
for (let i = 1; i < values.length; i++) {
  if (values[i] === values[i - 1]) count++;
  else count = 1;
  if (count > best) best = count;
}
console.log(best);
```

Expected output: `3`. The counts are 1, 2, 1, 2, 3, 1. Updating best during traversal also handles a longest run at the end. Empty input returns zero.

<a id="p3-circular-iteration"></a>
## Circular iteration

Ordinary traversal ends at the final index. Circular traversal treats the first position as the successor of the last position. Examples include rotating schedules, repeating playlists and round-robin assignments.

<a id="p3-wrapping-an-index"></a>
### Wrapping an index

For four positions, valid indexes are 0, 1, 2 and 3. After index 3 we want index 0. We could increment and explicitly reset the index when it equals the length. For nonnegative integer positions, remainder provides another approach: position % length.

```javascript
const values = ['A', 'B', 'C', 'D'];
let index = 2;
for (let step = 0; step < 7; step++) {
  console.log(values[index]);
  index = (index + 1) % values.length;
}
```

Expected output:

```text
C
D
A
B
C
D
A
```

At index 3, index + 1 evaluates to 4, and 4 % 4 evaluates to 0. Parentheses ensure the addition occurs before remainder. Without them, index + 1 % 4 would evaluate to index + 1 and fail to wrap. The step counter controls termination independently of the circular index, which repeatedly returns to earlier positions.

For empty input, remainder by zero produces NaN. Check that the collection is nonempty before attempting circular traversal. For a one-element collection, every wrapped index is zero.

<a id="p3-backward-movement-and-negative-offsets"></a>
### Backward movement and negative offsets

JavaScript's % operator is remainder, not always a nonnegative mathematical modulo. For example, -1 % 4 is -1. That is not a valid ordinary array index. To normalize an integer offset into the range 0 through n - 1, use ((offset % n) + n) % n for positive n.

```javascript
const values = ['A', 'B', 'C', 'D'];
let index = 1;
for (let step = 0; step < 6; step++) {
  console.log(values[index]);
  index = ((index - 1) % values.length + values.length) % values.length;
}
```

Expected output:

```text
B
A
D
C
B
A
```

At index zero, index - 1 is -1. The first remainder is -1. Adding four produces three, and the final remainder is three. The second remainder is necessary for nonnegative offsets too: if the first remainder is two, adding four produces six, which must be reduced to two.

<a id="p3-rotating-an-array"></a>
### Rotating an array

Rotate [10, 20, 30, 40, 50] left by two positions, preserving the original. The new first element is the original element at index two. Each following result position advances one source position, wrapping at the end.

```javascript
const values = [10, 20, 30, 40, 50];
const offset = 2;
const rotated = [];
if (values.length > 0) {
  for (let i = 0; i < values.length; i++) {
    const source = ((i + offset) % values.length + values.length) % values.length;
    rotated.push(values[source]);
  }
}
console.log(rotated.join(','));
```

Expected output: `30,40,50,10,20`.

At result index three, i + offset is five. Five % five is zero, so the source is index zero. Negative offsets rotate in the opposite direction. Offsets greater than the length are equivalent to their normalized remainder. The loop runs exactly once per original element, preserving every occurrence. Assume integer offsets small enough for exact JavaScript integer arithmetic.

<a id="p3-circular-adjacent-pairs"></a>
### Circular adjacent pairs

For [3, 8, 5], report each value and its successor, including the last-to-first pair. Unlike ordinary adjacent traversal, the loop includes the last index.

```javascript
const values = [3, 8, 5];
for (let i = 0; i < values.length; i++) {
  const next = (i + 1) % values.length;
  console.log(values[i], values[next]);
}
```

Expected output:

```text
3 8
8 5
5 3
```

At i = 2, next becomes zero. Empty input never enters the loop. A singleton produces a self-pair; if the problem requires distinct positions, add a length check requiring at least two elements.

<a id="p3-practice-circular-iteration"></a>
### Practice: circular iteration

**Question 1.** Assign seven tasks to workers A, B and C in repeating order, beginning with B.

**Solution.** The worker index starts at one. Each task advances the index, wrapping after C. The task counter, not the worker index, determines when to stop.

```javascript
const workers = ['A', 'B', 'C'];
let index = 1;
for (let task = 1; task <= 7; task++) {
  console.log(task, workers[index]);
  index = (index + 1) % workers.length;
}
```

Expected output:

```text
1 B
2 C
3 A
4 B
5 C
6 A
7 B
```

**Question 2.** Rotate [1, 2, 3, 4] right by one. Expected: [4, 1, 2, 3].

**Solution.** Right rotation by one corresponds to a left offset of -1. At result index zero, the source index must normalize -1 to 3.

```javascript
const values = [1, 2, 3, 4];
const result = [];
for (let i = 0; i < values.length; i++) {
  const source = ((i - 1) % values.length + values.length) % values.length;
  result.push(values[source]);
}
console.log(result.join(','));
```

Expected output: `4,1,2,3`. The source indexes are 3, 0, 1, 2. The original array is unchanged.

<a id="p3-processing-values-in-chunks"></a>
## Processing values in chunks

A chunk is a contiguous group of elements. Unlike overlapping sliding windows, ordinary chunks do not overlap. For [1, 2, 3, 4, 5, 6, 7] and chunk size three, the groups are [1, 2, 3], [4, 5, 6], and [7].

<a id="p3-deriving-chunk-boundaries"></a>
### Deriving chunk boundaries

The first chunk starts at zero. Each subsequent chunk starts size positions later. Within a chunk, the exclusive end is start + size, but it must not exceed the array length. We can enforce both limits in the inner loop.

```javascript
const values = [1, 2, 3, 4, 5, 6, 7];
const size = 3;
const chunks = [];
for (let start = 0; start < values.length; start += size) {
  const chunk = [];
  for (let i = start; i < start + size && i < values.length; i++) {
    chunk.push(values[i]);
  }
  chunks.push(chunk);
}
for (let i = 0; i < chunks.length; i++) {
  console.log(chunks[i].join(','));
}
```

Expected output:

```text
1,2,3
4,5,6
7
```

At start = 6, start + size equals nine. The inner loop first visits index six, then stops at index seven because seven < values.length is false. Creating chunk inside the outer loop gives each group its own array. Creating it outside and repeatedly pushing the same array would share one mutable object among all groups.

| start | Exclusive requested end | Actual indexes |
|---|---|---|
| 0 | 3 | 0, 1, 2 |
| 3 | 6 | 3, 4, 5 |
| 6 | 9 | 6 |

<a id="p3-validating-the-chunk-size"></a>
### Validating the chunk size

A zero size makes start += size leave start unchanged, causing nontermination for nonempty input. A negative size moves away from the end. A fractional size eventually produces fractional indexes. Require a positive integer. Number.isInteger(value) returns true for integer-valued numbers and false for fractions, strings, NaN and infinities.

```javascript
const values = [1, 2, 3];
const size = 0;
if (!Number.isInteger(size) || size <= 0) {
  console.log('Invalid chunk size');
} else {
  for (let start = 0; start < values.length; start += size) {
    console.log(start);
  }
}
```

Expected output: `Invalid chunk size`. The logical OR rejects either a noninteger or a nonpositive size. Subsequent examples assume a valid size.

<a id="p3-aggregating-chunks-without-storing-them"></a>
### Aggregating chunks without storing them

Suppose we need the total and average of each group of three sales, including the final partial group. There is no need to construct separate chunk arrays. Reset the sum and count for each group.

```javascript
const sales = [6, 9, 3, 12, 6, 3, 8];
const size = 3;
for (let start = 0; start < sales.length; start += size) {
  let sum = 0;
  let count = 0;
  for (let i = start; i < start + size && i < sales.length; i++) {
    sum += sales[i];
    count++;
  }
  console.log(sum, sum / count);
}
```

Expected output:

```text
18 6
21 7
8 8
```

The final group contains one value. Dividing by size would incorrectly give 8 / 3 rather than 8 / 1. The outer condition ensures each processed group contains at least one element, so count cannot be zero. Keeping sum outside the outer loop would accumulate across groups instead of reporting independent totals.

<a id="p3-full-chunks-only"></a>
### Full chunks only

Sometimes incomplete groups must be ignored, such as when each record requires exactly three fields. Change the outer condition to start + size <= values.length. Equality is necessary because a chunk ending exactly at the array boundary is complete.

```javascript
const values = [1, 2, 3, 4, 5, 6, 7];
const size = 3;
for (let start = 0; start + size <= values.length; start += size) {
  let sum = 0;
  for (let i = start; i < start + size; i++) {
    sum += values[i];
  }
  console.log(sum);
}
```

Expected output:

```text
6
15
```

At start six, 6 + 3 <= 7 is false. The partial group is ignored. If the size exceeds the array length, no full chunks are processed. An alternative requirement could retain the partial group or pad it with a specified value; those are different policies, not interchangeable fixes.

<a id="p3-practice-chunks"></a>
### Practice: chunks

**Question 1.** Find the maximum of each group of two values in [-8, -3, -9, -4, -7]. Retain the partial group. Expected: [-3, -4, -7].

**Solution.** Initialize each maximum to the first actual element, not zero, because all values may be negative. Start comparisons at the following position.

```javascript
const values = [-8, -3, -9, -4, -7];
const result = [];
const size = 2;
for (let start = 0; start < values.length; start += size) {
  let max = values[start];
  for (let i = start + 1; i < start + size && i < values.length; i++) {
    if (values[i] > max) max = values[i];
  }
  result.push(max);
}
console.log(result.join(','));
```

Expected output: `-3,-4,-7`. For the final chunk, max starts at -7 and the inner loop never runs. The singleton maximum is therefore correct.

**Question 2.** Pad the final group of [1, 2, 3, 4, 5] to size three using zeros. Expected groups: [1, 2, 3] and [4, 5, 0].

**Solution.** Run the inner loop exactly size times. Calculate the source index as start + offset. Append a real value only if the source index is valid; otherwise append zero.

```javascript
const values = [1, 2, 3, 4, 5];
const size = 3;
for (let start = 0; start < values.length; start += size) {
  const chunk = [];
  for (let offset = 0; offset < size; offset++) {
    const index = start + offset;
    if (index < values.length) chunk.push(values[index]);
    else chunk.push(0);
  }
  console.log(chunk.join(','));
}
```

Expected output:

```text
1,2,3
4,5,0
```

At start three and offset two, index equals five, which is outside the array. The padding branch executes. Padding is appropriate only when the requirements explicitly permit synthetic values.

<a id="p3-integrated-practice"></a>
## Integrated practice

<a id="p3-circular-batches"></a>
### Circular batches

Process eight readings from a repeating sequence [2, 5, 7], starting at index one, in batches of three. Report the sum of each batch, including the partial final batch. The visited sequence is 5, 7, 2, 5, 7, 2, 5, 7. Expected totals: 14, 14, 12.

The circular position and the batch position serve different purposes. Use a global step number to calculate the source index, and use chunk boundaries to reset the sum. The total number of steps is eight, not the length of the source array.

```javascript
const values = [2, 5, 7];
const offset = 1;
const total = 8;
const size = 3;
for (let start = 0; start < total; start += size) {
  let sum = 0;
  for (let step = start; step < start + size && step < total; step++) {
    const index = (offset + step) % values.length;
    sum += values[index];
  }
  console.log(sum);
}
```

Expected output:

```text
14
14
12
```

At step five, offset + step equals six. Six % three equals zero, so the selected reading is two. At step seven the selected reading is seven. The final batch stops when step reaches eight. The source must be nonempty, total a nonnegative integer, and size a positive integer.

<a id="p3-detect-changes-within-batches"></a>
### Detect changes within batches

Given [3, 5, 2, 8, 9, 4, 7], count increases within each group of three. Do not compare across group boundaries. Expected counts: 1, 1, 0.

```javascript
const values = [3, 5, 2, 8, 9, 4, 7];
const size = 3;
for (let start = 0; start < values.length; start += size) {
  let increases = 0;
  for (let i = start + 1; i < start + size && i < values.length; i++) {
    if (values[i] > values[i - 1]) increases++;
  }
  console.log(increases);
}
```

Expected output:

```text
1
1
0
```

The inner loop begins at start + 1 rather than start. At the second group, the first comparison is 9 versus 8, not 8 versus 2. The final singleton contains no adjacent pair. This combines chunk boundaries with adjacent comparison without requiring intermediate arrays.

<a id="p3-summary"></a>
## Summary

Consecutive processing uses relationships between neighboring elements and may track run boundaries. Circular iteration normalizes positions into a repeating valid index range and needs an independent termination condition. Chunk processing advances by group size and requires an explicit policy for incomplete groups. For each technique, derive the valid index range, identify the state that persists or resets, and test empty, singleton and boundary inputs.

[Previous](#part-2) | [Next (planned)](#part-4)


<a id="part-4"></a>

<a id="p4-lesson-21---part-4-reducing-the-search-space-and-binary-search"></a>
# Lesson 21 - Part 4: Reducing the Search Space and Binary Search

[Previous](#part-3) | [Curriculum](README.md) | [Next (planned)](#part-5)

<a id="p4-learning-objectives"></a>
## Learning objectives

Derive safe search boundaries, eliminate candidates using mathematical properties, implement binary search, find duplicate boundaries and insertion positions, and verify termination and correctness.

<a id="p4-table-of-contents"></a>
## Table of contents

- [1. Reducing the search space](#p4-1-reducing-the-search-space)
- [2. Binary search](#p4-2-binary-search)
- [3. Boundary searches](#p4-3-boundary-searches)
- [4. Searching an answer range](#p4-4-searching-an-answer-range)
- [5. Integrated exercises](#p4-5-integrated-exercises)
- [6. Summary](#p4-6-summary)

<a id="p4-1-reducing-the-search-space"></a>
## 1. Reducing the search space

A search space is the collection of candidates that might satisfy a problem. Searching does not necessarily require examining every candidate. If we can prove that some candidates cannot satisfy the requirements, we can exclude them without testing them individually.

<a id="p4-factor-pairs"></a>
### Factor pairs

Find all positive integer factors of 36. A straightforward loop tests every integer from 1 through 36. But factors occur in pairs: 1 and 36, 2 and 18, 3 and 12, 4 and 9, 6 and 6. At least one member of each pair must be at most the square root of 36. Otherwise their product would exceed 36.

Instead of calculating a square root, we can continue while `d * d <= n`. This condition means that the candidate squared does not exceed the input. When a divisor is found, `n / d` gives its partner.

```javascript
const n = 36;
const factors = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    factors.push(d);
    const partner = n / d;
    if (partner !== d) factors.push(partner);
  }
}
console.log(factors.join(','));
```

Expected: `1,36,2,18,3,12,4,9,6`

Here `n % d` calculates the remainder. A zero remainder establishes divisibility. At d = 4, 36 % 4 is zero and 36 / 4 is 9. At d = 6, the partner is also 6, so we avoid appending it twice. The output is in discovery order, not sorted order.

For sorted output, store the small factors and large partners separately. Small factors arrive in increasing order, whereas partners arrive in decreasing order. Traverse the latter backward.

```javascript
const n = 36;
const small = [], large = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    small.push(d);
    if (d !== n / d) large.push(n / d);
  }
}
for (let i = large.length - 1; i >= 0; i--) small.push(large[i]);
console.log(small.join(','));
```

Expected: `1,2,3,4,6,9,12,18,36`

<a id="p4-prime-testing"></a>
### Prime testing

A prime is an integer greater than one with no positive divisors other than one and itself. If a composite integer has a nontrivial factor, it has one at or below its square root. We therefore need not test larger candidates. After checking divisibility by two, we can skip even candidates: any even divisor would imply divisibility by two.

```javascript
const n = 97;
let prime = n >= 2;
if (n > 2 && n % 2 === 0) prime = false;
if (prime) {
  for (let d = 3; d * d <= n; d += 2) {
    if (n % d === 0) {
      prime = false;
      break;
    }
  }
}
console.log(prime);
```

Expected: `true`

This example assumes integer input. For 97, the tested odd candidates are 3, 5, 7 and 9. The next candidate, 11, has square 121, so the loop ends. For 49, the candidate 7 establishes that it is composite. Using a strict `<` boundary would miss that factor. For 2 the even-number rejection does not apply and the loop is empty. For 1 the initial flag is false.

<a id="p4-practice-1"></a>
### Practice 1

Find the positive factors of 25 without testing candidates above its square root. Explain why 5 appears only once.

<a id="p4-worked-answer"></a>
#### Worked answer

```javascript
const n = 25;
const factors = [];
for (let d = 1; d * d <= n; d++) {
  if (n % d === 0) {
    factors.push(d);
    if (n / d !== d) factors.push(n / d);
  }
}
console.log(factors.join(','));
```

Expected: `1,25,5`

At d = 1 the partner is 25. Candidates 2, 3 and 4 leave nonzero remainders. At d = 5 the partner is 25 / 5 = 5. The equality guard prevents duplicate insertion. The next candidate is 6, whose square exceeds 25.

<a id="p4-2-binary-search"></a>
## 2. Binary search

<a id="p4-from-sequential-search-to-elimination"></a>
### From sequential search to elimination

Suppose the sorted array is [2, 5, 8, 12, 16, 23, 38] and the target is 23. Sequential search checks elements from the beginning. Sorted order permits a stronger decision: if the middle element is 12, all elements to its left are at most 12. None can equal 23. We can discard the entire left portion including the middle element.

Binary search repeatedly examines a middle candidate and discards the half that cannot contain the target. It requires sorted input under the same comparison rule used by the search.

<a id="p4-deriving-the-midpoint"></a>
### Deriving the midpoint

Maintain an inclusive candidate range from `left` to `right`. Initially left is zero and right is length minus one. For indexes 0 through 6, the middle index is 3. For indexes 4 through 6, it is 5.

```javascript
const left = 4, right = 7;
const mid = left + Math.trunc((right - left) / 2);
console.log(mid);
```

Expected: `5`

The subtraction 7 - 4 gives the distance 3. Division gives 1.5. Math.trunc removes the fractional part, giving 1. Adding the left boundary gives 5. Math.trunc returns a number with its fractional part removed; for nonnegative distances it rounds downward. We need an integer index. Using only the distance divided by two would incorrectly measure from zero instead of the current left boundary.

<a id="p4-implementing-exact-search"></a>
### Implementing exact search

```javascript
const values = [2, 5, 8, 12, 16, 23, 38];
const target = 23;
let left = 0, right = values.length - 1;
let found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) {
    found = mid;
    break;
  }
  if (values[mid] < target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `5`

| left | right | mid | value | decision |
|---:|---:|---:|---:|---|
| 0 | 6 | 3 | 12 | left = 4 |
| 4 | 6 | 5 | 23 | found |

The middle element has already been tested. If it is too small, `mid + 1` is the first remaining possible position. If too large, `mid - 1` is the last remaining possible position. The candidate interval strictly shrinks on every unsuccessful comparison.

The condition includes equality because a range with one candidate still needs testing. An empty array starts with right = -1 and never enters the loop. A missing target eventually produces left > right, leaving found at -1.

<a id="p4-incorrect-updates-and-termination"></a>
### Incorrect updates and termination

If left = 4 and right = 5, the midpoint is 4. If its value is too small, setting `left = mid` leaves left at 4. The next iteration repeats the same comparison forever. Use `left = mid + 1` in this inclusive exact-search formulation. Similarly, `right = mid - 1` excludes a tested candidate on the other side.

The invariant (a property maintained throughout the loop) is that if the target exists and has not been found, at least one matching index remains within the candidate interval. Sorted order justifies both exclusions. When the interval is empty, no match remains possible.

<a id="p4-descending-order"></a>
### Descending order

For descending arrays, smaller values occur to the right. The movement rules must change accordingly.

```javascript
const values = [38, 23, 16, 12, 8, 5, 2];
const target = 5;
let left = 0, right = values.length - 1, found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) { found = mid; break; }
  if (values[mid] > target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `5`

At midpoint 3, the value 12 is greater than 5. In descending order the target must be to the right, so left becomes 4.

<a id="p4-practice-2"></a>
### Practice 2

Trace an unsuccessful search for 9 in [2, 4, 6, 8, 10].

<a id="p4-worked-answer-1"></a>
#### Worked answer

| left | right | mid | value | update |
|---:|---:|---:|---:|---|
| 0 | 4 | 2 | 6 | left = 3 |
| 3 | 4 | 3 | 8 | left = 4 |
| 4 | 4 | 4 | 10 | right = 3 |

Now 4 <= 3 is false. The result is -1. Notice why the last single-element interval must be tested.

<a id="p4-3-boundary-searches"></a>
## 3. Boundary searches

Exact binary search returns an arbitrary matching position when duplicates exist. To find the first or last occurrence, we must continue after discovering a match.

<a id="p4-first-occurrence"></a>
### First occurrence

```javascript
const values = [1, 3, 3, 3, 7, 9];
const target = 3;
let left = 0, right = values.length - 1, found = -1;
while (left <= right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] === target) {
    found = mid;
    right = mid - 1;
  } else if (values[mid] < target) left = mid + 1;
  else right = mid - 1;
}
console.log(found);
```

Expected: `1`

The first midpoint is 2, a match. Save it before discarding that position and searching earlier indexes. The next midpoint is 0, whose value is 1, so left becomes 1. The final midpoint is 1, a match. Save it and reduce right to zero. The saved answer is the earliest match. For the last occurrence, save each match and move left to mid + 1 instead.

<a id="p4-lower-bound-first-value-greater-than-or-equal-to-the-target"></a>
### Lower bound: first value greater than or equal to the target

Sometimes we need a position even when the target is absent. Inserting 4 into [1, 3, 3, 7] should place it at index 3. The lower bound is the first index whose value is at least the target, or the array length if no such value exists.

Use a half-open interval [left, right), with right initially equal to the length. Here right can also represent the insertion boundary at the end. When a middle value is too small, exclude it. Otherwise it might be the first qualifying value, so preserve its index as the right boundary.

```javascript
const values = [1, 3, 3, 7];
const target = 4;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `3`

| left | right | mid | value | update |
|---:|---:|---:|---:|---|
| 0 | 4 | 2 | 3 | left = 3 |
| 3 | 4 | 3 | 7 | right = 3 |

The loop ends when the boundaries meet. Every index before left has been established as too small; every index at or after right within the array qualifies. Unlike inclusive exact search, `right = mid` is correct here because the midpoint may be the answer. The strict loop condition ensures that mid is always smaller than right, so this update makes progress.

<a id="p4-upper-bound-and-counting-duplicates"></a>
### Upper bound and counting duplicates

The upper bound is the first index whose value is strictly greater than the target. Change the lower-bound comparison from `<` to `<=`. The difference between upper and lower bounds counts occurrences, including zero when absent.

```javascript
const values = [1, 3, 3, 3, 7, 9];
const target = 3;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
const lower = left;
left = 0;
right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] <= target) left = mid + 1;
  else right = mid;
}
const upper = left;
console.log(lower, upper, upper - lower);
```

Expected: `1 4 3`

The matching interval is [1, 4), containing indexes 1, 2 and 3. Its length is 4 - 1 = 3. To retrieve the last matching index, use upper - 1 only if upper > lower. Otherwise no matching index exists.

<a id="p4-practice-3"></a>
### Practice 3

For [2, 4, 4, 4, 8], determine lower and upper bounds for targets 1, 4, 5 and 10.

<a id="p4-worked-answer-2"></a>
#### Worked answer

| Target | Lower | Upper | Count |
|---:|---:|---:|---:|
| 1 | 0 | 0 | 0 |
| 4 | 1 | 4 | 3 |
| 5 | 4 | 4 | 0 |
| 10 | 5 | 5 | 0 |

For target 4, lower-bound search first examines index 2, which qualifies, and sets right to 2. Index 1 qualifies, so right becomes 1. Index 0 is too small, so left becomes 1. Upper-bound search excludes all equal values and stops at index 4. For target 10 every element is too small, so both boundaries reach the array length.

<a id="p4-4-searching-an-answer-range"></a>
## 4. Searching an answer range

Binary search can operate on an ordered range of possible answers without constructing an array. The requirement is a monotonic predicate: a condition whose truth changes in only one direction across the ordered candidates.

<a id="p4-smallest-integer-whose-square-reaches-a-threshold"></a>
### Smallest integer whose square reaches a threshold

Find the smallest nonnegative integer whose square is at least 50. Candidates 0 through 7 fail; candidates 8 and above succeed. The condition `mid * mid >= target` therefore has a false region followed by a true region. We can locate the boundary just as in lower-bound search.

```javascript
const target = 50;
let left = 0, right = target;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (mid * mid < target) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `8`

For a nonnegative integer target, the target itself is a sufficient upper bound. At target zero the loop is empty and returns zero. At 50 the first midpoint is 25, whose square qualifies; right becomes 25. The next midpoint is 12, which qualifies; right becomes 12. Midpoint 6 fails, so left becomes 7. Midpoint 9 qualifies, then 8 qualifies, then 7 fails. Both boundaries meet at 8. Its square is 64 and the preceding square is 49, verifying minimality.

These examples assume small, exactly represented integer calculations. JavaScript Number arithmetic can lose integer precision for large values; avoid treating rounded products as exact mathematical comparisons near numerical limits.

<a id="p4-practice-4"></a>
### Practice 4

Find the smallest positive number of boxes needed to hold 53 items when each box holds at most 8 items, using a binary search over possible box counts.

<a id="p4-worked-solution"></a>
#### Worked solution

A candidate count qualifies when count * 8 >= 53. Every larger count also qualifies. Zero boxes fail and 53 boxes suffice. Search for the first qualifying count.

```javascript
const items = 53, capacity = 8;
let left = 0, right = items;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (mid * capacity < items) left = mid + 1;
  else right = mid;
}
console.log(left);
```

Expected: `7`

Six boxes hold 48 items, which is insufficient. Seven hold 56, which is sufficient. Direct arithmetic would be simpler for this particular problem; the exercise demonstrates the boundary-search technique for more complex monotonic requirements.

<a id="p4-5-integrated-exercises"></a>
## 5. Integrated exercises

<a id="p4-exercise-1-count-values-in-an-inclusive-interval"></a>
### Exercise 1: Count values in an inclusive interval

Given [1, 2, 2, 4, 6, 6, 9], count values from 2 through 6 inclusive. Do not traverse every element.

<a id="p4-worked-solution-1"></a>
#### Worked solution

Find the first value >= 2 and the first value > 6. Their index difference is the number of values in the requested interval.

```javascript
const values = [1, 2, 2, 4, 6, 6, 9];
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < 2) left = mid + 1;
  else right = mid;
}
const start = left;
left = 0; right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] <= 6) left = mid + 1;
  else right = mid;
}
console.log(left - start);
```

Expected: `5`

The first search returns 1, the second returns 6. Indexes 1 through 5 qualify. The subtraction 6 - 1 gives 5. This assumes the lower requested value is no greater than the upper requested value; otherwise return zero or reject the range before searching.

<a id="p4-exercise-2-find-the-nearest-sorted-value"></a>
### Exercise 2: Find the nearest sorted value

Given [2, 5, 9, 14], find the value closest to 11, choosing the smaller value on ties.

<a id="p4-worked-solution-2"></a>
#### Worked solution

The lower bound identifies the first value at least 11. Only that value and its predecessor can be closest: every earlier value is no greater than the predecessor and every later value is no smaller than the lower-bound value. Handle missing neighbors before comparing distances.

```javascript
const values = [2, 5, 9, 14];
const target = 11;
let left = 0, right = values.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (values[mid] < target) left = mid + 1;
  else right = mid;
}
let nearest;
if (values.length === 0) nearest = undefined;
else if (left === 0) nearest = values[0];
else if (left === values.length) nearest = values[left - 1];
else if (target - values[left - 1] <= values[left] - target) {
  nearest = values[left - 1];
} else nearest = values[left];
console.log(nearest);
```

Expected: `9`

The lower bound is 3. The predecessor is values[2], or 9. The distances are 11 - 9 = 2 and 14 - 11 = 3. The smaller distance wins. Using <= gives the smaller value priority on ties. For a target below every element, the first element is nearest; above every element, the last is nearest. An empty array has no nearest value.

<a id="p4-6-summary"></a>
## 6. Summary

Candidate elimination must be justified by a property of the problem, not merely by a desire to perform fewer iterations. Factor searches use paired divisors; binary searches use sorted order or a monotonic condition. Inclusive exact search and half-open boundary search use different termination and update rules. Do not mix them. Binary search approximately halves the remaining interval at each step, while sequential search removes one candidate at a time. Sorting unsorted data solely for a single search may cost more than scanning it directly. Later lessons develop formal performance analysis.

[Previous](#part-3) | [Curriculum](README.md) | [Next (planned)](#part-5)


<a id="part-5"></a>

<a id="p5-lesson-21---part-5-sorting-with-loops"></a>
# Lesson 21 - Part 5: Sorting With Loops

[Previous: Binary Search](#part-4) | [Next: Integrated Exercises (planned)](#part-6)

<a id="p5-table-of-contents"></a>
## Table of contents

- [Sorting fundamentals](#p5-sorting-fundamentals)
- [Bubble sort](#p5-bubble-sort)
- [Selection sort](#p5-selection-sort)
- [Insertion sort](#p5-insertion-sort)
- [Comparing algorithms](#p5-comparing-algorithms)
- [Integrated practice](#p5-integrated-practice)

<a id="p5-sorting-fundamentals"></a>
## Sorting fundamentals

Sorting rearranges elements according to an ordering rule. Ascending numerical order means each element is no greater than its successor; descending order reverses that relationship. Sorting must preserve every original occurrence, including duplicates. Merely producing an ordered array is not sufficient if values have been lost.

For example, [4, 1, 3, 1] becomes [1, 1, 3, 4]. The result [1, 3, 4] is ordered but is not a correct sort because one occurrence disappeared.

<a id="p5-checking-order-before-sorting"></a>
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

<a id="p5-exchanges-and-preservation"></a>
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

<a id="p5-records-keys-and-stability"></a>
### Records, keys and stability

A key is the value used to determine order. A student record can be sorted by score without losing its name. A stable sort preserves the original relative order of records whose keys compare equal. For example, A:70, B:60, C:70 becomes B:60, A:70, C:70 in a stable ascending sort. Stability matters when equal-key records already have a meaningful order.

All numerical examples below assume dense arrays of finite numbers, with no changes to length during sorting. JavaScript Number comparisons are not suitable for NaN without a separately defined ordering rule.

<a id="p5-bubble-sort"></a>
## Bubble sort

Imagine moving through [5, 2, 4, 1] from left to right. Compare neighboring elements and exchange them if they are in the wrong order.

| Comparison | Array after comparison |
|---|---|
| 5 and 2 | 2,5,4,1 |
| 5 and 4 | 2,4,5,1 |
| 5 and 1 | 2,4,1,5 |

The largest value has reached its final position. The other values are not necessarily sorted. Another pass is needed.

<a id="p5-deriving-the-nested-loops"></a>
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

<a id="p5-early-termination"></a>
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

<a id="p5-descending-order-and-stable-records"></a>
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

<a id="p5-practice-descending-bubble-sort"></a>
### Practice: descending bubble sort

Sort [2, 7, 1, 7] descending. Preserve both sevens. Count exchanges.

<a id="p5-worked-solution"></a>
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

<a id="p5-selection-sort"></a>
## Selection sort

Bubble sort moves a large value gradually through adjacent exchanges. Another strategy is to locate the smallest remaining value first, then move it directly into its final position.

For [5, 2, 4, 1], search the entire array for its minimum. The minimum is 1 at index 3. Exchange indexes 0 and 3. The array becomes [1, 2, 4, 5]. Index 0 is now final. Repeat the search starting at index 1.

<a id="p5-deriving-the-implementation"></a>
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

<a id="p5-a-less-convenient-input"></a>
### A less convenient input

| Start | Minimum index | Array after exchange |
|---|---|---|
| 0 | 2 | 1,4,6,2 |
| 1 | 3 | 1,2,6,4 |
| 2 | 3 | 1,2,4,6 |

This trace begins with [6,4,1,2]. The sorted prefix grows from the left. Each pass finds the smallest element of the remaining suffix, so the prefix contains the smallest values in their final order.

<a id="p5-why-ordinary-selection-sort-is-not-stable"></a>
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

<a id="p5-practice-sort-only-a-selected-range"></a>
### Practice: sort only a selected range

Sort indexes 1 through 4 of [99, 8, 3, 6, 2, 88] ascending, leaving the other positions unchanged.

<a id="p5-worked-solution-1"></a>
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

<a id="p5-insertion-sort"></a>
## Insertion sort

Insertion sort builds a sorted prefix by inserting one new element at a time. Imagine arranging playing cards in your hand: the cards already held are ordered, and each new card is inserted into the appropriate position.

Start with [5,2,4,1]. The first element alone is sorted. Insert 2 before 5, producing [2,5,4,1]. Next insert 4 between 2 and 5. Finally insert 1 before every element in the prefix.

<a id="p5-creating-room-without-losing-values"></a>
### Creating room without losing values

Suppose the sorted prefix is [2,5] and the next value is 4. Save 4 in a variable before shifting 5 right. The array temporarily contains [2,5,5,1]. Place the saved 4 into index 1. The temporary duplicate is harmless because the displaced value has been preserved.

<a id="p5-deriving-the-loop"></a>
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

<a id="p5-common-mistakes"></a>
### Common mistakes

Using a[j] = a[j + 1] shifts in the wrong direction and destroys a prefix value. Forgetting to save key means a[i] may be overwritten before insertion. Using a[j] = key after the while loop places the key one position too far left; j identifies the last value that did not need shifting, or -1 if every value shifted.

Using >= instead of > shifts equal values and can reverse equal-key records. Strict > preserves stability.

<a id="p5-inserting-into-an-already-sorted-array"></a>
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

<a id="p5-practice-stable-descending-insertion-sort"></a>
### Practice: stable descending insertion sort

Sort records A:50, B:80, C:50, D:90 by descending score. Preserve the order of equal scores.

<a id="p5-worked-solution-2"></a>
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

<a id="p5-comparing-algorithms"></a>
## Comparing algorithms

For n elements, ordinary bubble sort and selection sort perform (n - 1) + (n - 2) + ... + 1 comparisons, which equals n * (n - 1) / 2. For four elements this is 3 + 2 + 1 = 6. Insertion sort performs a similar number of key comparisons in reverse-sorted input, but on already sorted input it needs only one key comparison per insertion.

| Algorithm | Already sorted input | Reverse sorted input | Stable ordinary version | Movement |
|---|---|---|---|---|
| Bubble with early exit | n - 1 comparisons | n(n-1)/2 comparisons | Yes | Adjacent exchanges |
| Selection | n(n-1)/2 comparisons | n(n-1)/2 comparisons | No | At most n - 1 exchanges |
| Insertion | n - 1 key comparisons | n(n-1)/2 key comparisons | Yes | Shifts and insertions |

All three ordinary implementations use a fixed number of extra variables, independent of array length. Their worst-case comparison counts grow quadratically: doubling a large input approximately quadruples the work. Formal complexity analysis is developed later in the course.

Selection sort can be useful when exchanges are expensive. Insertion sort is particularly useful for small or nearly sorted collections. Bubble sort is valuable for understanding adjacent comparisons, but is rarely the best general-purpose choice.

<a id="p5-integrated-practice"></a>
## Integrated practice

<a id="p5-exercise-1-preserve-the-original-array"></a>
### Exercise 1: preserve the original array

Sort [4,1,4,2] ascending using insertion sort without modifying the original array.

<a id="p5-worked-solution-3"></a>
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

<a id="p5-exercise-2-select-the-three-smallest-values"></a>
### Exercise 2: select the three smallest values

Given [8,3,7,1,5,2], obtain the three smallest values in ascending order without sorting the entire array.

<a id="p5-worked-solution-4"></a>
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

<a id="p5-exercise-3-diagnose-a-boundary-error"></a>
### Exercise 3: diagnose a boundary error

A bubble sort uses j <= end rather than j < end. Explain the problem and correct it.

<a id="p5-worked-answer"></a>
#### Worked answer

When j equals end, j + 1 equals end + 1. On the first pass this is outside the array, and a[j + 1] is undefined. For finite numbers, comparison with undefined is false, so the extra comparison does not help. On subsequent passes it compares with the already sorted suffix, which is unnecessary. Use j < end to ensure both indexes belong to the unsorted region. The outer loop should use end > 0 because a single remaining position needs no comparison.

<a id="p5-exercise-4-verify-edge-cases"></a>
### Exercise 4: verify edge cases

Predict the results of sorting [], [7], [2,2], [-1,-5,0], and [3,2,1] ascending. Explain why no special handling is needed.

<a id="p5-worked-answer-1"></a>
#### Worked answer

The results are [], [7], [2,2], [-5,-1,0], and [1,2,3]. For empty and singleton arrays the outer conditions are false immediately. Strict comparisons preserve equal values without unnecessary movement in bubble and insertion sort. Negative numbers obey the same numerical ordering rules. Reverse order requires the most shifting in insertion sort, but every inner iteration decreases j, ensuring termination.

<a id="p5-summary"></a>
## Summary

Sorting requires both ordered output and preservation of original elements. Bubble sort establishes a sorted suffix, selection sort establishes a final sorted prefix by selecting minima, and insertion sort extends a sorted prefix by shifting elements. Derive boundaries from the region being processed, preserve values before overwriting them, and choose comparisons according to the required order and stability.

[Previous: Binary Search](#part-4) | [Next: Integrated Exercises (planned)](#part-6)


<a id="part-6"></a>

<a id="p6-lesson-21---part-6-comprehensive-integrated-exercises"></a>
# Lesson 21 - Part 6: Comprehensive Integrated Exercises

[Previous](#part-5) | [Complete lesson](21-advanced-loop-techniques.md) | Next lesson: Converting and Refactoring Loops (planned)

<a id="p6-introduction"></a>
## Introduction

Attempt each problem before reading its worked solution. Use traditional loops, dense arrays and small finite integer values unless otherwise stated. Verify the output, then explain why the loop terminates and why its updates preserve correctness.

<a id="p6-contents"></a>
## Contents

- [1. Pair search after sorting](#p6-1-pair-search-after-sorting)
- [2. Best circular three-day total](#p6-2-best-circular-three-day-total)
- [3. Range totals after sorting](#p6-3-range-totals-after-sorting)
- [4. Compact then group into batches](#p6-4-compact-then-group-into-batches)
- [5. Merge and count runs](#p6-5-merge-and-count-runs)
- [6. Longest affordable consecutive range](#p6-6-longest-affordable-consecutive-range)
- [7. Balance position with prefix and suffix reasoning](#p6-7-balance-position-with-prefix-and-suffix-reasoning)
- [8. Stable sorting and top records](#p6-8-stable-sorting-and-top-records)

<a id="p6-1-pair-search-after-sorting"></a>
## 1. Pair search after sorting

<a id="p6-problem"></a>
### Problem

Given [8, 1, 6, 3, 4], find two distinct elements totaling 10. Preserve the original array. Return their values, not original indexes.

Expected output:

```text
4 6
8,1,6,3,4
```

<a id="p6-worked-solution"></a>
### Worked solution

Copy the input, then use insertion sort to establish the ordering required by two-pointer search. The sorted copy is [1, 3, 4, 6, 8]. Start at its extremes. A sum below the target eliminates the left candidate; a sum above it eliminates the right candidate.

```javascript
const input = [8, 1, 6, 3, 4];
const a = [];
for (let i = 0; i < input.length; i++) a.push(input[i]);
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
let left = 0;
let right = a.length - 1;
let found = false;
while (left < right) {
  const sum = a[left] + a[right];
  if (sum === 10) {
    console.log(a[left], a[right]);
    found = true;
    break;
  }
  if (sum < 10) left++;
  else right--;
}
if (!found) console.log("No pair");
console.log(input.join(","));
```

<a id="p6-explanation-and-correctness"></a>
### Explanation and correctness

The sums are 9, 11, 9, and 10. In insertion sort, j + 1 is the vacant position after shifting larger values. The copy preserves the original input. Empty and singleton arrays cannot form pairs. Sorting adds work; for one query a nested search may be simpler.

<a id="p6-2-best-circular-three-day-total"></a>
## 2. Best circular three-day total

<a id="p6-problem-1"></a>
### Problem

For daily values [8, 1, 2, 7, 6], find the largest total of three consecutive days, allowing wraparound. Report the earliest starting index on ties.

Expected output:

```text
3 21
```

<a id="p6-worked-solution-1"></a>
### Worked solution

Build the first window once. For each new start, subtract the outgoing value and add the incoming value. Use remainder to wrap the incoming index.

```javascript
const a = [8, 1, 2, 7, 6];
const k = 3;
let sum = 0;
for (let i = 0; i < k; i++) sum += a[i];
let best = sum;
let start = 0;
for (let i = 1; i < a.length; i++) {
  sum -= a[i - 1];
  sum += a[(i + k - 1) % a.length];
  if (sum > best) {
    best = sum;
    start = i;
  }
}
console.log(start, best);
```

<a id="p6-explanation-and-correctness-1"></a>
### Explanation and correctness

The totals are 11, 10, 15, 21, 15. At start 3, the incoming index is (3 + 3 - 1) % 5 = 0, so 8 enters. Strict > preserves the earliest tie. Require a nonempty array and 1 <= k <= length; initialize best from the first window, not zero, to handle negative totals.

<a id="p6-3-range-totals-after-sorting"></a>
## 3. Range totals after sorting

<a id="p6-problem-2"></a>
### Problem

Sort [9, 2, 5, 2, 7] and calculate the sum of all values between 2 and 5 inclusive using binary boundaries and a prefix array.

Expected output:

```text
3 9
```

<a id="p6-worked-solution-2"></a>
### Worked solution

Sort first. The lower boundary is the first index with value >= 2; the upper boundary is the first index with value > 5. Both searches use a half-open interval. Build prefix totals with an initial zero.

```javascript
const a = [9, 2, 5, 2, 7];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j] > key) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
const prefix = [0];
for (let i = 0; i < a.length; i++) prefix.push(prefix[i] + a[i]);
let left = 0;
let right = a.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (a[mid] < 2) left = mid + 1;
  else right = mid;
}
const start = left;
left = 0;
right = a.length;
while (left < right) {
  const mid = left + Math.trunc((right - left) / 2);
  if (a[mid] <= 5) left = mid + 1;
  else right = mid;
}
console.log(left - start, prefix[left] - prefix[start]);
```

<a id="p6-explanation-and-correctness-2"></a>
### Explanation and correctness

Sorted values are [2,2,5,7,9]. Prefix totals are [0,2,4,9,16,25]. Boundaries are 0 and 3. Subtracting prefix[0] from prefix[3] gives 9. The upper search uses <= to include every copy of the upper endpoint. If no values qualify, equal boundaries give zero. Require lower endpoint <= upper endpoint.

<a id="p6-4-compact-then-group-into-batches"></a>
## 4. Compact then group into batches

<a id="p6-problem-3"></a>
### Problem

Remove negative values from [4,-1,2,7,-3,5,8] in place, then report totals for consecutive batches of two, retaining the incomplete final batch.

Expected output:

```text
6
12
8
```

<a id="p6-worked-solution-3"></a>
### Worked solution

Compaction keeps a write pointer behind or equal to the read pointer. After truncation, traverse the retained values in steps of two. Bound the inner loop by both the batch end and the array length.

```javascript
const a = [4,-1,2,7,-3,5,8];
let write = 0;
for (let read = 0; read < a.length; read++) {
  if (a[read] >= 0) {
    a[write] = a[read];
    write++;
  }
}
a.length = write;
for (let start = 0; start < a.length; start += 2) {
  let sum = 0;
  for (let i = start; i < start + 2 && i < a.length; i++) sum += a[i];
  console.log(sum);
}
```

<a id="p6-explanation-and-correctness-3"></a>
### Explanation and correctness

The retained sequence is [4,2,7,5,8]. Batch starts are 0, 2, 4. At start 4 the inner condition stops at index 5 rather than reading undefined. If all values are rejected, no batches are printed. The accumulator resets once per batch, not once per element.

<a id="p6-5-merge-and-count-runs"></a>
## 5. Merge and count runs

<a id="p6-problem-4"></a>
### Problem

Merge [1,2,2,5] and [2,3,5,5], then report each value and its total frequency.

Expected output:

```text
1 1
2 3
3 1
5 3
```

<a id="p6-worked-solution-4"></a>
### Worked solution

Use sorted merging to make equal values adjacent. Then process consecutive runs, keeping a current value and count. Flush the final run after traversal.

```javascript
const a = [1,2,2,5];
const b = [2,3,5,5];
const merged = [];
let i = 0;
let j = 0;
while (i < a.length || j < b.length) {
  if (i < a.length && (j >= b.length || a[i] <= b[j])) {
    merged.push(a[i]);
    i++;
  } else {
    merged.push(b[j]);
    j++;
  }
}
if (merged.length > 0) {
  let value = merged[0];
  let count = 1;
  for (let k = 1; k < merged.length; k++) {
    if (merged[k] === value) count++;
    else {
      console.log(value, count);
      value = merged[k];
      count = 1;
    }
  }
  console.log(value, count);
}
```

<a id="p6-explanation-and-correctness-4"></a>
### Explanation and correctness

The merged sequence is [1,2,2,2,3,5,5,5]. At index 4, the value changes from 2 to 3, so the count of 3 occurrences of 2 is printed before resetting. The final run has no following different value to trigger printing, hence the final console.log. Empty inputs are guarded.

<a id="p6-6-longest-affordable-consecutive-range"></a>
## 6. Longest affordable consecutive range

<a id="p6-problem-5"></a>
### Problem

Given nonnegative costs [2,4,1,3,2,1] and budget 6, find the longest consecutive range costing at most 6. Report its starting index and length, keeping the earliest tie.

Expected output:

```text
2 3
```

<a id="p6-worked-solution-5"></a>
### Worked solution

Expand the right boundary one element at a time. While the total exceeds the budget, remove elements from the left. Nonnegative costs ensure that adding elements cannot reduce the total.

```javascript
const a = [2,4,1,3,2,1];
let left = 0;
let sum = 0;
let bestLength = 0;
let bestStart = -1;
for (let right = 0; right < a.length; right++) {
  sum += a[right];
  while (sum > 6) {
    sum -= a[left];
    left++;
  }
  const length = right - left + 1;
  if (length > bestLength) {
    bestLength = length;
    bestStart = left;
  }
}
console.log(bestStart, bestLength);
```

<a id="p6-explanation-and-correctness-5"></a>
### Explanation and correctness

At right 2 the total is 7; remove 2 to obtain 5. At right 3 the total is 8; remove 4 to obtain 4. At right 4 the total is 6, covering indexes 2 through 4. Length is 4 - 2 + 1 = 3. At right 5 another length-three range occurs, but strict > retains the earlier range. Negative costs invalidate the elimination argument; require a nonnegative budget.

<a id="p6-7-balance-position-with-prefix-and-suffix-reasoning"></a>
## 7. Balance position with prefix and suffix reasoning

<a id="p6-problem-6"></a>
### Problem

For [2,1,3,1,2], find every index where the sum strictly before it equals the sum strictly after it.

Expected output:

```text
2
```

<a id="p6-worked-solution-6"></a>
### Worked solution

Calculate the total once. Maintain the sum before the current index. The sum after it equals total minus the preceding sum minus the current value.

```javascript
const a = [2,1,3,1,2];
let total = 0;
for (let i = 0; i < a.length; i++) total += a[i];
let before = 0;
for (let i = 0; i < a.length; i++) {
  const after = total - before - a[i];
  if (before === after) console.log(i);
  before += a[i];
}
```

<a id="p6-explanation-and-correctness-6"></a>
### Explanation and correctness

At index 2, total is 9, before is 3, and after is 9 - 3 - 3 = 3. Update before only after comparison, otherwise the current element is counted twice. A singleton balances at index zero. An empty array has no balance index. Negative integers are permitted.

<a id="p6-8-stable-sorting-and-top-records"></a>
## 8. Stable sorting and top records

<a id="p6-problem-7"></a>
### Problem

Sort [{name:"A",score:7},{name:"B",score:9},{name:"C",score:9},{name:"D",score:5}] by descending score, preserving input order for equal scores. Print the top two names.

Expected output:

```text
B
C
```

<a id="p6-worked-solution-7"></a>
### Worked solution

Insertion sort shifts only strictly smaller scores. Equal scores must not be shifted past one another. After sorting, traverse at most two positions.

```javascript
const a = [{name:"A",score:7},{name:"B",score:9},{name:"C",score:9},{name:"D",score:5}];
for (let i = 1; i < a.length; i++) {
  const key = a[i];
  let j = i - 1;
  while (j >= 0 && a[j].score < key.score) {
    a[j + 1] = a[j];
    j--;
  }
  a[j + 1] = key;
}
for (let i = 0; i < 2 && i < a.length; i++) console.log(a[i].name);
```

<a id="p6-explanation-and-correctness-7"></a>
### Explanation and correctness

When C is inserted, A shifts right because 7 < 9. B does not shift because 9 < 9 is false. C is placed after B. Using <= instead would reverse equal-score records. The final loop also works for fewer than two records.

<a id="p6-final-review"></a>
## Final review

Choose a technique by identifying the property that makes its updates safe: sorted order, overlapping ranges, nonnegative costs, consecutive equal values, or a bounded index range. Do not apply a technique merely because its loop resembles a previous example.

[Previous](#part-5) | [Complete lesson](21-advanced-loop-techniques.md)
