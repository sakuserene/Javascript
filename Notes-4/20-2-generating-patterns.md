# Lesson 20 - Generating Patterns

*JavaScript Plain Loops*

Previous: [Frequency Counting and Grouping](19-frequency-counting-and-grouping.md) | Next: Advanced Loop Techniques (planned)

Pattern generation translates structural requirements into loop boundaries, relationships between variables and conditional operations. These techniques also apply to tables, calendars, reports and coordinate-based layouts. The goal is to derive solutions rather than memorize shapes.

## Table of contents

1. [One-dimensional patterns](#1-one-dimensional-patterns)
2. [Two-dimensional patterns](#2-two-dimensional-patterns)
3. [Geometric patterns](#3-geometric-patterns)
4. [Conditional patterns](#4-conditional-patterns)
5. [Number and character patterns](#5-number-and-character-patterns)
6. [Summary](#6-summary)
7. [Integrated exercises](#7-integrated-exercises)
8. [Worked exercise solutions](#8-worked-exercise-solutions)
9. [Additional concept development and practice](#9-additional-concept-development-and-practice)

All examples are independent. Dimensions are assumed to be small nonnegative integers unless otherwise specified. Spaces in text patterns are significant. Run each example separately.

## 1. One-dimensional patterns

A one-dimensional pattern is a sequence whose structure can be described using a single position. To generate five stars, accumulate one character per iteration rather than printing each character on a separate line.

```javascript
const length = 5;
let pattern = "";
for (let i = 0; i < length; i++) {
  pattern += "*";
}
console.log(pattern);
```

Expected output:

```text
*****
```

The accumulator progresses through `*`, `**`, `***`, `****`, `*****`. A length of zero produces an empty string. A negative length is invalid under our assumptions, even though the loop would perform no iterations.

### Alternating patterns

To produce ABABABA, even indexes select A and odd indexes select B. The remainder after division by two distinguishes them.

```javascript
let pattern = "";
for (let i = 0; i < 7; i++) {
  if (i % 2 === 0) {
    pattern += "A";
  } else {
    pattern += "B";
  }
}
console.log(pattern);
```

Expected output:

```text
ABABABA
```

### Repeating a sequence

A longer repeating unit need not require more conditions. Use indexed string access and remainder to wrap around to its beginning.

```javascript
const unit = "ABC";
let pattern = "";
for (let i = 0; i < 8; i++) {
  pattern += unit[i % unit.length];
}
console.log(pattern);
```

Expected output:

```text
ABCABCAB
```

The combined expression can be decomposed:

```javascript
const unit = "ABC";
let pattern = "";
for (let i = 0; i < 8; i++) {
  const position = i % unit.length;
  const character = unit[position];
  const updatedPattern = pattern + character;
  pattern = updatedPattern;
}
console.log(pattern);
```

Expected output:

```text
ABCABCAB
```

`unit.length` evaluates to 3. At i = 4, `i % unit.length` becomes `4 % 3`, which evaluates to 1. The remainder of division by a positive integer is a valid index when the dividend is a nonnegative integer. `const position` creates a fresh binding each time the loop body executes. `unit[position]` becomes `unit[1]`, retrieving "B". If pattern was "ABCA", `pattern + character` evaluates to "ABCAB". Assignment stores this new string. The compact statement `pattern += unit[position]` performs these same operations: indexed access, concatenation and assignment. It does not mutate the original string.

| i | position | character | pattern afterward |
|---:|---:|---|---|
|0|0|A|A|
|1|1|B|AB|
|2|2|C|ABC|
|3|0|A|ABCA|
|4|1|B|ABCAB|
|5|2|C|ABCABC|
|6|0|A|ABCABCA|
|7|1|B|ABCABCAB|

Without remainder, index 3 retrieves undefined, and concatenation appends the text "undefined". The indexes are 0,1,2,0,1,2,0,1. This assumes a nonempty unit of single UTF-16 code-unit characters. An empty unit must be rejected or handled separately: remainder by zero is NaN and indexing with it does not retrieve a character.

### Repeating blocks

For AAABBBCCC, positions 0 through 2 belong to block 0, positions 3 through 5 belong to block 1, and positions 6 through 8 belong to block 2. Integer division gives the block index, whereas remainder gives a position within a block.

```javascript
const characters = "ABC";
const blockSize = 3;
let pattern = "";
for (let i = 0; i < characters.length * blockSize; i++) {
  pattern += characters[Math.trunc(i / blockSize)];
}
console.log(pattern);
```

Expected output:

```text
AAABBBCCC
```

At i = 5, `i / blockSize` evaluates to approximately 1.6667. `Math.trunc()` removes the fractional part, producing 1. `characters[1]` retrieves B. The boundary `characters.length * blockSize` evaluates to 3 * 3 = 9, so the last visited index is 8, whose block is 2. Remainder would instead alternate the letters.

For a positive block size, an alternative is an outer loop over characters and an inner loop repeating the current character. The nested version expresses the grouping directly; the single loop is useful when the absolute position matters.

### Practice

1. Generate eight stars: `********`.
2. Repeat XYZ to length ten: `XYZXYZXYZX`.
3. Repeat each character of ABC four times: `AAAABBBBCCCC`.
4. Generate nine positions with stars at indexes divisible by three: `*--*--*--`.
5. Explain the difference between remainder and integer division for indexes 0 through 8.

## 2. Two-dimensional patterns

A rectangle has two independent dimensions. For three rows and four columns, each row must contain four characters. The outer loop selects the row; the inner loop constructs it.

```javascript
const height = 3;
const width = 4;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < width; col++) {
    line += "*";
  }
  console.log(line);
}
```

Expected output:

```text
****
****
****
```

The line accumulator belongs to one row, so initialize it inside the outer loop. If initialized outside, it retains previous rows and grows to lengths 4, 8, 12. Printing inside the inner loop instead would print partial rows. The placement of initialization and output determines the level of repetition.

### Generating a reusable grid

Instead of printing immediately, collect completed rows in an array. This separates construction from display and permits subsequent processing.

```javascript
const grid = [];
for (let row = 0; row < 2; row++) {
  let line = "";
  for (let col = 0; col < 3; col++) {
    line += "#";
  }
  grid.push(line);
}
console.log(grid);
```

Expected output:

```text
[ '###', '###' ]
```

For height zero there are no rows; for width zero each row is an empty string. Decide whether those are valid results for the application.

### Dependent row lengths

A growing triangle has row lengths 1,2,3,4. With zero-based rows, the length is row + 1. A shrinking triangle has lengths 4,3,2,1, so its length is height - row.

```javascript
const height = 4;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < row + 1; col++) {
    line += "*";
  }
  console.log(line);
}
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < height - row; col++) {
    line += "*";
  }
  console.log(line);
}
```

Expected output:

```text
*
**
***
****
****
***
**
*
```

The growing triangle performs 1+2+3+4 = 10 character appends. In general the sum is height * (height + 1) / 2: pairing the sequence with its reverse produces height pairs each summing to height + 1, and counts the original sum twice.

### Practice

1. Generate a rectangle of two rows and six columns.
2. Generate a growing triangle of five rows, using row lengths 2,4,6,8,10.
3. Predict the output of the rectangle implementation if line is initialized outside the outer loop. Explain the correction.
4. Construct a grid of three strings, each containing four dots. Verify that a zero-height grid is empty.

## 3. Geometric patterns

Alignment adds a new requirement: characters must occupy specific horizontal positions. Spaces are part of the generated string, not merely decoration.

### Right-aligned triangle

For height four, the right edge must remain in column three. Each row has row+1 stars and height-row-1 leading spaces. Their sum is always height.

| Row | Spaces | Stars |
|---:|---:|---:|
| 0 | 3 | 1 |
| 1 | 2 | 2 |
| 2 | 1 | 3 |
| 3 | 0 | 4 |

```javascript
const height = 4;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let s = 0; s < height - row - 1; s++) {
    line += " ";
  }
  for (let c = 0; c < row + 1; c++) {
    line += "*";
  }
  console.log(line);
}
```

Expected output:

```text
   *
  **
 ***
****
```

The two inner loops are sequential, not nested within each other. One generates padding and the other generates the visible part. Using height-row spaces would shift the entire pattern one position right.

### Centered pyramid

A centered pyramid expands on both sides of its center. Its star counts are 1,3,5,7, so the formula is 2*row+1. Its leading spaces decrease from height-1 to zero.

```javascript
const height = 4;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let s = 0; s < height - row - 1; s++) {
    line += " ";
  }
  for (let c = 0; c < 2 * row + 1; c++) {
    line += "*";
  }
  console.log(line);
}
```

Expected output:

```text
   *
  ***
 *****
*******
```

The first star is at height-row-1 and the last at height+row-1. Their midpoint is height-1 for every row. This explains the alignment rather than relying on a memorized formula. A height of one produces one star.

### Diamond

A diamond consists of a growing pyramid and a shrinking pyramid without repeating the widest row. For height three (the upper half including the center), the levels are 0,1,2,1,0. There are 2*height-1 rows. We can use a single outer loop and calculate the level from its distance to the center.

```javascript
const height = 3;
for (let row = 0; row < 2 * height - 1; row++) {
  const distance = Math.abs(row - (height - 1));
  const level = height - 1 - distance;
  let line = "";
  for (let s = 0; s < distance; s++) {
    line += " ";
  }
  for (let c = 0; c < 2 * level + 1; c++) {
    line += "*";
  }
  console.log(line);
}
```

Expected output:

```text
  *
 ***
*****
 ***
  *
```

An alternative is two outer loops: levels 0 through height-1, then height-2 down to zero. This is easier to derive from the two halves, while the distance formula is useful when a single row coordinate is needed. Starting the second half at height-1 repeats the center.

### Practice

1. Generate a right-aligned triangle of height five. Verify every line has length five.
2. Generate an inverted centered pyramid with star counts 7,5,3,1 and leading spaces 0,1,2,3.
3. Generate a diamond of height four, with seven rows and star counts 1,3,5,7,5,3,1.
4. Explain why a centered pyramid uses odd star counts. Test height one and zero.

## 4. Conditional patterns

Instead of varying the number of iterations per row, we can visit every cell of a rectangular grid and decide what belongs at each coordinate. This is particularly useful for outlines and intersecting shapes.

### Hollow rectangle

A cell belongs to the border when its row is the first or last row, or its column is the first or last column. All other cells are interior. The OR operator is essential because any one boundary is sufficient.

```javascript
const height = 4;
const width = 6;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < width; col++) {
    if (row === 0 || row === height - 1 ||
        col === 0 || col === width - 1) {
      line += "*";
    } else {
      line += " ";
    }
  }
  console.log(line);
}
```

Expected output:

```text
******
*    *
*    *
******
```

For a one-row or one-column rectangle, every cell is on the border. Combining all four tests with AND would require a cell to be simultaneously on opposite edges and normally produces no stars. Using only the row tests omits the vertical edges.

### Diagonals and intersections

For a square of size five, the main diagonal contains (0,0),(1,1),(2,2),(3,3),(4,4). The other diagonal contains (0,4),(1,3),(2,2),(3,1),(4,0). The first has row equal to column; the second has row+column equal to size-1.

```javascript
const size = 5;
for (let row = 0; row < size; row++) {
  let line = "";
  for (let col = 0; col < size; col++) {
    if (row === col || row + col === size - 1) {
      line += "*";
    } else {
      line += ".";
    }
  }
  console.log(line);
}
```

Expected output:

```text
*...*
.*.*.
..*..
.*.*.
*...*
```

The center is printed only once because each cell produces one character. With even size, the diagonals do not share a cell. Two independent append statements would incorrectly produce two characters at an intersection.

### Checkerboard

Alternating columns alone produces vertical stripes. To alternate across both dimensions, use the parity of row+col. Moving one step horizontally or vertically changes that sum by one.

```javascript
for (let row = 0; row < 3; row++) {
  let line = "";
  for (let col = 0; col < 5; col++) {
    if ((row + col) % 2 === 0) {
      line += "#";
    } else {
      line += ".";
    }
  }
  console.log(line);
}
```

Expected output:

```text
#.#.#
.#.#.
#.#.#
```

### Hollow pyramid

A pyramid of height four has a center column of three. For row r, the left edge is center-r and the right edge is center+r. A cell is visible if it lies on either edge or lies between the edges on the bottom row. Use dots to make the full rectangular coordinate system visible.

```javascript
const height = 4;
const center = height - 1;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < 2 * height - 1; col++) {
    const left = center - row;
    const right = center + row;
    if (col === left || col === right ||
        (row === height - 1 && col >= left && col <= right)) {
      line += "*";
    } else {
      line += ".";
    }
  }
  console.log(line);
}
```

Expected output:

```text
...*...
..*.*..
.*...*.
*******
```

For height one, both edges coincide and the bottom row consists of a single cell. This approach evaluates every cell, including background cells. A padding-and-edge approach could generate only the necessary visible portion but would need special handling when the edges coincide.

### Practice

1. Generate a hollow rectangle of height three and width five. Test width one.
2. Generate a 4-by-4 X pattern with dots for background. Expected rows: `*..*`, `.**.`, `.**.`, `*..*`.
3. Generate a 5-by-5 plus sign using its center row and column.
4. Modify the checkerboard so the top-left cell is a dot.
5. Explain why two independent append conditions can produce an incorrect row width at an intersection.

## 5. Number and character patterns

A pattern may require calculating the value at each position instead of selecting a fixed symbol. Distinguish values that depend on the row, the column, both coordinates, or persistent state.

### Restarting versus continuing sequences

For rows `1`, `1 2`, `1 2 3`, the value depends only on the column. For rows `1`, `2 3`, `4 5 6`, the sequence continues across rows, so a counter must persist outside the outer loop.

```javascript
let next = 1;
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col <= row; col++) {
    if (col > 0) {
      line += " ";
    }
    line += next;
    next++;
  }
  console.log(line);
}
```

Expected output:

```text
1
2 3
4 5 6
7 8 9 10
```

Adding a number to a string concatenates its text representation. The separator is appended only after the first value, avoiding leading and trailing separators. Resetting next inside the outer loop would restart the sequence each row.

### Multiplication table and alignment

For a table of products, cell (row,col) contains row*col when both coordinates start at one. Multi-digit numbers occupy more characters, so ordinary single spaces do not align columns. Build a field of fixed width by converting the number to text with String(value), which returns its string representation, and adding leading spaces until the desired width is reached.

```javascript
for (let row = 1; row <= 4; row++) {
  let line = "";
  for (let col = 1; col <= 4; col++) {
    let field = String(row * col);
    while (field.length < 3) {
      field = " " + field;
    }
    line += field;
  }
  console.log(line);
}
```

Expected output:

```text
  1  2  3  4
  2  4  6  8
  3  6  9 12
  4  8 12 16
```

A field width of three is sufficient here. For larger tables choose a width greater than the number of digits of the largest product. The loop never truncates a value that exceeds the width; alignment would then be lost.

### Character sequences

Use an alphabet string as a lookup table. The column selects a character, while the row determines how many characters to include. This avoids requiring character-code arithmetic.

```javascript
const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col <= row; col++) {
    line += alphabet[col];
  }
  console.log(line);
}
```

Expected output:

```text
A
AB
ABC
ABCD
```

For more than 26 columns, choose whether to reject the size or cycle through the alphabet with remainder. These are different requirements. Reading beyond the alphabet would append the text undefined.

### Symmetric number patterns

To generate `12321`, the value rises until the center and then decreases. For a row with peak p, the width is 2*p-1 and the center index is p-1. The value is p minus the distance from the center.

```javascript
for (let peak = 1; peak <= 4; peak++) {
  let line = "";
  for (let col = 0; col < 2 * peak - 1; col++) {
    line += peak - Math.abs(col - (peak - 1));
  }
  console.log(line);
}
```

Expected output:

```text
1
121
12321
1234321
```

For peaks greater than nine, numbers occupy multiple characters. Use separators or fixed-width fields if the requirement is symmetry of numerical positions rather than characters.

### Practice

1. Generate rows `1`, `12`, `123`, `1234` without spaces.
2. Generate three rows of three consecutive numbers starting at 10. Expected rows: `10 11 12`, `13 14 15`, `16 17 18`.
3. Generate `A`, `BB`, `CCC`, `DDDD` using the row to select the letter.
4. Generate a 3-by-3 addition table using coordinates starting at one. Expected rows: `2 3 4`, `3 4 5`, `4 5 6`.
5. Explain why a fixed-width field is needed for a table containing both single-digit and double-digit values.

## 6. Summary

| Requirement | Technique |
|---|---|
| Repeat a symbol | String accumulator |
| Cycle through symbols | Remainder |
| Repeat blocks | Integer division |
| Rectangular layout | Independent nested loops |
| Changing row lengths | Dependent inner boundaries |
| Alignment | Leading padding |
| Symmetry | Distance from center |
| Borders and diagonals | Coordinate conditions |
| Continuous numbering | Persistent counter |
| Aligned numeric columns | Fixed-width fields |

To derive a pattern, identify its dimensions, enumerate representative coordinates, determine what changes with each coordinate, derive boundaries and conditions, then verify the smallest and largest cases. Decide whether the output is a string, a collection of rows, or printed lines. Keep the accumulator at the appropriate level of repetition.

## 7. Integrated exercises

Worked solutions and explanations are provided in Section 8. Try each exercise independently before reading its solution. Use traditional loops only. Test each implementation at its smallest valid size and at a larger size.

### 7.1 - Striped banner

Generate a banner of height three and width eight. Alternate blocks of two hashes and two dots horizontally. Every row must be `##..##..`. Then modify it so odd rows start with dots.

### 7.2 - Hollow diamond

Generate the following five-by-five grid using coordinate conditions. Hint: a boundary cell has the sum of its absolute row and column distances from the center equal to two.

```text
..*..
.*.*.
*...*
.*.*.
..*..
```

### 7.3 - Framed checkerboard

Generate a 5-by-6 grid with a star border and alternating dots and hashes inside. The first interior cell is a hash.

```text
******
*#.#.*
*.#.#*
*#.#.*
******
```

### 7.4 - Calendar layout

A month has 30 days and begins in column two of a seven-column grid (columns start at zero). Print five rows of seven fields, each three characters wide. Blank fields precede day one and follow day 30. Derive the day from the absolute cell position. Verify the first row contains two blanks followed by 1 through 5, and the final row contains 27 through 30 followed by three blanks.

### 7.5 - Debugging a triangle

A programmer initializes line outside the outer loop and appends row+1 stars each row. For height four, predict the four output lengths, explain the mistake, and correct it. The intended lengths are 1,2,3,4.

### 7.6 - Symmetric labels

Generate the rows A, ABA, ABCBA, ABCDCBA using alphabet indexing. Derive the index from the distance to the center. Reject a requested peak greater than 26 or explicitly define a cycling rule.

### 7.7 - Multiplication report

Generate a multiplication table from 1 through 12 on both axes. Use fields of width four. Verify the first and last products are 1 and 144 and that every row has 48 characters.

### 7.8 - Pattern as data

Construct an array of strings representing a 4-by-4 X pattern. Expected array: ["*..*", ".**.", ".**.", "*..*"]. In a separate traversal count the stars. Expected count: 8. Repeat for size five; expected count: 9. Explain the difference.

### 7.9 - Independent design

Generate a square spiral-like border consisting of alternating concentric square rings. For size five, use hashes on the outer ring, dots on the next ring and a hash at the center. Determine the ring by finding the smallest distance to any edge using comparisons. Expected output:

```text
#####
#...#
#.#.#
#...#
#####
```

Previous: [Frequency Counting and Grouping](19-frequency-counting-and-grouping.md) | Next: Advanced Loop Techniques (planned)

## 8. Worked exercise solutions

Each code block is independent. The following solutions correspond to every exercise in Sections 1-5 and 7. Alternative correct implementations are possible.

### Section 1 solutions

**1.1 - Eight stars.** Start with no characters and append one per iteration. The exclusive boundary 8 gives indexes 0 through 7.

```javascript
let pattern = "";
for (let i = 0; i < 8; i++) pattern += "*";
console.log(pattern);
```

**1.2 - Repeat XYZ.** The remainder cycles through 0, 1, 2; indexing retrieves the corresponding letter. Ten iterations end at index 9, selecting X.

```javascript
const unit = "XYZ";
let pattern = "";
for (let i = 0; i < 10; i++) {
  const position = i % unit.length;
  pattern += unit[position];
}
console.log(pattern);
```

**1.3 - Four copies per letter.** The outer index selects a letter; the inner index counts copies. Using the inner index to select a letter would change the requirement.

```javascript
const letters = "ABC";
let pattern = "";
for (let i = 0; i < letters.length; i++) {
  for (let j = 0; j < 4; j++) pattern += letters[i];
}
console.log(pattern);
```

**1.4 - Every third position.** At indexes 0, 3, 6 the remainder is zero. All other positions receive a hyphen.

```javascript
let pattern = "";
for (let i = 0; i < 9; i++) {
  if (i % 3 === 0) pattern += "*";
  else pattern += "-";
}
console.log(pattern);
```

**1.5 - Remainder versus integer division.** For indexes 0 through 8, remainder gives 0,1,2,0,1,2,0,1,2; truncating division gives 0,0,0,1,1,1,2,2,2. The former selects a position within a block; the latter identifies the block.

### Section 2 solutions

**2.1 - Rectangle.** Two outer iterations each construct six characters. Reset the accumulator inside the outer loop.

```javascript
for (let row = 0; row < 2; row++) {
  let line = "";
  for (let col = 0; col < 6; col++) line += "*";
  console.log(line);
}
```

**2.2 - Even row lengths.** The first row has two stars and each subsequent row adds two. The expression `2 * (row + 1)` converts zero-based row indexes to 2,4,6,8,10.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 2 * (row + 1); col++) line += "*";
  console.log(line);
}
```

**2.3 - Accumulator placement.** For the original 3-by-4 rectangle, moving `line` outside the outer loop yields lengths 4,8,12 because each row retains the previous characters. Move `let line = ""` inside the outer loop, before the inner loop.

**2.4 - Grid as data.** Push each completed string once. With height zero the outer loop never executes, so the result remains empty.

```javascript
const grid = [];
for (let row = 0; row < 3; row++) {
  let line = "";
  for (let col = 0; col < 4; col++) line += ".";
  grid.push(line);
}
console.log(grid);
```

### Section 3 solutions

**3.1 - Right alignment.** Each row has `row + 1` stars; subtract that count from 5 to obtain the padding. The sum of the counts is always five.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let i = 0; i < 4 - row; i++) line += " ";
  for (let i = 0; i < row + 1; i++) line += "*";
  console.log(line);
}
```

**3.2 - Inverted pyramid.** Traverse levels 3,2,1,0. The same formulas as the growing pyramid now produce decreasing star counts and increasing padding.

```javascript
for (let level = 3; level >= 0; level--) {
  let line = "";
  for (let i = 0; i < 3 - level; i++) line += " ";
  for (let i = 0; i < 2 * level + 1; i++) line += "*";
  console.log(line);
}
```

**3.3 - Diamond.** Seven rows share center row 3. The absolute distance from that row is 3,2,1,0,1,2,3. Subtracting from 3 gives the pyramid level.

```javascript
for (let row = 0; row < 7; row++) {
  const spaces = Math.abs(row - 3);
  const level = 3 - spaces;
  let line = "";
  for (let i = 0; i < spaces; i++) line += " ";
  for (let i = 0; i < 2 * level + 1; i++) line += "*";
  console.log(line);
}
```

**3.4 - Odd widths.** A centered row contains one center star and an equal number on both sides, hence `2 * row + 1`. Height one prints a single star; height zero prints no rows.

### Section 4 solutions

**4.1 - Hollow rectangle.** A cell is on the boundary if any of the four comparisons succeeds. Width one makes every cell a boundary cell.

```javascript
const height = 3, width = 5;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < width; col++) {
    if (row === 0 || row === height - 1 || col === 0 || col === width - 1) line += "*";
    else line += " ";
  }
  console.log(line);
}
```

**4.2 - X pattern.** The main diagonal has equal indexes. The opposite diagonal has index sum 3. OR ensures one character per cell.

```javascript
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col < 4; col++) {
    if (row === col || row + col === 3) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

**4.3 - Plus sign.** The center row or center column is 2. At their intersection both conditions hold but only one star is appended.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 5; col++) {
    if (row === 2 || col === 2) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

**4.4 - Reversed checkerboard.** Select a dot for even coordinate sums instead of a hash. At (0,0) the sum is zero.

```javascript
for (let row = 0; row < 3; row++) {
  let line = "";
  for (let col = 0; col < 5; col++) {
    if ((row + col) % 2 === 0) line += ".";
    else line += "#";
  }
  console.log(line);
}
```

**4.5 - Intersection error.** Two independent `if` statements that each append a star append twice when both conditions hold. Use one combined OR condition and one else branch, ensuring exactly one append per cell.

### Section 5 solutions

**5.1 - Restarting numbers.** The column starts at zero on every row. Adding one produces the desired number; string concatenation converts it to text.

```javascript
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col <= row; col++) line += col + 1;
  console.log(line);
}
```

**5.2 - Continuing numbers.** Keep the counter outside both loops so it survives row changes. Insert spaces only before nonfirst values.

```javascript
let next = 10;
for (let row = 0; row < 3; row++) {
  let line = "";
  for (let col = 0; col < 3; col++) {
    if (col > 0) line += " ";
    line += next;
    next++;
  }
  console.log(line);
}
```

**5.3 - Row-dependent letters.** Select `alphabet[row]` instead of `alphabet[col]`. The inner loop repeats the selected letter.

```javascript
const alphabet = "ABCD";
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col <= row; col++) line += alphabet[row];
  console.log(line);
}
```

**5.4 - Addition table.** Start both indexes at one; each value is their sum. Parentheses are not necessary in `line += row + col` because the right side is evaluated before concatenation.

```javascript
for (let row = 1; row <= 3; row++) {
  let line = "";
  for (let col = 1; col <= 3; col++) {
    if (col > 1) line += " ";
    line += row + col;
  }
  console.log(line);
}
```

**5.5 - Fixed width.** A one-digit number occupies one character and a two-digit number occupies two. A width-three field pads them with two and one spaces respectively. Each field therefore ends at the same relative position, aligning columns.

### Integrated solutions

**7.1 - Striped banner.** Integer division by two gives block indexes 0,0,1,1,2,2,3,3. Remainder by two distinguishes alternating blocks. Adding the row index before testing parity reverses the symbols on odd rows. Remove `+ row` for the original version.

```javascript
for (let row = 0; row < 3; row++) {
  let line = "";
  for (let col = 0; col < 8; col++) {
    const block = Math.trunc(col / 2);
    if ((block + row) % 2 === 0) line += "#";
    else line += ".";
  }
  console.log(line);
}
```

Output: `##..##..`, `..##..##`, `##..##..` on separate lines.

**7.2 - Hollow diamond.** Subtract center 2 from each coordinate, take absolute values, then add. At (1,3) the distances are 1 and 1, totaling 2. Equality selects the outline; less-than-or-equal would fill it.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 5; col++) {
    const distance = Math.abs(row - 2) + Math.abs(col - 2);
    if (distance === 2) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

**7.3 - Framed checkerboard.** Border conditions take priority. The first interior cell (1,1) has even coordinate sum, so it receives a hash.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 6; col++) {
    if (row === 0 || row === 4 || col === 0 || col === 5) line += "*";
    else if ((row + col) % 2 === 0) line += "#";
    else line += ".";
  }
  console.log(line);
}
```

**7.4 - Calendar.** `row * 7 + col` is the absolute zero-based position. Subtract the two leading blanks and add one to convert to a day number. At (0,2), 0*7+2-2+1 = 1. At (4,3), 28+3-2+1 = 30. Reject positions outside 1 through 30 by leaving their fields blank. Pad every field to width three.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 7; col++) {
    const day = row * 7 + col - 2 + 1;
    let field = "";
    if (day >= 1 && day <= 30) field = String(day);
    while (field.length < 3) field = " " + field;
    line += field;
  }
  console.log(line);
}
```

**7.5 - Debugging.** The incorrect lengths are 1,3,6,10 because the previous total is retained. Reset the string inside the outer loop.

```javascript
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col <= row; col++) line += "*";
  console.log(line);
}
```

**7.6 - Symmetric labels.** For peak 4, the center index is 3. The distance sequence is 3,2,1,0,1,2,3. Subtract from 3 to obtain alphabet indexes 0,1,2,3,2,1,0. Validate the height against the alphabet length.

```javascript
const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
const height = 4;
if (height > alphabet.length) console.log("Invalid height");
else {
  for (let row = 0; row < height; row++) {
    let line = "";
    for (let col = 0; col < 2 * row + 1; col++) {
      const index = row - Math.abs(col - row);
      line += alphabet[index];
    }
    console.log(line);
  }
}
```

**7.7 - Multiplication report.** Each row contains 12 fields of four characters, so its length is 48. The largest product is 144, leaving at least one separating space.

```javascript
for (let row = 1; row <= 12; row++) {
  let line = "";
  for (let col = 1; col <= 12; col++) {
    let field = String(row * col);
    while (field.length < 4) field = " " + field;
    line += field;
  }
  console.log(line);
}
```

**7.8 - Pattern as data.** Store each completed row, then traverse its characters independently. Each diagonal contains size stars. Odd sizes share one center cell; even sizes do not.

```javascript
for (let size = 4; size <= 5; size++) {
  const grid = [];
  for (let row = 0; row < size; row++) {
    let line = "";
    for (let col = 0; col < size; col++) {
      if (row === col || row + col === size - 1) line += "*";
      else line += ".";
    }
    grid.push(line);
  }
  let count = 0;
  for (let row = 0; row < grid.length; row++) {
    for (let col = 0; col < grid[row].length; col++) {
      if (grid[row][col] === "*") count++;
    }
  }
  console.log(grid, count);
}
```

**7.9 - Concentric rings.** The four distances are row, col, size-1-row and size-1-col. The smallest determines the nearest edge. At (1,3) in size 5, the distances are 1,3,3,1, so ring 1 is a dot. Ring 0 and ring 2 are hashes.

```javascript
const size = 5;
for (let row = 0; row < size; row++) {
  let line = "";
  for (let col = 0; col < size; col++) {
    let ring = row;
    if (col < ring) ring = col;
    if (size - 1 - row < ring) ring = size - 1 - row;
    if (size - 1 - col < ring) ring = size - 1 - col;
    if (ring % 2 === 0) line += "#";
    else line += ".";
  }
  console.log(line);
}
```

## 9. Additional concept development and practice

### Variable blocks and alternative repetition

For ABBCCC, the outer index selects a letter and `i + 1` determines its repetition count. At i = 2 the inner loop runs three times, appending C each time. To generate odd block lengths 1,3,5,7 instead, use `2 * i + 1`.

```javascript
const letters = "ABCD";
let pattern = "";
for (let i = 0; i < letters.length; i++) {
  const repetitions = 2 * i + 1;
  for (let j = 0; j < repetitions; j++) pattern += letters[i];
}
console.log(pattern);
```

Output: `ABBBCCCCCDDDDDDD`.

Practice: Generate twelve characters in groups of two stars followed by one hyphen. Answer: `**-**-**-**-`. The remainder by three is 0 or 1 for a star and 2 for a hyphen.

```javascript
let line = "";
for (let i = 0; i < 12; i++) {
  if (i % 3 < 2) line += "*";
  else line += "-";
}
console.log(line);
```

### Comparing row construction and coordinate construction

A centered pyramid of height four has width seven and center column three. In row r, the horizontal distance from the center must be at most r. At row 2, column 5, `Math.abs(5 - 3)` gives 2, so the cell belongs to the pyramid. At column 6 the distance is 3, so it does not. The coordinate approach generates background cells as well; row construction can omit trailing background spaces.

```javascript
const height = 4;
const center = height - 1;
for (let row = 0; row < height; row++) {
  let line = "";
  for (let col = 0; col < 2 * height - 1; col++) {
    const distance = Math.abs(col - center);
    if (distance <= row) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

Practice: Generate a filled diamond of size seven. Answer: use the sum of absolute distances from center (3,3), and select distances at most three. For a hollow diamond change `<=` to `===`.

```javascript
for (let row = 0; row < 7; row++) {
  let line = "";
  for (let col = 0; col < 7; col++) {
    const distance = Math.abs(row - 3) + Math.abs(col - 3);
    if (distance <= 3) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

### Additional practical exercises with solutions

**Seating arrangement.** Generate four rows of six seats, marking the first row and last column unavailable. Expected rows: XXXXXX, OOOOOX, OOOOOX, OOOOOX. The first row is index zero; the last column is index five. Either condition is sufficient.

```javascript
for (let row = 0; row < 4; row++) {
  let line = "";
  for (let col = 0; col < 6; col++) {
    if (row === 0 || col === 5) line += "X";
    else line += "O";
  }
  console.log(line);
}
```

**Symmetric number pyramid.** Generate rows 1,121,12321,1234321 with leading spaces 3,2,1,0. The first inner loop generates padding, the second increases to the peak, and the third decreases from peak-1 to avoid duplicating the center.

```javascript
for (let row = 0; row < 4; row++) {
  let line = "";
  const peak = row + 1;
  for (let i = 0; i < 3 - row; i++) line += " ";
  for (let n = 1; n <= peak; n++) line += n;
  for (let n = peak - 1; n >= 1; n--) line += n;
  console.log(line);
}
```

**Border, diagonals and distinct center.** Generate a five-by-five grid with stars on the border and diagonals, O at the center, dots elsewhere. The center must be checked first because it also belongs to both diagonals.

```javascript
for (let row = 0; row < 5; row++) {
  let line = "";
  for (let col = 0; col < 5; col++) {
    if (row === 2 && col === 2) line += "O";
    else if (row === 0 || row === 4 || col === 0 || col === 4 || row === col || row + col === 4) line += "*";
    else line += ".";
  }
  console.log(line);
}
```

Expected output:

```text
*****
**.**
*.O.*
**.**
*****
```

Previous: [Frequency Counting and Grouping](19-frequency-counting-and-grouping.md) | Next: Advanced Loop Techniques (planned)
