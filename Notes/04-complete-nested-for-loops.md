# JavaScript Mastery â€” Topic 04: Complete Nested `for` Loops

> **Scope:** Standard ECMAScript `for` nesting, mechanics, scope, control flow, iteration patterns, algorithms, debugging, testing, performance, and advanced connections.  
> **Study method:** Read the business requirement first; predict and design before running code.  
> **Prerequisites:** Variables, expressions, comparisons, basic `if` and `for` statements, console output.  
> **Important:** A nested loop is a *composition of ordinary loop statements*, not a separate JavaScript keyword or API. This topic has no intrinsic methods/properties/constructors to inventory. Related APIs are distinguished below.

## Master capability inventory and coverage status

Legend: **CORE** = learn here; **RELATED** = understand here, full API later; **ADVANCED** = preview here, complete in the named future topic. **Covered** means explained or demonstrated in these notes; it does **not** mean you have mastered it.

| ID | Capability | Level | Location | Status |
|---|---|---|---|---|
| A1 | Outer/inner loop meaning, execution and reset | CORE | Parts 1â€“2 | Covered |
| A2 | Grammar, headers, bodies, braces, empty statements | CORE | Parts 1â€“2 | Covered |
| A3 | Initialization, test, body, update and evaluation order | CORE | Parts 1â€“2 | Covered |
| A4 | Upward, downward, stepped, mixed and dependent bounds | CORE | Parts 2, 4 | Covered |
| A5 | Zero iterations, termination and off-by-one behavior | CORE | Parts 2, 6 | Covered |
| A6 | `let`, `var`, `const`, blocks, shadowing, lifetime | CORE | Part 2 | Covered |
| A7 | Multiple counters, comma expressions, omitted clauses | CORE | Part 2 | Covered |
| A8 | Counters, accumulators, scope of declarations | CORE | Parts 2, 4 | Covered |
| B1 | Nested `if` / `else`, logical operators and `switch` | CORE | Part 3 | Covered |
| B2 | `break`, `continue`, labels and targets | CORE | Part 3 | Covered |
| B3 | `return`, `throw`, `try`/`finally` interactions | RELATED | Part 3 | Covered; Functions/Errors later |
| B4 | `while`, `do...while`, `for...of`, `for...in` mixing | RELATED | Part 3 | Covered; Iteration/Objects later |
| C1 | Rectangular, triangular, diagonal, staircase patterns | CORE | Part 4 | Covered |
| C2 | Cartesian product, unique pairs, comparisons | CORE | Part 4 | Covered |
| C3 | Counting, totals, searching, classification, grouping | CORE | Parts 4â€“5 | Covered |
| C4 | Three or more levels, jagged bounds, traversal order | CORE | Parts 4â€“5 | Covered |
| C5 | Arrays of arrays, objects, matrix mutation | ADVANCED | Parts 4â€“5 | Preview; Phase 2 Arrays/Objects |
| C6 | Recursion, graph search, backtracking | ADVANCED | Part 7 | Preview; Algorithms later |
| D1 | Business problems, algorithm selection, pseudocode | CORE | Parts 1, 4â€“5 | Covered |
| D2 | Execution tracing and condition-check counting | CORE | Parts 1â€“2, 6 | Covered |
| D3 | Syntax, reference, type, logic, termination bugs | CORE | Part 6 | Covered |
| D4 | Tests, boundaries, assertions, invariants | CORE | Part 6 | Covered |
| D5 | Time/space complexity, early exits, optimization | CORE | Part 6 | Covered |
| D6 | `Map`/`Set`, indexing, hash lookups | ADVANCED | Part 7 | Preview; Phase 2 |
| D7 | DOM grid generation and accessible UI | ADVANCED | Part 7 | Preview; Phase 3 |
| D8 | Async operations, promises, event loop, cancellation | ADVANCED | Part 7 | Preview; Phase 4 |
| D9 | Closures and per-iteration captured bindings | ADVANCED | Parts 2, 7 | Preview; Functions later |
| D10 | Performance profiling, engine optimizations | RELATED | Part 6 | Covered at conceptual level |

**Mastery rule:** Mark a capability *mastered* only after independent explanation, tracing, implementation, debugging, tests and transfer to an unfamiliar scenario. Do not mark this inventory as mastered merely by reading it.

---

# Part 1 â€” Foundations, syntax, and execution

## 1.1 Conceptual theory

An outer loop repeats a *group of operations*. If that group includes another loop, the inner loop normally completes its iterations before the outer loop advances. The inner initialization executes anew whenever control reaches that inner `for` statement again.

Standard form:

```js
for (outerInitialization; outerCondition; outerUpdate) {
  for (innerInitialization; innerCondition; innerUpdate) {
    // Operation for this pair of values.
  }
}
```

The ordinary `for` execution sequence is: **initialize once â†’ test â†’ body â†’ update â†’ test â†’ ... â†’ exit when test is falsy**. A `break`, `return`, or thrown exception can exit sooner. A `continue` skips the rest of the targeted loop body but in a classic `for` still proceeds to its update before retesting. The test is converted to a boolean by JavaScript's truthiness rules; it need not literally evaluate to `true` or `false`.

The header consists of three *optional* sections separated by **two mandatory semicolons**. The first section can be a variable declaration or expression; the second is the continuation test; the third is an update expression. Omitting the test means no test-based termination (equivalent to an always-truthy condition).

## 1.2 Real-world question first â€” School registration

