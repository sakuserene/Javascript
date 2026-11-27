# Lesson 12 - Managing State in Loops

*JavaScript Plain Loops*

Previous: [Common Loop Patterns](11-common-loop-patterns.md) | Curriculum: Plain Loops | Next: Looping Until a Condition Is Met

## Table of contents

1. [Understanding state](#1-understanding-state)
2. [Temporary variables](#2-temporary-variables)
3. [Persistent state](#3-persistent-state)
4. [Tracking previous values](#4-tracking-previous-values)
5. [Managing multiple variables](#5-managing-multiple-variables)
6. [Recurrence relations](#6-recurrence-relations)
7. [Resetting state](#7-resetting-state)
8. [State transitions](#8-state-transitions)
9. [Combining state management techniques](#9-combining-state-management-techniques)
10. [Summary](#10-summary)
11. [Integrated exercises](#11-integrated-exercises)

## 1. Understanding state

In the previous chapter, we learned how to solve problems using common loop patterns such as counting, accumulation, searching and filtering.

Many of these patterns depend on remembering information from previous iterations.

Suppose a shop records daily sales of 100, 200 and 150. Calculate cumulative sales after each day and the final total.

```javascript
const sales = [100, 200, 150];
let total = 0;
for (let i = 0; i < sales.length; i++) {
  total += sales[i];
  console.log("Day " + (i + 1) + ":", total);
}
console.log("Final total:", total);
```

Expected output:

```text
Day 1: 100
Day 2: 300
Day 3: 450
Final total: 450
```

The variable `total` represents the combined sales of every day processed so far. The information maintained by a program at a particular stage of execution is called **state**.

| Stage | Previous total | Current sale | Updated total |
|---|---:|---:|---:|
| Initialization | - | - | 0 |
| Day 1 | 0 | 100 | 100 |
| Day 2 | 100 | 200 | 300 |
| Day 3 | 300 | 150 | 450 |

Managing state means deciding what to remember, how to initialize it, when to update it and when to reset it. Run each complete code example independently unless otherwise stated.

## 2. Temporary variables

Suppose we need to calculate the discounted price of each product and print both the discount and the final price. The discount is 10 percent of the original price.

```javascript
const prices = [100, 200, 300];
for (let i = 0; i < prices.length; i++) {
  const price = prices[i];
  const discount = price * 0.1;
  const finalPrice = price - discount;
  console.log(discount, finalPrice);
}
```

Output:

```text
10 90
20 180
30 270
```

Each calculation depends only on the current product. We do not need yesterday's discount or the preceding product's final price. Variables declared inside the body with `const` or `let` have block scope, and a new binding is created each time the body executes. Here they serve as temporary variables.

| Iteration | price | discount | finalPrice |
|---:|---:|---:|---:|
| 1 | 100 | 10 | 90 |
| 2 | 200 | 20 | 180 |
| 3 | 300 | 30 | 270 |

Naming intermediate calculations makes their meaning explicit and avoids repeating expressions. Use `const` when the binding does not need reassignment during the iteration.

### Avoiding stale temporary values

Suppose only products costing at least 200 receive a discount. Consider this incorrect implementation:

```javascript
const prices = [200, 100, 300];
let discount = 0;
for (let i = 0; i < prices.length; i++) {
  if (prices[i] >= 200) discount = prices[i] * 0.1;
  console.log(prices[i] - discount);
}
```

Output:

```text
180
80
270
```

The second product incorrectly receives the preceding product's discount. The variable retains its old value because the condition is false. We need a fresh discount of zero for each product.

```javascript
const prices = [200, 100, 300];
for (let i = 0; i < prices.length; i++) {
  let discount = 0;
  if (prices[i] >= 200) discount = prices[i] * 0.1;
  console.log(prices[i] - discount);
}
```

Output:

```text
180
100
270
```

The initialization now occurs exactly where its meaning applies: at the beginning of processing each product.

### Practice

1. Given `[2, 3, -2, 0]`, print the square and cube of each number using temporary variables. Expected pairs: `(4, 8), (9, 27), (4, -8), (0, 0)`.
2. Given orders `[600, 200, 500, 100]`, charge 50 for orders strictly below 500 and zero otherwise. Collect final prices. Expected: `[600, 250, 500, 150]`. Test `[100, 600]` -> `[150, 600]` and `[]` -> `[]`.
3. Debug a loop that initializes a discount outside its body and updates it only for qualifying products. With prices `[200, 100]` and a 10 percent discount for prices at least 200, the correct final prices are `[180, 100]`. Explain why the old discount must not survive.

## 3. Persistent state

Unlike a temporary calculation, a running balance must survive from one iteration to the next. Here persistent means retained across iterations, not stored permanently on disk.

Suppose an account starts with 100 and receives changes of 30, -50 and 20. Each new balance depends on the preceding balance.

```javascript
const changes = [30, -50, 20];
let balance = 100;
for (let i = 0; i < changes.length; i++) {
  const change = changes[i];
  balance += change;
  console.log(balance);
}
console.log("Final:", balance);
```

Output:

```text
130
80
100
Final: 100
```

`change` is temporary; `balance` is persistent. Declaring the balance inside the loop would restart it at 100 every time, producing 130, 50 and 120 instead.

### Remembering a past event

Suppose we also need to know whether the balance ever fell below 90. The final balance alone cannot answer this question. We need a flag that remembers a violation even after recovery.

```javascript
const changes = [30, -50, 20];
let balance = 100;
let fellBelow = balance < 90;
for (let i = 0; i < changes.length; i++) {
  balance += changes[i];
  if (balance < 90) fellBelow = true;
}
console.log(balance, fellBelow);
```

Output:

```text
100 true
```

The flag includes the starting balance in its check. If the requirement concerns only balances after transactions, initialize it to false instead. Assigning `fellBelow = balance < 90` on every iteration would forget earlier violations. We cannot break on the first violation because we also need the final balance.

### Practice

1. Start with 50 units and process sales `[10, 25, 5]`. Collect remaining inventory after each sale. Expected: `[40, 15, 10]`.
2. Start with 20 units and apply changes `[-15, 20, -2]`. Report final inventory and whether it ever fell below 10, including the initial quantity. Expected: `23, true`. Test no changes with initial quantity 5 -> `5, true`.
3. Explain why assigning the flag to the current comparison every iteration loses information in exercise 2.

## 4. Tracking previous values

Consider temperatures of 20, 24, 21 and 21. We want to classify each change as an increase, decrease or no change. Comparing a temperature with a fixed threshold cannot answer this question: the comparison target changes every day.

### Accessing the preceding element

When the complete array is available, the preceding element is at index `i - 1`. The first element has no predecessor, so start at index one.

```javascript
const temperatures = [20, 24, 21, 21];
for (let i = 1; i < temperatures.length; i++) {
  const previous = temperatures[i - 1];
  const current = temperatures[i];
  console.log(current - previous);
}
```

Output:

```text
4
-3
0
```

Four observations produce three comparisons. Empty and single-element arrays produce none. Starting at zero would access index -1, which is not an ordinary array element.

### Remembering the previous value

Sometimes values are generated during execution rather than stored in an array. We can maintain a variable containing the preceding observation. The following example uses an array so we can focus on the technique.

```javascript
const temperatures = [20, 24, 21, 21];
let previous = temperatures[0];
for (let i = 1; i < temperatures.length; i++) {
  const current = temperatures[i];
  if (current > previous) console.log("Increase");
  else if (current < previous) console.log("Decrease");
  else console.log("Unchanged");
  previous = current; // (1)
}
```

Output:

```text
Increase
Decrease
Unchanged
```

Line (1) prepares the state for the next iteration, after the comparison has used the old value.

| Current | Previous before | Result | Previous after |
|---:|---:|---|---:|
| 24 | 20 | Increase | 24 |
| 21 | 24 | Decrease | 21 |
| 21 | 21 | Unchanged | 21 |

Moving `previous = current` before the comparison would make every comparison report unchanged. Omitting the assignment would compare every temperature with the first temperature rather than its predecessor.

### Previous accepted value

Suppose negative readings are invalid and should be ignored. For `[20, -1, 24, 21]`, we want changes of 4 and -3, comparing consecutive valid readings. Index `i - 1` no longer necessarily identifies the desired predecessor.

```javascript
const readings = [20, -1, 24, 21];
let previous;
let hasPrevious = false;
for (let i = 0; i < readings.length; i++) {
  const current = readings[i];
  if (current < 0) continue;
  if (hasPrevious) console.log(current - previous);
  previous = current;
  hasPrevious = true;
}
```

Output:

```text
4
-3
```

The flag distinguishes the first accepted observation from subsequent ones. Initializing previous to zero would invent a comparison with a reading that never occurred. Updating previous before skipping invalid readings would also violate the requirement. With no valid readings or only one, no difference is printed.

### Practice

1. Given `[20, 25, 22, 22, 28, 24]`, count adjacent increases, decreases and unchanged values. Expected: `2, 2, 1`. Test `[]` and `[10]` -> `0, 0, 0`; `[5, 5, 5]` -> `0, 0, 2`.
2. Given `[10, 3, 8, 12]`, find the largest absolute adjacent difference. Expected: `7`. Negate a negative difference to obtain its magnitude. Report `No comparison` for fewer than two observations.
3. Ignore negative readings in `[10, -1, 15, 12, -2, 20]`. Collect changes between consecutive accepted readings. Expected: `[5, -3, 8]`. Test `[-1, -2]` -> `[]`; `[0, -1, 3]` -> `[3]`.

## 5. Managing multiple variables

Some results depend on several pieces of state. Consider finding the day with the greatest increase in sales. We need the preceding sale, the greatest increase discovered and its position.

For `[100, 130, 120, 180, 190]`, the changes are 30, -10, 60 and 10. The greatest change is 60 on day four.

```javascript
const sales = [100, 130, 120, 180, 190];
if (sales.length < 2) {
  console.log("No comparison");
} else {
  let previous = sales[0];
  let bestChange = sales[1] - sales[0];
  let bestDay = 2;
  for (let i = 1; i < sales.length; i++) {
    const change = sales[i] - previous;
    if (change > bestChange) {
      bestChange = change;
      bestDay = i + 1;
    }
    previous = sales[i];
  }
  console.log(bestChange, bestDay);
}
```

Output:

```text
60 4
```

The candidate starts with an actual difference, not zero, so the algorithm also works when all changes are negative. In that case it reports the greatest signed change, which is the smallest decline. If the requirement is specifically a positive increase, report no increase when the final candidate is not positive. The strict comparison retains the first tied day.

### Preserving old values during updates

Suppose two variables contain 3 and 8, and we want to exchange their values. The following assignments do not work:

```javascript
let a = 3;
let b = 8;
a = b;
b = a;
console.log(a, b);
```

Output:

```text
8 8
```

The first assignment destroys the original value of a. The second reads the new value. JavaScript executes these assignments sequentially, not simultaneously.

We can preserve the original value in a temporary variable:

```javascript
let a = 3;
let b = 8;
const oldA = a;
a = b;
b = oldA;
console.log(a, b);
```

Output:

```text
8 3
```

The same principle applies inside loops whenever several new state values depend on old state values. Calculate what must be preserved before overwriting it.

### Practice

1. Given `[100, 130, 80, 90, 40]`, find the largest positive decline and its first ending day (one-based). Expected: `50, 3`. For `[1, 2, 3]`, report `No decline`.
2. Start with balance 100 and changes `[-30, 20, -60, 100]`. Report final balance and lowest balance including the initial value. Expected: `130, 30`. Test `[20, 30]` -> `150, 100`.
3. Start with `a = 3` and `b = 8`. Exchange their values three times, printing each pair. Expected: `(8, 3), (3, 8), (8, 3)`. Explain why a temporary variable is needed.

## 6. Recurrence relations

A recurrence relation defines a term or state using one or more preceding terms or states. We already encountered a simple recurrence when generating geometric sequences: the next term equals the current term multiplied by a constant.

### A recurrence using one preceding value

Suppose a population begins at 100 and doubles each period. We want its value after four periods.

```javascript
let population = 100;
for (let period = 1; period <= 4; period++) {
  population *= 2;
  console.log(population);
}
```

Output:

```text
200
400
800
1600
```

The initial value is the population before any period has elapsed. Printing before updating would instead show the populations at the beginning of the four periods. A recurrence needs an initial value and a rule for obtaining the next value.

### A recurrence using two preceding values

The Fibonacci sequence begins with 0 and 1. Each subsequent term is the sum of the preceding two terms.

```text
0, 1, 1, 2, 3, 5, 8, 13
```

One variable is insufficient because calculating the next term requires two values. Let `a` represent the current term and `b` the following term. After emitting a, we want the new a to be the old b and the new b to be the sum of the old a and old b.

Consider the incorrect update `a = b; b = a + b;`. Starting from 0 and 1, it produces 1 and 2 instead of 1 and 1 because the original a has been overwritten.

Calculate the sum first:

```javascript
let a = 0;
let b = 1;
const terms = [];
for (let i = 0; i < 8; i++) {
  terms.push(a);
  const next = a + b;
  a = b;
  b = next;
}
console.log(terms);
```

Output:

```text
[0, 1, 1, 2, 3, 5, 8, 13]
```

| Iteration | Old a | Old b | Emitted | Next | New a | New b |
|---:|---:|---:|---:|---:|---:|---:|
| 1 | 0 | 1 | 0 | 1 | 1 | 1 |
| 2 | 1 | 1 | 1 | 2 | 1 | 2 |
| 3 | 1 | 2 | 1 | 3 | 2 | 3 |
| 4 | 2 | 3 | 2 | 5 | 3 | 5 |
| 5 | 3 | 5 | 3 | 8 | 5 | 8 |

This implementation naturally handles a requested count of zero or one: the loop emits exactly the requested number of terms. Use small counts because JavaScript numbers cannot represent arbitrarily large integers exactly.

### Storing history instead of two variables

If all generated terms must be retained, we can use the array itself as history. The first two terms require special treatment because they have no two predecessors.

```javascript
const count = 8;
const terms = [];
for (let i = 0; i < count; i++) {
  if (i === 0) terms.push(0);
  else if (i === 1) terms.push(1);
  else terms.push(terms[i - 1] + terms[i - 2]);
}
console.log(terms);
```

Output:

```text
[0, 1, 1, 2, 3, 5, 8, 13]
```

If only a final term is needed, retaining two variables avoids storing the entire sequence. If the history is required, either implementation is suitable.

### Practice

1. Generate five terms starting at 2, with each next term three times the preceding term plus one. Expected: `[2, 7, 22, 67, 202]`.
2. Generate ten Fibonacci terms. Expected: `[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]`. Test counts 0, 1 and 2 -> `[]`, `[0]`, `[0, 1]`.
3. Generate six terms starting with 2 and 3; each later term is twice its predecessor plus the term before that. Expected: `[2, 3, 8, 19, 46, 111]`.

## 7. Resetting state

Counting all passing marks and counting consecutive passing marks are different problems. Consider `[60, 70, 30, 80, 90, 55, 40]`. Five marks pass, but the longest uninterrupted group contains three.

A consecutive group of qualifying observations is called a streak. To track a streak, increase its length on a qualifying observation and reset it to zero when the condition fails.

```javascript
const marks = [60, 70, 30, 80, 90, 55, 40];
let current = 0;
for (let i = 0; i < marks.length; i++) {
  if (marks[i] >= 50) current++;
  else current = 0;
  console.log(current);
}
```

Output:

```text
1
2
0
1
2
3
0
```

The current streak describes the consecutive passes ending at the current observation. It does not remember the longest streak once a failure occurs.

### Preserving the best streak

We need a second variable that survives resets of the current streak.

```javascript
const marks = [60, 70, 30, 80, 90, 55, 40];
let current = 0;
let longest = 0;
for (let i = 0; i < marks.length; i++) {
  if (marks[i] >= 50) current++;
  else current = 0;
  if (current > longest) longest = current;
}
console.log(longest);
```

Output:

```text
3
```

| Mark | Current | Longest |
|---:|---:|---:|
| 60 | 1 | 1 |
| 70 | 2 | 2 |
| 30 | 0 | 2 |
| 80 | 1 | 2 |
| 90 | 2 | 2 |
| 55 | 3 | 3 |
| 40 | 0 | 3 |

Updating the best after each observation handles streaks that extend to the end of the array. Updating only when a failure occurs would miss a final uninterrupted streak unless we performed an additional check after the loop.

### Remembering the streak position

If a streak of length three ends at index five, its starting index is `5 - 3 + 1`, or three. We can calculate the start whenever a new best is found.

```javascript
const marks = [60, 70, 30, 80, 90, 55, 40];
let current = 0;
let longest = 0;
let bestStart = -1;
for (let i = 0; i < marks.length; i++) {
  if (marks[i] >= 50) current++;
  else current = 0;
  if (current > longest) {
    longest = current;
    bestStart = i - current + 1;
  }
}
console.log(longest, bestStart);
```

Output:

```text
3 3
```

No qualifying marks leave the length at zero and the start at -1. The strict comparison retains the earliest tied longest streak.

### Resetting a group total

Resets can apply to accumulators too. Suppose zero separates batches of positive sales amounts. We want the total of each nonempty batch. A separator ends the current batch but is not part of it.

```javascript
const sales = [10, 20, 0, 5, 0, 0, 7, 8];
const totals = [];
let total = 0;
let count = 0;
for (let i = 0; i < sales.length; i++) {
  if (sales[i] === 0) {
    if (count > 0) totals.push(total);
    total = 0;
    count = 0;
  } else {
    total += sales[i];
    count++;
  }
}
if (count > 0) totals.push(total);
console.log(totals);
```

Output:

```text
[30, 5, 15]
```

We save the completed total before resetting it. The count distinguishes an empty batch from a nonempty one. The final check saves a batch that ends without a separator. Consecutive separators do not create empty results.

### Practice

1. Find the longest positive streak and its earliest starting index in `[-1, 2, 3, 0, 4, 5, 6]`. Expected: `3, 4`. Test `[-1, 0]` -> `0, -1` and `[1, 2]` -> `2, 0`.
2. Count separate passing streaks (marks at least 50) in `[60, 70, 30, 80, 40, 50]`. Expected: `3`. Test `[50, 60]` -> `1`; `[]` -> `0`.
3. For `[0, 4, 6, 0, 0, 3, 0, 8, 2]`, collect totals and counts of nonempty batches separated by zero. Expected totals: `[10, 3, 10]`; counts: `[2, 1, 2]`. Test `[0, 0]` -> two empty result arrays.

## 8. State transitions

So far, most state has been numerical. State can also represent a situation, such as whether a door is open or closed. An event is an input that may cause the situation to change. A change from one state to another is called a state transition.

Suppose a door receives the commands `open` and `close`. Opening an already open door leaves it open. Closing an already closed door leaves it closed.

| Current state | Event | Next state |
|---|---|---|
| closed | open | open |
| closed | close | closed |
| open | open | open |
| open | close | closed |

This table describes the transition rules before we write code.

```javascript
const events = ["open", "open", "close", "open"];
let state = "closed";
for (let i = 0; i < events.length; i++) {
  if (events[i] === "open") state = "open";
  else if (events[i] === "close") state = "closed";
  console.log(state);
}
```

Output:

```text
open
open
closed
open
```

An event describes what happened or was requested; state describes the current situation. They are not interchangeable. Unknown commands in this implementation leave the state unchanged.

### Transitions that depend on current state

Now suppose a machine has three states: idle, running and paused. It can start only when idle, pause only when running, resume only when paused, and stop when running or paused. Other commands are rejected without changing state.

| Current state | Accepted event | Next state |
|---|---|---|
| idle | start | running |
| running | pause | paused |
| paused | resume | running |
| running or paused | stop | idle |

```javascript
const events = ["pause", "start", "pause", "resume", "stop", "resume"];
let state = "idle";
let rejected = 0;
for (let i = 0; i < events.length; i++) {
  const event = events[i];
  if (state === "idle" && event === "start") {
    state = "running";
  } else if (state === "running" && event === "pause") {
    state = "paused";
  } else if (state === "paused" && event === "resume") {
    state = "running";
  } else if ((state === "running" || state === "paused") && event === "stop") {
    state = "idle";
  } else {
    rejected++;
  }
  console.log(state);
}
console.log("Rejected:", rejected);
```

Output:

```text
idle
running
paused
running
idle
idle
Rejected: 2
```

The same event may be accepted or rejected depending on the current state. This is a simple finite-state machine: a model with a finite collection of possible states and rules for moving between them.

### Avoiding multiple transitions for one event

Consider a toggle command that changes closed to open and open to closed. Two independent conditions can cause an error:

```javascript
let state = "closed";
if (state === "closed") state = "open";
if (state === "open") state = "closed";
console.log(state);
```

Output:

```text
closed
```

The second condition sees the updated state and reverses the first transition. Use an `if...else` structure when exactly one transition should occur per event.

```javascript
const events = ["toggle", "toggle", "toggle"];
let state = "closed";
for (let i = 0; i < events.length; i++) {
  if (events[i] !== "toggle") continue;
  if (state === "closed") state = "open";
  else state = "closed";
  console.log(state);
}
```

Output:

```text
open
closed
open
```

### Practice

1. A light starts off. Process `["on", "toggle", "unknown", "toggle", "off"]`. Print state after every command, ignoring unknown commands. Expected: `["on", "off", "off", "on", "off"]`.
2. A session starts logged out. Login is accepted only when logged out, logout only when logged in. Process `["logout", "login", "login", "logout"]`. Expected final state: logged out; rejected: 2.
3. Extend the machine example to count successful starts and stops. Input `["start", "start", "pause", "stop", "stop"]`. Expected starts: 1; stops: 1; rejected: 2; final state: idle. Empty input -> all counts zero, idle.

## 9. Combining state management techniques

Suppose we monitor daily sales against a target of 100. We need the final total, the number of increases between adjacent days, the longest streak of days meeting the target and the starting day of that streak. Assume ordinary finite nonnegative sales values.

The total must survive every iteration. The previous sale is updated after comparison. The current streak resets on a missed target, while the best streak survives those resets. A flag prevents comparing the first day with an invented predecessor.

```javascript
const sales = [80, 120, 130, 70, 100, 110, 150];
let total = 0;
let previous;
let hasPrevious = false;
let increases = 0;
let current = 0;
let longest = 0;
let bestDay = -1;
for (let i = 0; i < sales.length; i++) {
  const sale = sales[i];
  total += sale;
  if (hasPrevious && sale > previous) increases++;
  previous = sale;
  hasPrevious = true;
  if (sale >= 100) current++;
  else current = 0;
  if (current > longest) {
    longest = current;
    bestDay = i - current + 2;
  }
}
console.log(total, increases, longest, bestDay);
```

Output:

```text
760 5 3 5
```

| Day | Sale | Total | Increases | Current | Longest | Best day |
|---:|---:|---:|---:|---:|---:|---:|
| 1 | 80 | 80 | 0 | 0 | 0 | -1 |
| 2 | 120 | 200 | 1 | 1 | 1 | 2 |
| 3 | 130 | 330 | 2 | 2 | 2 | 2 |
| 4 | 70 | 400 | 2 | 0 | 2 | 2 |
| 5 | 100 | 500 | 3 | 1 | 2 | 2 |
| 6 | 110 | 610 | 4 | 2 | 2 | 2 |
| 7 | 150 | 760 | 5 | 3 | 3 | 5 |

The expression `i - current + 2` converts the starting index into a one-based day number. Empty input produces zero total, zero increases, zero longest streak and -1 for the starting day. A single observation cannot produce an increase. A sale of exactly 100 qualifies.

We could separate these calculations into independent loops. That would simplify each loop but repeat traversal. Combining them is useful when the maintained variables have clear, independent meanings. Do not combine loops merely to reduce their number if the resulting state becomes difficult to reason about.

## 10. Summary

| Technique | Main responsibility |
|---|---|
| Temporary variables | Represent information needed only during the current iteration |
| Persistent state | Retain information across iterations |
| Previous-value tracking | Compare an observation with an earlier observation |
| Multiple variables | Maintain several related results without destroying needed old values |
| Recurrence relations | Derive new terms from preceding terms |
| Resetting state | Begin a new group or streak without losing required historical results |
| State transitions | Change a situation according to events and transition rules |

For every maintained variable, establish what it represents before processing, after an iteration and at termination. Pay particular attention to update order, first observations, resets and empty input.

## 11. Integrated exercises

### Exercise 1 - Temperature report

Given `[20, 22, 19, 19, 25, 24]`, count adjacent increases, decreases and unchanged observations. Find the greatest signed increase and longest streak of temperatures at least 20.

Expected: increases 2, decreases 2, unchanged 1, greatest increase 6, longest streak 2. For empty input, counts and streak are zero and no comparison exists.

### Exercise 2 - Inventory report

Start with 50 units and apply `[10, -30, -25, 20, -15]`. Allow negative inventory to represent a shortage. Record balances, minimum (including initial balance) and whether inventory ever fell below zero.

Expected balances: `[60, 30, 5, 25, 10]`; minimum 5; shortage false. Additional case: initial 5, changes `[-10, 20]` -> balances `[-5, 15]`, minimum -5, shortage true.

### Exercise 3 - Sequence analysis

Generate 12 Fibonacci terms, calculate their total and find the first term strictly above 20. Expected total: 232; first qualifying term: 21. For zero terms, total is zero and no term qualifies; for one term, total is zero and no term qualifies.

### Exercise 4 - Reading validation

Ignore negative readings in `[10, -1, 15, 12, -2, 20]`. Collect differences between consecutive accepted readings and count increases. Expected differences: `[5, -3, 8]`; increases: 2. Test `[-1, -2]` -> empty differences, zero increases.

### Exercise 5 - Streak report

For `[1, 1, 0, 1, 1, 1, 0, 1]`, find the number of streaks of ones, longest length and earliest starting index of a longest streak. Expected: `3, 3, 3`. Test `[0, 0]` -> `0, 0, -1`; `[1, 0, 1]` -> `2, 1, 0`.

### Exercise 6 - Batch report

Given `[4, 6, 0, 0, 3, 7, 0, 8]`, calculate averages of nonempty batches separated by zero. Expected: `[5, 5, 8]`. Test `[0, 0]` -> `[]`; `[2, 4]` -> `[3]`.

### Exercise 7 - Machine report

Use the machine transition rules from section 8. Process `["pause", "start", "pause", "resume", "stop", "resume"]`. Collect the state after every command, count accepted and rejected commands and report whether the machine ever entered paused state.

Expected states: `["idle", "running", "paused", "running", "idle", "idle"]`; accepted 4; rejected 2; ever paused true. Empty input: empty history, zero counts, false.

### Exercise 8 - Independent sales analysis

Given `[90, 110, 120, 0, 130, 140]`, treat zero as a valid sale. Calculate total, adjacent increases, longest streak of sales at least 100 and earliest starting day (one-based). Expected: total 590, increases 4, longest 2, starting day 2. Test `[100]` -> `100, 0, 1, 1`.

For each exercise, derive the required state and update rules before coding. Trace a small input and test relevant boundary cases. Solutions are intentionally omitted.

---

Previous: [Common Loop Patterns](11-common-loop-patterns.md) | Curriculum: Plain Loops | Next: Looping Until a Condition Is Met
