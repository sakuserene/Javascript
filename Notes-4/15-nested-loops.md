# Lesson 15 - Nested Loops

Previous: [Lesson 14](14-infinite-loops-and-loop-termination.md) | Next: [Lesson 16 (planned)](16-working-with-numbers.md)

## Contents

1. [Nested loop basics](#1-nested-loop-basics)
2. [Independent nested loops](#2-independent-nested-loops)
3. [Dependent nested loops](#3-dependent-nested-loops)
4. [Combining different loop types](#4-combining-different-loop-types)
5. [Managing state](#5-managing-state)
6. [Break and continue](#6-break-and-continue)
7. [Working with pairs](#7-working-with-pairs)
8. [Multidimensional iteration](#8-multidimensional-iteration)
9. [Summary](#9-summary)
10. [Integrated exercises](#10-integrated-exercises)

## 1. Nested loop basics

A nested loop is a loop inside another loop. The containing loop is the outer loop; the contained loop is the inner loop. The inner loop starts anew on every outer iteration.

Suppose we need three rounds, each containing two numbered operations. The round number must remain unchanged until both operations finish.

```javascript
for (let round = 1; round <= 3; round++) {
  for (let number = 1; number <= 2; number++) {
    console.log(round, number);
  }
}
```

Output:

```text
1 1
1 2
2 1
2 2
3 1
3 2
```

The outer initialization executes once. The inner initialization executes three times. The inner body executes six times. After number becomes 3, the inner condition fails, and the outer update advances round.

Statements before and after the inner loop execute once per outer iteration.

```javascript
for (let round = 1; round <= 2; round++) {
  console.log("Starting round:", round);
  for (let number = 1; number <= 3; number++) {
    console.log(number);
  }
  console.log("Finished round:", round);
}
```

Output:

```text
Starting round: 1
1
2
3
Finished round: 1
Starting round: 2
1
2
3
Finished round: 2
```

### Practice

Print three groups, each containing the numbers 4, 5, 6. Print the group number before its numbers. Predict how often each initialization and body executes. Repeat with zero groups and with zero numbers per group.

## 2. Independent nested loops

When the inner range does not depend on the outer variable, the loops have independent ranges. For example, a shop offers two colors and three sizes. Generate every color-size combination.

```javascript
const colors = ["red", "blue"];
const sizes = ["S", "M", "L"];
for (let i = 0; i < colors.length; i++) {
  for (let j = 0; j < sizes.length; j++) {
    console.log(colors[i], sizes[j]);
  }
}
```

Output:

```text
red S
red M
red L
blue S
blue M
blue L
```

For each color, all three sizes are visited. The complete set of combinations is called a Cartesian product. Two outer iterations times three inner iterations gives six combinations. If either array is empty, no combination is printed.

Changing loop order groups the same combinations differently: put the size loop outside and the color loop inside to obtain red S, blue S, red M, blue M, red L, blue L. Order matters when the output must be grouped by color or size.

### Building a rectangular pattern

Print three rows of four stars. A console.log call prints a separate line, so printing one star per inner iteration would produce twelve lines. Instead build a string for each row and print it after the inner loop.

```javascript
for (let row = 1; row <= 3; row++) {
  let line = "";
  for (let column = 1; column <= 4; column++) {
    line += "*";
  }
  console.log(line);
}
```

Output:

```text
****
****
****
```

The string grows from empty to four stars in each row. Declaring line outside the outer loop without resetting it would produce lines of lengths 4, 8, and 12.

### Practice

Generate every combination of ["tea", "coffee"] and ["small", "large"]. Expect four combinations. Reverse the grouping order. Then print a rectangle with two rows and five hashes; expect two lines of #####. Test zero rows and zero columns (the latter produces empty lines).

## 3. Dependent nested loops

Sometimes the inner range changes with the outer iteration. Print rows containing one, two, three and four stars. The row number determines the number of stars, so the inner upper boundary is row rather than a fixed constant.

```javascript
for (let row = 1; row <= 4; row++) {
  let line = "";
  for (let column = 1; column <= row; column++) {
    line += "*";
  }
  console.log(line);
}
```

Output:

```text
*
**
***
****
```

The inner body executes 1 + 2 + 3 + 4 = 10 times, not 4 * 4. Count dependent iterations by adding the work performed for each outer iteration.

### Changing the lower boundary

Generate all integer pairs (a, b) from 1 through 4 for which b is greater than a. Instead of examining every pair and rejecting unsuitable ones, start b at a + 1.

```javascript
for (let a = 1; a <= 4; a++) {
  for (let b = a + 1; b <= 4; b++) {
    console.log(a, b);
  }
}
```

Output:

```text
1 2
1 3
1 4
2 3
2 4
3 4
```

When a is 4, b starts at 5 and the inner body executes zero times. An outer iteration does not guarantee an inner iteration.

### Practice

Print rows of 4, 3, 2, 1 stars using a dependent boundary. Generate all pairs 1 <= a <= b <= 3; expect (1,1), (1,2), (1,3), (2,2), (2,3), (3,3). Explain how including equality changes the starting value.

## 4. Combining different loop types

Choose each loop according to its own repetition requirements. Suppose three containers hold 20, 65 and 0 units. Remove at most 30 units per operation until each container is empty. The outer count is known; the inner count depends on the quantity.

```javascript
const containers = [20, 65, 0];
for (let i = 0; i < containers.length; i++) {
  let remaining = containers[i];
  let operations = 0;
  while (remaining > 0) {
    let removal = 30;
    if (remaining < removal) removal = remaining;
    remaining -= removal;
    operations++;
  }
  console.log(i, operations);
}
```

Output:

```text
0 1
1 3
2 0
```

The inner loop uses a temporary copy of the quantity, so the original array is unchanged. Its progress measure is remaining; the outer progress measure is containers.length - i. Both must terminate.

A do...while inner loop would perform an unnecessary operation for the empty container unless guarded. Use it when at least one operation is required. For example, every machine must perform an initial test, then repeat until its required test count is reached.

```javascript
const required = [1, 3, 2];
for (let i = 0; i < required.length; i++) {
  let tests = 0;
  do {
    tests++;
  } while (tests < required[i]);
  console.log(i, tests);
}
```

Output: 0 1, 1 3, 2 2 on separate lines. Assume positive integer requirements.

### Practice

Process containers [0, 45, 60, 61] with a removal limit of 20. Expected operation counts: [0, 3, 3, 4]. Reject a nonpositive removal limit before processing.

## 5. Managing state

The lifetime of a variable determines whether its value belongs to one inner iteration, one group, or the entire traversal.

Suppose each of three days contains two sales, represented by six consecutive values. Calculate each day's total and the grand total.

```javascript
const sales = [10, 20, 5, 15, 30, 40];
let grandTotal = 0;
let index = 0;
for (let day = 1; day <= 3; day++) {
  let dailyTotal = 0;
  for (let sale = 1; sale <= 2; sale++) {
    const amount = sales[index];
    index++;
    dailyTotal += amount;
  }
  grandTotal += dailyTotal;
  console.log(day, dailyTotal);
}
console.log("Grand total:", grandTotal);
```

Output:

```text
1 30
2 20
3 70
Grand total: 120
```

Daily total resets once per day. Grand total and index persist across days. Amount is temporary. Moving dailyTotal outside without resetting it produces cumulative totals 30, 50, 120 and an incorrect grand total of 200. Moving index inside the outer loop repeats the first two sales. Moving dailyTotal inside the inner loop prevents accumulation across sales.

### Finding the best group

Extend the preceding problem to report the day with the largest total. Declare bestDay and bestTotal before the outer loop. After calculating a daily total, compare it with bestTotal. Initialize from the first completed group rather than zero if negative totals are possible.

```javascript
const sales = [-10, -20, -5, -15, -30, -40];
let index = 0;
let bestDay;
let bestTotal;
for (let day = 1; day <= 3; day++) {
  let total = 0;
  for (let sale = 1; sale <= 2; sale++) {
    total += sales[index];
    index++;
  }
  if (day === 1 || total > bestTotal) {
    bestDay = day;
    bestTotal = total;
  }
}
console.log(bestDay, bestTotal);
```

Output: 2 -20. Strict greater-than preserves the first group on ties. For zero groups, report no result instead of printing uninitialized variables.

### Practice

For sales [4, 6, 10, 20, 8, 2], calculate three group totals, grand total and best group. Expect [10, 30, 10], 50, group 2. Test all-negative totals and ties.

## 6. Break and continue

An unlabeled break exits only the innermost enclosing loop. An unlabeled continue advances only that loop.

```javascript
for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 4; col++) {
    if (col === 3) break;
    console.log(row, col);
  }
}
```

Output:

```text
1 1
1 2
2 1
2 2
3 1
3 2
```

Replacing break with continue skips column 3 but prints column 4 in every row.

### Exiting the entire search

Find the first pair from [2, 4, 6] and [1, 3, 5] whose sum is 7. Search in the supplied order. A plain break stops only the inner search, so the outer loop could find more matches.

Recall labels from Lesson 8: a label is an identifier followed by a colon before a statement. A labeled break exits the named statement.

```javascript
const a = [2, 4, 6];
const b = [1, 3, 5];
let found = false;
search: for (let i = 0; i < a.length; i++) {
  for (let j = 0; j < b.length; j++) {
    if (a[i] + b[j] === 7) {
      console.log(a[i], b[j]);
      found = true;
      break search;
    }
  }
}
if (!found) console.log("No pair");
```

Output: 2 5. An alternative is to use an ordinary break and then check found immediately after the inner loop, breaking the outer loop if true. A flag is useful when later code needs the result; a label directly expresses the intended exit.

### Skipping an entire group

Suppose a group is invalid if any of its two values is negative. Print only valid group totals.

```javascript
const values = [2, 3, 4, -1, 5, 6];
 groups: for (let group = 0; group < 3; group++) {
  let total = 0;
  for (let j = 0; j < 2; j++) {
    const value = values[group * 2 + j];
    if (value < 0) continue groups;
    total += value;
  }
  console.log(group + 1, total);
}
```

Output: 1 5 and 3 11 on separate lines. The expression group * 2 + j locates each element: group 0 starts at index 0, group 1 at index 2, group 2 at index 4. Labeled continue advances the outer for loop, bypassing the report for the invalid group. An unlabeled continue would merely skip the negative value and report an incomplete group.

### Practice

Print pairs (row, col) for rows 1..3 and columns 1..3, skipping col 2. Then stop the entire traversal at (2,2), checking this condition before skipping. Expected second output: (1,1), (1,3), (2,1). Find the first pair summing to 9 in [1,4,7] and [2,5]; expect 4 and 5. Test no match.

## 7. Working with pairs

When pairing elements from the same array, decide whether positions may repeat and whether order matters. A pair of positions is distinct from a pair of values: equal values can occur at different positions.

For [4, 7, 9], all ordered pairs including self-pairs number nine. Excluding self-pairs leaves six. Ignoring order and excluding self-pairs leaves three.

```javascript
const values = [4, 7, 9];
for (let i = 0; i < values.length; i++) {
  for (let j = i + 1; j < values.length; j++) {
    console.log(values[i], values[j]);
  }
}
```

Output: 4 7, 4 9, 7 9 on separate lines. Starting j at i + 1 ensures every pair of distinct positions appears exactly once. For n elements the number is n * (n - 1) / 2: each unordered pair corresponds to two ordered pairs, and there are n * (n - 1) ordered pairs of distinct positions.

### Finding all matching pairs

Given [2, 5, 2, 7], report every pair of distinct positions whose values sum to 7. Do not remove duplicate values.

```javascript
const values = [2, 5, 2, 7];
let count = 0;
for (let i = 0; i < values.length; i++) {
  for (let j = i + 1; j < values.length; j++) {
    if (values[i] + values[j] === 7) {
      console.log(i, j);
      count++;
    }
  }
}
console.log("Count:", count);
```

Output:

```text
0 1
1 2
Count: 2
```

Both matches involve the value 2 but different positions. Checking values[i] !== values[j] would incorrectly exclude equal-valued pairs, such as two distinct occurrences of 3 summing to 6.

### Practice

For [3,3,6,0], find all distinct-position unordered pairs summing to 6. Expect (0,1) and (2,3). Test empty and single-element arrays. Then generate ordered pairs excluding self-pairs; expect four matches.

## 8. Multidimensional iteration

A nested array is an array whose elements are themselves arrays. It can represent rows of a table. Access one row with table[row], and one value in that row with table[row][column].

```javascript
const table = [[10, 20], [30, 40]];
console.log(table[1]);
console.log(table[1][0]);
```

Output: [30, 40], then 30. Both indices start at zero. The first index selects a row; the second selects a position within that row.

### Traversing unequal rows

Rows need not have equal lengths. Given [[10,20], [], [30,40,50]], print every value with its coordinates.

```javascript
const table = [[10, 20], [], [30, 40, 50]];
for (let row = 0; row < table.length; row++) {
  for (let col = 0; col < table[row].length; col++) {
    console.log(row, col, table[row][col]);
  }
}
```

Output:

```text
0 0 10
0 1 20
2 0 30
2 1 40
2 2 50
```

Using table.length as the inner boundary confuses the number of rows with the number of elements in a row. Using table[0].length assumes every row has the same length and fails for an empty outer array if evaluated before checking availability. The correct boundary depends on the current row.

### Row totals and column totals

For a rectangular table, row totals process one row at a time. Column totals instead process one column across all rows. Assume a nonempty rectangular table for the column example.

```javascript
const table = [[1, 2, 3], [4, 5, 6]];
const rowTotals = [];
const colTotals = [];
for (let r = 0; r < table.length; r++) {
  let total = 0;
  for (let c = 0; c < table[r].length; c++) total += table[r][c];
  rowTotals.push(total);
}
for (let c = 0; c < table[0].length; c++) {
  let total = 0;
  for (let r = 0; r < table.length; r++) total += table[r][c];
  colTotals.push(total);
}
console.log(rowTotals, colTotals);
```

Output: [6, 15] [5, 7, 9]. Reversing traversal order changes which group is accumulated. For an empty table, return empty totals without accessing table[0]. For unequal rows, define how missing positions should be treated before implementing column totals.

### Constructing a table

Build a multiplication table with two rows and three columns. Each row must be a separate array. Create the row inside the outer loop, append products in the inner loop, then append the completed row to the table.

```javascript
const table = [];
for (let r = 1; r <= 2; r++) {
  const row = [];
  for (let c = 1; c <= 3; c++) {
    row.push(r * c);
  }
  table.push(row);
}
console.log(table);
```

Output: [[1, 2, 3], [2, 4, 6]]. Creating a fresh row each time avoids reusing the same array for multiple rows.

### Three levels of repetition

A warehouse has two floors, two shelves per floor and three positions per shelf. Each position is identified by three numbers.

```javascript
let count = 0;
for (let floor = 1; floor <= 2; floor++) {
  for (let shelf = 1; shelf <= 2; shelf++) {
    for (let position = 1; position <= 3; position++) {
      console.log(floor, shelf, position);
      count++;
    }
  }
}
console.log(count);
```

The first coordinate is 1 1 1, the last is 2 2 3, and the count is 12. Each additional nesting level introduces another range. Use descriptive names so the meaning of each level remains clear.

### Practice

Calculate row totals for [[5,8], [], [2,3,4]]; expect [13,0,9]. Calculate column totals for [[2,4],[6,8],[10,12]]; expect [18,24]. Build a 3 by 2 multiplication table; expect [[1,2],[2,4],[3,6]].

## 9. Summary

Independent ranges multiply their iteration counts; dependent ranges require summing the inner counts. Initialize group state inside the outer loop and global state outside it. Ordinary break and continue affect the nearest loop; labels can target an outer loop. Choose pair boundaries according to order and self-pair requirements. Use the current row length for unequal nested arrays. Establish termination separately for each level.

## 10. Integrated exercises

1. Generate all combinations of two products and three sizes, count them and group by size. Expect six combinations; an empty product list produces zero.
2. Print a triangle of five rows with lengths 1 through 5. Report 15 stars in total.
3. For containers [15,0,45], remove at most 20 units per operation. Report counts [1,0,3] and grand count 4. Reject a zero removal limit.
4. Find the first coordinate of 7 in [[1,2],[],[7,7]]. Expect row 2, column 0. Report no match for an empty table.
5. Find all unordered distinct-position pairs summing to 10 in [5,2,8,5]. Expect (0,3) and (1,2).
6. Given [[10,-1,20],[5,6],[],[7,8]], reject entire rows containing negative values. Report accepted row totals [11,0,15] and grand total 26. Count the empty row as valid.
7. Build a 3 by 3 table whose diagonal values are 1 and other values are 0. Expect [[1,0,0],[0,1,0],[0,0,1]]. The diagonal consists of positions with equal row and column indices.
8. For [[4,8],[3,9],[1,2]], report row totals [12,12,3], column totals [8,19], grand total 27, and first highest-total row index 0.
9. Debug a loop that declares its inner counter outside the outer loop and never resets it. For three groups of two operations, explain why only the first group executes its operations. Correct it and verify six operations.

Solutions are withheld for independent practice.

Previous: [Lesson 14](14-infinite-loops-and-loop-termination.md) | Next: [Lesson 16 (planned)](16-working-with-numbers.md)