**Background:** A school has 3 classes with 4 students in each. **Objective:** Generate an ordered registration checklist. **Inputs:** class identifiers 1â€“3 and student identifiers 1â€“4. **Rules:** Process each class fully before moving to the next; restart student numbering for every class. **Constraints:** Do not skip or duplicate a pair. **Output:** Twelve lines. **Edge cases:** No classes, no students, one class, and variable class sizes.

**Recognition:** â€œFor each class, for each studentâ€ describes a repeated operation inside another repeated operation. It is not merely a request to print 12 lines; the *grouping and order* matter.

**Pseudocode:**

```text
FOR class = 1 TO 3
    FOR student = 1 TO 4
        DISPLAY current class and student
    END
END
```

**Implementation:**

```js
for (let classNumber = 1; classNumber <= 3; classNumber++) {
  for (let studentNumber = 1; studentNumber <= 4; studentNumber++) {
    console.log(`Class ${classNumber}, Student ${studentNumber}`);
  }
}
```

**Line-by-line decomposition:**

- `for` introduces a loop; `(` and `)` enclose its control header. `let classNumber = 1` creates the outer counter, assigns its initial numeric value, and runs once when entering the outer loop. `classNumber <= 3` is a less-than-or-equal comparison checked before each outer body. `classNumber++` increments the outer counter after the body completes. `{` starts the outer block.
- The second `for` declares a separate `let studentNumber`, initially `1`. Its `<= 4` condition admits 1, 2, 3 and 4. Its `++` update occurs after each inner body. Crucially, this entire statement is *inside* the outer block, so its initialization is reached again for each class.
- `console.log(...)` calls the `log` method on the console object. The argument is a template literal delimited by backticks; `${classNumber}` and `${studentNumber}` substitute current values into a string. It logs once for each valid pair. Removing it preserves iteration but produces no checklist; moving it outside the inner block loses per-student output, and `studentNumber` is not in scope there.
- The first closing `}` ends the inner body; the second ends the outer body. Changing their positions changes which statements repeat.

**Expected output:**

```text
Class 1, Student 1
Class 1, Student 2
Class 1, Student 3
Class 1, Student 4
Class 2, Student 1
Class 2, Student 2
Class 2, Student 3
Class 2, Student 4
Class 3, Student 1
Class 3, Student 2
Class 3, Student 3
Class 3, Student 4
```

**Trace:**

| Inner body execution | Outer counter | Inner counter |
|---:|---:|---:|
| 1 | 1 | 1 |
| 2 | 1 | 2 |
| 3 | 1 | 3 |
| 4 | 1 | 4 |
| 5 | 2 | 1 |
| 6 | 2 | 2 |
| 7 | 2 | 3 |
| 8 | 2 | 4 |
| 9 | 3 | 1 |
| 10 | 3 | 2 |
| 11 | 3 | 3 |
| 12 | 3 | 4 |

After the last body, `studentNumber` updates to 5 and fails `<= 4`. Then `classNumber` updates to 4 and fails `<= 3`. Outer test evaluations = 4; inner test evaluations = 3 Ã— 5 = 15. This distinction between *body executions* and *condition evaluations* is important.

**Alternatives:** One flat loop could produce 12 identifiers with arithmetic, but nested loops express the grouping more directly. `for...of` may be preferable once the classes and students are represented as arrays. A single loop is preferable when there is only one independent sequence.

**Check yourself (do not run yet):** Predict all outputs if the outer bound is 2 and the inner bound is 3. Then explain why the inner counter returns to 1.

---

# Part 2 â€” Complete loop mechanics, counters, scope and boundaries

## 2.1 Exact header variations

```js
// Increasing and decreasing.
for (let i = 0; i < 3; i++) { /* 0, 1, 2 */ }
for (let i = 3; i >= 1; i--) { /* 3, 2, 1 */ }

// Stepped increments and decrements.
for (let i = 0; i <= 8; i += 2) { /* 0, 2, 4, 6, 8 */ }
for (let i = 9; i >= 1; i -= 2) { /* 9, 7, 5, 3, 1 */ }

// Multiple expressions: comma operator evaluates left then right.
for (let left = 0, right = 4; left < right; left++, right--) {
  console.log(left, right); // 0 4, then 1 3
}

// Initialization can be outside the header.
let count = 0;
for (; count < 3; count++) { console.log(count); }

// Explicitly managed updates are legal but easier to get wrong.
for (let i = 0; i < 3;) {
  console.log(i);
  i++;
}

// An omitted test requires an intentional exit.
for (;;) {
  break;
}
```

The update expression may be any expression, including a function call; do not assume it always increments by one. `i++` returns the *old* value as an expression while also incrementing the variable; `++i` returns the *new* value. When the result is unused in a `for` update clause, both usually have the same effect on a numeric counter. `i += 2` is an assignment expression, not the same operation as `i++`.

**Empty statement trap:** `for (let i = 0; i < 3; i++);` has an empty body because of the semicolon. A following `{ ... }` block is *not* the loop body. Always use braces for multi-statement bodies and for teaching clarity.

**Header grammar caveats:** `const` requires an initializer in an ordinary `for` declaration and cannot be incremented or reassigned. A classic `for` can use `let`, `var`, or expressions in its initializer; `for...of` and `for...in` use different grammar and are separate statements. JavaScript allows an empty body and optional braces for a single statement, but braces are safer.

## 2.2 Scope: `let`, `var`, `const`, shadowing

