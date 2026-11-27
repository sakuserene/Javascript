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

### Initialization is part of the repeated work

Consider two groups of three operations. If the inner counter is initialized only once, the second group starts with an exhausted counter.

```javascript
let j = 1;
for (let i = 1; i <= 2; i++) {
  while (j <= 3) {
    console.log(i, j);
    j++;
  }
}
```

Output:

```text
1 1
1 2
1 3
```

At the end of group one, j is 4. Group two checks 4 <= 3, which is false. Move initialization inside the outer body:

```javascript
for (let i = 1; i <= 2; i++) {
  let j = 1;
  while (j <= 3) {
    console.log(i, j);
    j++;
  }
}
```

Output:

```text
1 1
1 2
1 3
2 1
2 2
2 3
```

For the corrected version, the outer condition is checked three times, the inner initialization twice, and the inner condition eight times (three successful checks and one failed check per group). The inner update executes six times. The failed checks matter even though they produce no output.

### Practice: tracing and scope

Trace two outer iterations with an inner range of 2 through 4. Record each initialization, successful condition, failed condition and update. Predict the output if the inner counter is initialized outside the outer loop. Then correct it. A correct implementation prints six pairs, beginning with (1,2) and ending with (2,4).

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

### Combining independent ranges with calculations

A shop sells packages of 2 or 5 items, with unit prices of 10, 15 or 20. Calculate every possible package price. Each package size must be combined with every unit price; the product is calculated only after selecting both.

```javascript
const quantities = [2, 5];
const prices = [10, 15, 20];
for (let i = 0; i < quantities.length; i++) {
  for (let j = 0; j < prices.length; j++) {
    console.log(quantities[i], prices[j], quantities[i] * prices[j]);
  }
}
```

Output:

```text
2 10 20
2 15 30
2 20 40
5 10 50
5 15 75
5 20 100
```

If only packages costing at most 50 are wanted, calculate the price inside the inner loop and print conditionally. The loops still examine six combinations, even though only four qualify. The number of outputs is not necessarily the number of iterations.

Practice: implement the price restriction. Expected prices: 20, 30, 40, 50. Test a budget of 19 (no results) and 100 (all results).

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

### Decreasing ranges and two dependent boundaries

Print four rows with lengths 4, 3, 2, 1. At row 1 the length must be 4, and every subsequent row reduces it by one. Thus the length is 4 - row + 1.

```javascript
for (let row = 1; row <= 4; row++) {
  let line = "";
  for (let col = 1; col <= 4 - row + 1; col++) {
    line += "*";
  }
  console.log(line);
}
```

Output:

```text
****
***
**
*
```

Now consider a delivery schedule. For each starting day 1 through 4, list that day and the following two days, without going beyond day 5. The lower boundary is the starting day. The upper boundary is the smaller of start + 2 and 5. Derive that boundary once per outer iteration.

```javascript
for (let start = 1; start <= 4; start++) {
  let end = start + 2;
  if (end > 5) end = 5;
  for (let day = start; day <= end; day++) {
    console.log(start, day);
  }
}
```

Output:

```text
1 1
1 2
1 3
2 2
2 3
2 4
3 3
3 4
3 5
4 4
4 5
```

The last group has only two operations. A fixed count of three would incorrectly include day 6. For inclusive integer boundaries with unit steps, the count is end - start + 1 when end >= start, and zero otherwise. Here the total is 3 + 3 + 3 + 2 = 11.

Practice: for starting days 1 through 5, list only later days within two days of the start and no later than day 5. Expected pairs: (1,2), (1,3), (2,3), (2,4), (3,4), (3,5), (4,5). Explain why the last outer iteration has no inner operations.

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

### A condition-controlled outer loop

A stream contains batches of positive quantities separated by zero. Stop at -1 or exhaustion. For [2,3,0,4,-1,8], report batch totals 5 and 4. The outer loop begins a batch; the inner loop consumes its ordinary values. The separator must be consumed separately, otherwise the outer loop would repeatedly start an empty batch at the same position.

```javascript
const values = [2, 3, 0, 4, -1, 8];
let i = 0;
while (i < values.length && values[i] !== -1) {
  let total = 0;
  let count = 0;
  while (i < values.length && values[i] !== 0 && values[i] !== -1) {
    total += values[i];
    count++;
    i++;
  }
  if (count > 0) console.log(total);
  if (i < values.length && values[i] === 0) i++;
}
```

Output:

```text
5
4
```

The first inner loop consumes 2 and 3, then stops at zero. The outer body reports 5 and advances past zero. The next inner loop consumes 4 and stops at -1. The outer condition now fails. Each continuing outer iteration either consumes ordinary values or a separator; a sentinel or exhaustion ends processing. Leading and consecutive separators are safe because each is consumed even when count is zero.

Practice: test [0,2,0,0,3,4,-1] (totals 2 and 7), [] (no output), and [2,3] (total 5). Explain why removing the separator update prevents termination.

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

### Combining group and global counts

For three groups of two measurements, count positive readings in each group and overall. Also count how many groups contain at least one positive reading. The group count must be reset for each group; the global count and qualifying-group count must persist.

```javascript
const values = [0, 5, -2, 0, 3, 8];
let index = 0;
let overall = 0;
let qualifyingGroups = 0;
for (let group = 1; group <= 3; group++) {
  let count = 0;
  for (let j = 0; j < 2; j++) {
    if (values[index] > 0) count++;
    index++;
  }
  overall += count;
  if (count > 0) qualifyingGroups++;
  console.log(group, count);
}
console.log(overall, qualifyingGroups);
```