```js
for (let row = 1; row <= 2; row++) {
  for (let col = 1; col <= 2; col++) {
    console.log(row, col);
  }
  // console.log(col); // ReferenceError: col is out of scope.
}
// console.log(row); // ReferenceError: row is out of scope.
```

`let` is block-scoped and creates a fresh binding for each iteration of a `for` loop declared with `let` in the header. This matters especially when functions capture the counter (full closure explanation in Functions).

```js
for (var row = 1; row <= 2; row++) {
  for (var col = 1; col <= 2; col++) {}
}
console.log(row, col); // 3 3 in this example
```

`var` is function-scoped (or global-script scoped), not block-scoped; it can therefore leak beyond the loop block. Avoid using it for new nested-loop counters. `const` is useful for values that must not be reassigned *inside* a loop body:

```js
for (let row = 1; row <= 2; row++) {
  const label = `Row ${row}`;
  console.log(label);
}
```

`const` prevents reassignment of the binding, not mutation of an object referenced by it. `let`/`const` have a temporal dead zone before initialization. Inner declarations may shadow outer identifiers:

```js
let position = 100;
for (let position = 1; position <= 2; position++) {
  console.log(position); // 1, then 2
}
console.log(position); // 100
```

Shadowing is legal in nested blocks, but using different names for different dimensions makes reasoning easier. Re-declaring the same `let` in the *same* lexical scope is a syntax error. The exact visibility of declarations also depends on whether the loop body uses braces and whether the declaration occurs in a header or nested block.

## 2.3 Accumulator placement and reset semantics

**Business requirement:** A hotel has 2 floors and 3 rooms per floor. Count rooms per floor and across the building.

**Algorithm:** Initialize the building total once before the outer loop. Initialize the floor total once at the start of each outer iteration. Increase both totals for each room.

```js
let buildingTotal = 0;

for (let floor = 1; floor <= 2; floor++) {
  let floorTotal = 0;

  for (let room = 1; room <= 3; room++) {
    floorTotal++;
    buildingTotal++;
  }

  console.log(`Floor ${floor}: ${floorTotal} rooms`);
}

console.log(`Building total: ${buildingTotal} rooms`);
```

**Expected output:** `Floor 1: 3 rooms`, `Floor 2: 3 rooms`, `Building total: 6 rooms`.

**Line-by-line:** `buildingTotal = 0` runs once; `floorTotal = 0` runs once *per floor*. Each `++` adds one after a room is visited. The inner closing brace finishes room processing. The floor log occurs after that floor's inner loop, so it reports the completed count. The final log occurs after all floors. Moving `buildingTotal = 0` inside the outer loop would lose earlier floor totals. Moving `floorTotal = 0` outside the outer loop would make it cumulative rather than per-floor.

**Challenge:** Change the example to 4 floors with 5 rooms each; predict the final total and every floor total before running it.

## 2.4 Bounds, floating point and termination

- `<` excludes the bound; `<=` includes it. For 0-based indices `0..n-1`, prefer `i < n`. For 1-based business identifiers `1..n`, `i <= n` can be appropriate.
- A condition can be false immediately, producing zero body executions.
- The update must make progress toward termination, or another guaranteed exit must exist.
- Negative and fractional counts need explicit business validation; a loop that checks `i <= 2.5` from 1 will still execute for 1 and 2, but this does not mean â€œ2.5 departmentsâ€ is valid.
- Avoid fractional repeated addition for loop termination because binary floating-point rounding can cause surprising comparisons. Prefer integer counters and derive fractional values from them.
- Very large integer counters can lose precision above `Number.MAX_SAFE_INTEGER`. BigInt counters are possible when operations consistently use BigInt, e.g. `1n`, but are rarely appropriate for ordinary array indexing.
- If a condition mutates state or calls a function, it is evaluated each time and may have side effects. Keep ordinary loop conditions simple.

---

# Part 3 â€” Nested control flow and loop variations

## 3.1 Conditions inside nested loops

**Business requirement:** A warehouse processes 2 aisles, each with 4 shelves. Shelves with even numbers require an extra inspection label. **Output:** Every aisle/shelf pair with its inspection classification.

**Recognition:** Two dimensions plus classification of each combination. **Pseudocode:** For each aisle, for each shelf, check divisibility by 2 and display the classification.

```js
for (let aisle = 1; aisle <= 2; aisle++) {
  for (let shelf = 1; shelf <= 4; shelf++) {
    if (shelf % 2 === 0) {
      console.log(`Aisle ${aisle}, Shelf ${shelf}: EXTRA`);
    } else {
      console.log(`Aisle ${aisle}, Shelf ${shelf}: STANDARD`);
    }
  }
}
```

`%` computes the remainder, `===` is strict equality, and `if` chooses a branch based on a truthy/falsy condition. The loop still visits every pair; the condition changes the action, not the number of iterations. Output for each aisle is STANDARD, EXTRA, STANDARD, EXTRA.

`switch` is an alternative for several discrete cases. Logical operators `&&`, `||`, `!`, and `??` have different semantics; `&&` and `||` short-circuit, and `??` checks only nullish values. Full operator semantics belong to Conditions, but nested loops can use them in conditions.

## 3.2 `continue` vs `break`

```js
for (let row = 1; row <= 2; row++) {
  for (let col = 1; col <= 4; col++) {
    if (col === 2) continue; // Skip just this inner iteration.
    if (col === 4) break;    // Exit this inner loop.
    console.log(row, col);
  }
}
// 1 1, 1 3, 2 1, 2 3
```

**Execution detail:** `continue` targeting the inner classic `for` runs its update then checks its condition again. `break` exits the inner loop without running that loop's update again. The outer loop continues normally after the inner loop ends.

## 3.3 Labeled statements

JavaScript supports labels as identifiers followed by a colon. A labeled `break` can exit an outer labeled statement; labeled `continue` must target an enclosing iteration statement.

**Business requirement:** Search a 3-by-3 grid for the first forbidden coordinate, then stop *all* searching.

```js
outerSearch: for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    if (row === 2 && col === 2) {
      console.log('Found at', row, col);
      break outerSearch;
    }
    console.log('Checked', row, col);
  }
}
```

The label `outerSearch:` identifies the outer statement. `break outerSearch` exits both loops because it exits the outer statement itself. Without the label, plain `break` would exit only the inner loop. Labels are standardized, but can reduce readability if overused; consider a function with `return` when functions have been mastered.

```js
nextRow: for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    if (row === 2 && col === 2) continue nextRow;
    console.log(row, col);
  }
}
```

`continue nextRow` skips the remainder of the current outer iteration, then performs the outer `for` update. `break` can target enclosing labeled statements; `continue` cannot target a label on a non-loop statement. A label is not a variable or a function name.

## 3.4 `return`, `throw`, `try` and `finally`

Inside a function, `return` exits the function, thereby stopping any loops in it. `throw` abruptly exits normal execution until handled by a matching `catch` (or propagates outward). `finally` executes on exits from its `try`, including many `break`, `continue`, `return`, and thrown-error paths; a control transfer inside `finally` can override an earlier one. These are language-level control-flow effects, not special nested-loop features.

```js
function findPair() {
  for (let row = 1; row <= 3; row++) {
    for (let col = 1; col <= 3; col++) {
      if (row === 2 && col === 3) return `${row},${col}`;
    }
  }
  return null;
}
console.log(findPair()); // 2,3
```

This function example previews `return`; functions and their complete API surface will be mastered in Phase 2.

## 3.5 Mixing loop forms

A `for` can contain `while`, `do...while`, `for...of`, or `for...in`, and vice versa. The same nesting principle applies, but iteration semantics differ:

- `while`: checks before each iteration; updates must be written in the body or other executed expressions.
- `do...while`: executes its body at least once before checking.
- `for...of`: reads values from an iterable, using the iteration protocol; `break` normally triggers iterator closing when supported.
- `for...in`: enumerates enumerable string property keys (including inherited ones) rather than array values; not the preferred way to iterate array elements.
- `for await...of`: async iteration, legal only in appropriate async contexts; covered in Phase 4.

These are *related* loop forms, not interchangeable syntax variations of the classic `for` header.

---

# Part 4 â€” Iteration patterns and algorithms

## 4.1 Rectangular iteration (Cartesian product)

**Question:** A delivery business operates 3 depots and 2 routes per depot. Generate every depot-route assignment. **Inputs:** 3, 2. **Rule:** Every route is considered for every depot. **Output:** Six pairs.

```js
for (let depot = 1; depot <= 3; depot++) {
  for (let route = 1; route <= 2; route++) {
    console.log(depot, route);
  }
}
```

Trace: `(1,1), (1,2), (2,1), (2,2), (3,1), (3,2)`. This is a Cartesian product of two numeric ranges. Count = `n*m` if both bounds are fixed and nonnegative. Do not use a Cartesian product when the requirement calls for *corresponding* pairs only.

## 4.2 Dependent bounds and triangular iteration

**Question:** A training coordinator must schedule each new cohort to meet every earlier cohort, but no cohort meets itself and no pair is repeated. **Input:** 4 cohorts. **Output:** Six unique meetings.

**Recognition:** â€œEvery unique pair, no self-pairs, no duplicatesâ€ means the inner loop should begin *after* the current outer index.

**Pseudocode:** For first cohort from 1 to 4, for second cohort from first+1 to 4, record pair.

```js
for (let first = 1; first <= 4; first++) {
  for (let second = first + 1; second <= 4; second++) {
    console.log(first, second);
  }
}
```

Output: `1 2`, `1 3`, `1 4`, `2 3`, `2 4`, `3 4`.

**Line-by-line:** The outer `first` iterates 1 through 4. The inner initialization `second = first + 1` is evaluated anew for each outer iteration. `second <= 4` restricts the partner to valid later cohorts; `second++` advances it. The body logs a unique pair. For `first = 4`, `second` starts at 5, so the inner body executes zero times. Removing `+ 1` creates self-pairs; starting at 1 duplicates symmetric pairs.

Count for `n` cohorts = `n(n-1)/2`, and time is `O(nÂ²)` for this enumeration. An alternative with two fixed loops plus `if (second > first)` works but performs unnecessary checks.

## 4.3 Staircase and triangular output

**Question:** Print a staircase with 4 rows where row 1 has one star and row 4 has four stars. **Output:** Four lines.