Output:

```text
1 1
2 0
3 2
3 2
```

Incrementing qualifyingGroups inside the inner loop would count readings rather than groups. The condition count > 0 becomes a group-level decision only after the inner traversal completes. Practice: change the requirement to count groups where every reading is positive. Expected qualifying groups: 1.

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

### Comparing flags and labeled exits

The earlier first-pair search can also use a flag. The inner break exits only the inner loop; the outer break must be explicitly reached.

```javascript
const a = [2, 4, 6];
const b = [1, 3, 5];
let found = false;
for (let i = 0; i < a.length; i++) {
  for (let j = 0; j < b.length; j++) {
    if (a[i] + b[j] === 7) {
      console.log(a[i], b[j]);
      found = true;
      break;
    }
  }
  if (found) break;
}
if (!found) console.log("No pair");
```

Output:

```text
2 5
```

Without the outer break, the search would also print 4 3 and 6 1. The flag communicates the inner result to the outer level. A label avoids this extra control check, but a flag can also communicate success to subsequent code. Neither is inherently required for all searches.

Practice: modify both approaches to search for sum 20. Expect No pair. Then search for sum 5; expect 2 3. Explain which loop update is bypassed by each break.

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

### Deriving pair boundaries from requirements

For four values [4,7,9,12], imagine a grid of positions. The diagonal represents pairing an element with itself. Above the diagonal are pairs whose second index is greater than the first.

```text
       j=0 j=1 j=2 j=3
i=0     -   X   X   X
i=1     -   -   X   X
i=2     -   -   -   X
i=3     -   -   -   -
```

A direct solution examines all sixteen combinations and prints only those satisfying j > i.

```javascript
const values = [4, 7, 9, 12];
for (let i = 0; i < values.length; i++) {
  for (let j = 0; j < values.length; j++) {
    if (j > i) console.log(values[i], values[j]);
  }
}
```

Output:

```text
4 7
4 9
4 12
7 9
7 12
9 12
```

For a fixed i, the first allowed j is i + 1. Starting there instead of zero removes ten unnecessary body executions. The output order remains unchanged.

If order matters, use all positions and exclude only i === j. For [4,7,9], this produces six pairs:

```javascript
const values = [4, 7, 9];
for (let i = 0; i < values.length; i++) {
  for (let j = 0; j < values.length; j++) {
    if (i !== j) console.log(values[i], values[j]);
  }
}
```

Output:

```text
4 7
4 9
7 4
7 9
9 4
9 7
```

If self-pairs are permitted but reversed repetitions are not, start j at i. If only adjacent positions are needed, a second loop is unnecessary: for each i below length - 1, use i + 1 directly.

```javascript
const values = [4, 7, 9];
for (let i = 0; i + 1 < values.length; i++) {
  console.log(values[i], values[i + 1]);
}
```

Output:

```text
4 7
7 9
```

Practice: generate unordered pairs including self-pairs for [2,5,8]. Expect (2,2), (2,5), (2,8), (5,5), (5,8), (8,8). Then generate adjacent pairs for [] and [2]; both produce no output.

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

### Modifying and sharing rows

An array element can hold an array. Assigning that element to a variable does not copy its contents; both refer to the same row. This matters when constructing tables.

```javascript
const table = [[10, 20], [30, 40]];
const row = table[1];
row[0] = 99;
console.log(table[1][0]);
```

Output:

```text
99
```

The const declaration prevents reassigning row, not changing its elements. To construct independent rows, create a new array on each outer iteration. Reusing one row would make every stored row refer to the same growing array.

### Column totals for unequal rows

For [[2,4],[],[6,8,10]], calculate column totals, treating absent positions as contributing nothing. First discover the maximum row length. Then traverse each possible column and add only existing positions.

```javascript
const table = [[2, 4], [], [6, 8, 10]];
let width = 0;
for (let r = 0; r < table.length; r++) {
  if (table[r].length > width) width = table[r].length;
}
const totals = [];
for (let c = 0; c < width; c++) {
  let total = 0;
  for (let r = 0; r < table.length; r++) {
    if (c < table[r].length) total += table[r][c];
  }
  totals.push(total);
}
console.log(totals);
```

Output: [8, 12, 10]. The width is zero for an empty table, so no column loop executes. This assumes dense rows containing numbers, not arrays with missing internal elements.

### Searching a nested array

Find the first coordinate of 7 in [[1,2],[],[7,7]]. A labeled break stops the entire traversal after the first match. Initialize coordinates to -1, which cannot be a valid index.

```javascript
const table = [[1, 2], [], [7, 7]];
let foundRow = -1;
let foundCol = -1;
search: for (let r = 0; r < table.length; r++) {
  for (let c = 0; c < table[r].length; c++) {
    if (table[r][c] === 7) {
      foundRow = r;
      foundCol = c;
      break search;
    }
  }
}
console.log(foundRow, foundCol);
```

Output:

```text
2 0
```

Practice: search for 2 (0,1), 99 (-1,-1), and 7 in an empty table (-1,-1). Then collect every matching coordinate instead of stopping at the first. For 7 the expected coordinates are [[2,0],[2,1]].

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