```js
for (let row = 1; row <= 4; row++) {
  let line = '';
  for (let col = 1; col <= row; col++) {
    line += '*';
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

`line = ''` resets the accumulator per row; `line += '*'` appends one star per inner iteration; the log is outside the inner loop to print a complete row. If moved inside, it would print each partial row instead. The number of appended stars is `1+2+3+4=10`, generally `n(n+1)/2`. Repeated string concatenation performance depends on engine implementation; for large output, consider an array of pieces and joining (Arrays topic).

## 4.4 Diagonals, grids and coordinate conditions

**Question:** Mark the main diagonal in a 3-by-3 grid with `X`, all other cells with `.`.

```js
for (let row = 1; row <= 3; row++) {
  let line = '';
  for (let col = 1; col <= 3; col++) {
    line += row === col ? 'X' : '.';
  }
  console.log(line);
}
// X..
// .X.
// ..X
```

`row === col` identifies the main diagonal. For a square 0-based grid of side `n`, the anti-diagonal condition is `row + col === n - 1`. The ternary operator `condition ? a : b` selects one of two expressions; full ternary coverage belongs to Conditions.

## 4.5 Reversed, stepped, offset and three-level nesting

```js
// Reverse inner order.
for (let row = 1; row <= 2; row++) {
  for (let col = 3; col >= 1; col--) console.log(row, col);
}

// Only even numbered positions.
for (let row = 1; row <= 2; row++) {
  for (let col = 2; col <= 6; col += 2) console.log(row, col);
}

// Three dimensions: buildings, floors, rooms.
for (let building = 1; building <= 2; building++) {
  for (let floor = 1; floor <= 3; floor++) {
    for (let room = 1; room <= 4; room++) {
      console.log(building, floor, room);
    }
  }
}
```

For fixed independent dimensions, the last example visits `2*3*4=24` combinations. In general, more nesting levels multiply iteration counts when all bounds are independent. With dependent bounds, calculate the total as a sum over the outer iterations, not blindly as a product.

## 4.6 Searching, counting, classification, accumulation and grouping

The same nested structure can support different operations. Recognize the *verb* in the requirement:

| Requirement wording | Operation | Typical state |
|---|---|---|
| â€œHow many qualify?â€ | Count | Integer counter |
| â€œWhat is the total amount?â€ | Accumulate | Numeric total |
| â€œFind the first matchâ€ | Search and early exit | Found value / flag |
| â€œList all matchesâ€ | Filter | Output collection or immediate log |
| â€œAssign each to a categoryâ€ | Classify | Condition and category |
| â€œTotal per branchâ€ | Group | Per-branch accumulator reset |
| â€œCompare each pairâ€ | Pairwise comparison | Two indices |
| â€œGenerate every combinationâ€ | Cartesian product | One counter per dimension |

**Important distinction:** Iterating all pairs is not always optimal. If a task only asks whether an ID exists, a `Set` may avoid a nested scan after indexing data. That alternative requires the Arrays/Set topic and should be chosen based on correctness, memory and workload.

---

# Part 5 â€” Complete real-world applications and progressive challenges

## 5.1 Worked example â€” Supermarket stock inspections

**Background:** A supermarket has 3 branches, each with 4 aisles. **Objective:** Count aisles requiring a priority inspection. **Inputs:** Branch numbers 1â€“3, aisle numbers 1â€“4. **Business rule:** An aisle is priority when its number is even; count priority aisles per branch and across all branches. **Constraints:** Every aisle is checked once. **Output:** Per-branch priority count and overall count. **Edge cases:** Zero branches or aisles; future variable aisle counts.

**Interpretation:** Two-dimensional iteration, classification (`aisle % 2 === 0`), a per-branch accumulator and an overall accumulator.

**Pseudocode:** Initialize overall count; for each branch initialize branch count; for each aisle, if even increment both; after inner loop print branch count; after outer loop print overall count.

```js
let overallPriorityCount = 0;

for (let branch = 1; branch <= 3; branch++) {
  let branchPriorityCount = 0;

  for (let aisle = 1; aisle <= 4; aisle++) {
    if (aisle % 2 === 0) {
      branchPriorityCount++;
      overallPriorityCount++;
    }
  }

  console.log(`Branch ${branch}: ${branchPriorityCount} priority aisles`);
}

console.log(`Overall: ${overallPriorityCount} priority aisles`);
```

**Expected output:** Each branch reports 2; overall reports 6.

**Line-by-line deconstruction:** `overallPriorityCount` is initialized once before any branch. Outer `branch` runs 1â€“3. `branchPriorityCount` resets to zero at the start of each branch. Inner `aisle` runs 1â€“4. `% 2` computes the remainder after division by 2; `=== 0` detects even numbers. Only when true do the two `++` expressions change their respective counters. After the inner loop ends, the per-branch `console.log` reports that branch's completed count. After all branches, the final log reports the cumulative total. Removing the inner `if` would count all aisles; moving the per-branch initialization outside would incorrectly accumulate across branches; moving the final log inside the outer loop would report running totals rather than one final result.

**Tests:** 3Ã—4 â†’ 6 priority aisles; 1Ã—1 â†’ 0; 2Ã—2 â†’ 2; 0 branches â†’ 0; 0 aisles â†’ 0. A negative or fractional count must be rejected before iteration in a production implementation.

## 5.2 Worked example â€” Payment reconciliation comparison

**Background:** A finance team receives 3 payment reference IDs and 3 settlement reference IDs. **Objective:** Identify matching references. **Inputs:** `['P10','P20','P30']` and `['P20','P40','P10']`. **Rules:** For each payment, compare with each settlement until the first match; report match or unmatched. **Output:** P10 matched, P20 matched, P30 unmatched. **Edge cases:** Duplicate settlement references, empty collections, case differences, invalid data.

**Recognition:** Cross-list matching; a nested scan is understandable for small collections, though not always optimal for large data.

**Pseudocode:** For each payment set matched false; scan settlements; if equal set true and break inner loop; after scan print status.

```js
const payments = ['P10', 'P20', 'P30'];
const settlements = ['P20', 'P40', 'P10'];

for (let i = 0; i < payments.length; i++) {
  let matched = false;

  for (let j = 0; j < settlements.length; j++) {
    if (payments[i] === settlements[j]) {
      matched = true;
      break;
    }
  }

  console.log(`${payments[i]}: ${matched ? 'MATCHED' : 'UNMATCHED'}`);
}
```

**Expected output:** `P10: MATCHED`, `P20: MATCHED`, `P30: UNMATCHED`.

**Line-by-line deconstruction:** `const payments` and `const settlements` bind two arrays; `.length` gives the number of elements. `i` is the payment index and starts at 0; `i < payments.length` prevents an out-of-bounds access. `matched = false` resets for every payment. `j` is the settlement index and also starts at 0. `payments[i]` and `settlements[j]` retrieve elements by numeric index. Strict equality compares their strings. On a match, the flag becomes true and `break` stops the inner search only. The final template literal uses a ternary to choose the status label. Moving `matched = false` outside the outer loop would let an earlier match contaminate later results.

**Limitations:** This is only a *reference existence* check, not financial reconciliation: a real system must validate amounts, currencies, statuses, duplicates, reversals, timing and one-to-one allocation. For large collections, indexing settlement references using `Set` or `Map` can reduce repeated comparisons, but requires handling duplicates deliberately. Array methods and hash collections receive full treatment in Phase 2.

## 5.3 Worked example â€” Multiplication table

**Question:** Produce a 3-by-4 multiplication table for rows 1â€“3 and columns 1â€“4. **Output:** Three complete lines, each with four products. **Edge cases:** Zero or negative bounds; formatting for large products.

```js
for (let row = 1; row <= 3; row++) {
  let line = '';
  for (let col = 1; col <= 4; col++) {
    line += `${row * col}${col === 4 ? '' : ' '}`;
  }
  console.log(line);
}
// 1 2 3 4
// 2 4 6 8
// 3 6 9 12
```

**Deconstruction:** Outer `row` chooses the multiplicand. `line` resets for each row. Inner `col` chooses the multiplier. `row * col` calculates the product. The inner ternary adds a space except after the final column. `+=` appends text; after the inner loop, `console.log(line)` prints the finished row. If the log were inside, each partial line would print. For large datasets, collecting values and joining them is a readable alternative.

## 5.4 Eight-level progressive practice â€” do not reveal solutions until requested

### Level 1 â€” Recognition
**Scenario:** A hotel has 5 floors, 8 rooms each. **Task:** Identify the two iteration dimensions and explain which should be outer and why. **Deliverable:** Plain-English algorithm and predicted number of room visits. **Assessment:** Correct decomposition and 40 visits.

### Level 2 â€” Basic application
**Scenario:** A bus company has 3 routes with 4 daily departures each. **Task:** Print every route/departure pair in order. **Inputs:** 3 and 4. **Deliverable:** Runnable nested loops, output and trace. **Assessment:** Exactly 12 pairs, no duplicates.

### Level 3 â€” Combined operations
**Scenario:** A warehouse has 4 racks, each with 6 bins. **Task:** Count bins with even bin numbers per rack and overall. **Deliverable:** Two accumulator levels. **Assessment:** Reset placement, correct totals, zero-bound reasoning.

### Level 4 â€” Multiple rules
**Scenario:** A school has 3 grades and 5 tests per grade. Tests 2 and 4 are practical; grade 3 test 4 is canceled. **Task:** Classify every noncanceled test and count practical tests. **Deliverable:** Clear condition order, output and tests. **Assessment:** Correct skip/classification and totals.

### Level 5 â€” Complex control flow
**Scenario:** A facility has 4 buildings, 3 floors each and 5 rooms per floor. **Task:** Visit rooms in descending room order, skip floor 2 of building 3, and stop searching the entire facility at building 4/floor 1/room 3. **Deliverable:** Three-level algorithm and reasoned control-flow choice. **Assessment:** Correct target of skip/exit.

### Level 6 â€” Algorithm design
**Scenario:** A conference has 7 speakers. Each unordered pair must meet once, with no self-meeting. **Task:** Design from scratch, give pseudocode, implement, and calculate number of meetings. **Deliverable:** Pair enumeration and complexity analysis. **Assessment:** Exactly 21 unique pairs.

### Level 7 â€” Debugging and optimization
**Scenario:** A logistics program compares every parcel with every route and repeatedly checks pairs already rejected. **Task:** Identify faulty loop bounds, fix redundant comparisons, estimate complexity, and propose a more efficient lookup design if requirements allow. **Deliverable:** Bug report, corrected code, tests and tradeoffs. **Assessment:** Correctness before optimization.

### Level 8 â€” Independent transfer
**Scenario:** A school examination system must create an invigilation assignment report across campuses, exam days, rooms and available supervisors, while excluding conflicts and recording unmet coverage. **Task:** Decide whether nested loops are appropriate, design data structures, produce pseudocode, implement when prerequisites are available, and test boundary/conflict cases. **Deliverable:** Independent design, implementation and explanation. **Assessment:** Requirements interpretation, correctness, testing, complexity and maintainability. **Note:** Full implementation naturally depends on arrays and objects, so this is a later integration assessment rather than evidence of current-topic mastery alone.

---

# Part 6 â€” Debugging, testing, tracing and performance

## 6.1 Debugging taxonomy

| Problem | Symptom | Root cause / correction |
|---|---|---|
| Off-by-one | Missing final row/column | Compare `<` vs `<=`, starting index and bound |
| Wrong counter updated | Nontermination or skipped rows | Inner update must advance the intended state |
| Counter not reset | Later groups start at wrong value | Initialize inner counter on each outer entry |
| Accumulator misplaced | Wrong per-group totals | Reset group total inside outer body, before inner loop |
| Unwanted `break` | Stops only one dimension | Decide whether plain break, label or return is needed |
| Unwanted `continue` | Skips wrong dimension | Verify the target loop and update behavior |
| Variable shadowing | Unexpected values | Use distinct names and inspect lexical scope |
| Out-of-range access | `undefined` or incorrect result | Use `< collection.length` for 0-based indexing |
| Type coercion | Strange comparisons or concatenation | Validate input types; understand `+` vs numeric operations |
| Excessive output | Console slowdown / memory pressure | Separate computation from logging; sample output |
| Infinite loop | Program hangs | Verify update, condition and guaranteed termination |
| Mutation during traversal | Skipped/duplicated records | Understand index changes; prefer deliberate strategies |

## 6.2 Intentionally faulty program â€” predict before fixing

```js
let total = 0;
for (let branch = 1; branch < 3; branch++) {
  total = 0;
  for (let department = 1; department <= 4; branch++) {
    total++;
  }
}
console.log(total);
```

**Your tasks:** (1) Identify all three important defects. (2) Explain whether this terminates. (3) Rewrite to count all departments in 3 branches. (4) Test 0, 1, 3 and 5 branches. (5) Explain why a timeout or manual interruption might be needed for a truly nonterminating loop. Do not run untrusted potentially infinite loops without a safeguard.

## 6.3 Systematic tracing

Use a trace table with columns: `outer`, `inner`, `outer condition`, `inner condition`, `action`, `updates`, `accumulator`, `next step`. Count *failed* condition checks too. When an unexpected output occurs, insert logs before and after the inner loop, or use a debugger breakpoint and inspect counters at each stop.

In browser developer tools, use the Sources panel to set breakpoints and Step Over/Step Into. `console.log` is useful but can be noisy; `console.table` can display prepared trace records (console support varies). Node.js provides inspector debugging. Never depend solely on logging to prove correctness.

## 6.4 Test design and assertions

**Question:** Verify that a 3-by-4 loop executes exactly 12 times. **Algorithm:** Count body executions and assert expected value.

```js
let visits = 0;
for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 4; col++) {
    visits++;
  }
}
console.assert(visits === 12, `Expected 12 visits, got ${visits}`);
```

`console.assert` reports failed assertions in supporting runtimes; it is not a substitute for a test framework, and its behavior/output varies by environment. Automated tests should also cover: 0Ã—m, nÃ—0, 1Ã—1, rectangular nÃ—m, reversed bounds, negative/fractional inputs (rejected by validation), large values (resource limits), unique-pair counts, early exits and state reset.

**Invariants:** During a normal fixed-bound inner loop, the outer counter remains unchanged. After completing a full inner loop of `m` items, exactly `m` body executions have occurred for that outer iteration. For unique pairs, the invariant `second > first` holds on every body execution. Invariants help prove algorithms rather than only testing examples.

## 6.5 Time and space complexity

| Pattern | Body executions | Time if body O(1) | Auxiliary space |
|---|---:|---|---|
| n outer Ã— m inner | nÂ·m | O(nm) | O(1) |
| n Ã— n | nÂ² | O(nÂ²) | O(1) |
| 1 through n inner | n(n+1)/2 | O(nÂ²) | O(1) |
| unique unordered pairs | n(nâˆ’1)/2 | O(nÂ²) | O(1) |
| n Ã— m Ã— k | nÂ·mÂ·k | O(nmk) | O(1) |
| early-exit search | varies | O(nm) worst case | O(1) |

These space bounds describe loop bookkeeping only; storing all outputs requires additional space proportional to stored results. If the inner body itself takes O(k), multiply accordingly. Nested loops are **not always quadratic**: the bounds, dependent ranges and body cost determine complexity. Conversely, two sequential loops of lengths n and m have O(n+m), not O(nm).

**Optimization order:** (1) Verify correctness. (2) Measure real workload. (3) Eliminate unnecessary comparisons. (4) Use early exit when semantically correct. (5) Consider indexing (`Map`/`Set`) when repeated membership searches dominate. (6) Re-test edge cases and memory usage. Microbenchmarks can mislead due to warm-up, JIT optimization, I/O and engine differences.

---

# Part 7 â€” Advanced connections, integration and mastery assessment

## 7.1 Nested arrays and matrices â€” preview

```js
const matrix = [
  [10, 20],
  [30, 40],
];
for (let row = 0; row < matrix.length; row++) {
  for (let col = 0; col < matrix[row].length; col++) {
    console.log(matrix[row][col]);
  }
}
```

The outer index selects a row; the inner index selects a cell within that row. `matrix[row].length` allows *jagged* rows of different lengths. Indexing, array mutation, sparse arrays, iterators, and all standardized array methods belong to the Arrays topic. Avoid assuming every row has equal length or even that every element is an array unless the data is validated.

## 7.2 Closures and captured counters â€” preview

```js
const tasks = [];
for (let i = 0; i < 3; i++) {
  tasks.push(() => i);
}
console.log(tasks.map(task => task())); // [0, 1, 2]
```

With `let` in a classic `for` header, each iteration has its own captured binding. With `var`, callbacks can share one function-scoped binding and later see its final value. This difference becomes especially important when nesting loops and scheduling callbacks. Arrow functions, closures, `.push()` and `.map()` are preview syntax here; full treatment belongs to Functions and Arrays.

## 7.3 Async nesting and concurrency â€” preview

```js
async function processGroups(groups) {
  for (const group of groups) {
    for (const item of group) {
      await processItem(item); // Assumes processItem is defined elsewhere.
    }
  }
}
```

This processes items sequentially, awaiting each result. Nested `for` loops alone do not create concurrency. `Promise.all` can run suitable independent operations concurrently, but changes error behavior, resource usage and ordering guarantees. Cancellation, retries, rate limits, microtasks and network requests belong to Phase 4. Never use `forEach(async ...)` as if it automatically awaited all callbacks.

## 7.4 DOM rendering and accessibility â€” preview

Nested loops can construct rows and cells in an HTML table or grid, but a real interface must use correct DOM APIs, safe text insertion (avoid injecting untrusted HTML), appropriate table semantics, labels and keyboard-accessible controls. DOM construction, events, re-rendering and state management belong to Phase 3.

## 7.5 Recursion, backtracking, graphs, indexing â€” preview

- Recursion can represent variable-depth nesting where the number of dimensions is not known in advance; it introduces call-stack and termination concerns.
- Backtracking explores combinations with constraints and may prune branches earlier than exhaustive nested enumeration.
- Graph algorithms use adjacency relationships and visited-state tracking; naive nested loops are not automatically appropriate.
- `Map` and `Set` can replace repeated linear searches with indexed membership operations in many cases, trading memory and preprocessing for faster expected lookup.
- Vectorization, database joins and SQL queries may be preferable to transferring large datasets into JavaScript and scanning all pairs.

These are related algorithms and APIs, not missing syntax forms of nested `for` loops.

## 7.6 Independent mastery examination (solutions intentionally withheld)

**Section A â€” Recognition (15 points):** For each requirement, decide between one loop, nested loops, indexed lookup or another approach; justify your decision: (1) every seat in every bus, (2) sum one sales list, (3) first matching payment reference in each branch, (4) all unique pairs of employees, (5) a direct ID lookup.

**Section B â€” Tracing (15 points):** Without running, list exact outputs and condition-check counts:

```js
for (let x = 2; x <= 4; x++) {
  for (let y = x - 1; y < 4; y += 2) {
    console.log(x, y);
  }
}
```

**Section C â€” Algorithm design (15 points):** A delivery company has 4 zones, each with 6 stops. Every third stop requires a security check. Write inputs, outputs, business rules, pseudocode and invariants before code.

**Section D â€” Implementation (20 points):** Implement Section C with per-zone and overall security-check totals; validate configurable counts as nonnegative integers. Explain every line and the placement of each accumulator.

**Section E â€” Debugging (15 points):** Correct this program and explain every defect:

```js
let count = 0;
for (let row = 1; row < 4; row++) {
  count = 0;
  for (let col = 1; col <= 3; row++) {
    if (col === 2) continue;
    count++;
  }
}
console.log(count);
```

**Section F â€” Testing (10 points):** Supply normal, zero, one-element, boundary, invalid-type, negative, fractional and large-input tests; state expected behavior.

**Section G â€” Performance and transfer (10 points):** Compare a Cartesian-product scan, unique-pair scan and membership search. Give iteration counts, asymptotic complexity, and one justified alternative.

**Pass threshold:** At least 85/100 **and** no major weakness in execution order, termination, counter scope, algorithm selection or testing. The instructor should assess explanations and code, not just final outputs. Correct answers and complete worked solutions should be provided only after your attempt or explicit request.

---

# Consolidated reference and review checklist

## Syntax and semantic checklist

- [ ] I can explain the complete classic `for` header and its optional sections.
- [ ] I can distinguish initialization, test, body and update, including abrupt exits.
- [ ] I can trace nested iteration and reinitialization precisely.
- [ ] I can explain `<` vs `<=`, 0-based vs 1-based bounds, steps and descending loops.
- [ ] I can explain comma expressions, multiple counters and omitted clauses.
- [ ] I can explain `let`, `var`, `const`, block scope, shadowing and captured bindings at the appropriate level.
- [ ] I can position counters and accumulators correctly.
- [ ] I can explain `break`, `continue`, labeled targets and their effects on updates.
- [ ] I can recognize rectangular, triangular, unique-pair, diagonal, staircase and three-dimensional patterns.
- [ ] I can distinguish counting, summing, searching, matching, classifying and grouping.
- [ ] I can trace false condition checks and diagnose infinite loops.
- [ ] I can write tests, state invariants and analyze time/auxiliary-space complexity.
- [ ] I can justify when a nested loop is appropriate and when indexing or another algorithm is better.
- [ ] I can identify which related APIs need full study in later topics.

## Authoritative references

1. ECMAScript specification â€” iteration statements, lexical declarations, completion records, labels: https://tc39.es/ecma262/
2. MDN â€” `for`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for
3. MDN â€” `break`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/break
4. MDN â€” `continue`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/continue
5. MDN â€” `let`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let
6. MDN â€” `var`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var
7. MDN â€” `for...of`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of
8. MDN â€” loops and iteration guide: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration

**Completion status:** All listed nested-`for` capability families have been addressed or explicitly scheduled for prerequisite-dependent later modules. Reading this document is *coverage*, not demonstrated mastery. Work through the independent challenges before moving on.
