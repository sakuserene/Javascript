# Lesson 14 - Infinite Loops and Loop Termination

[Previous: Looping Until a Condition Is Met](13-looping-until-a-condition-is-met.md) | [Next: Nested Loops (planned)](15-nested-loops.md)

## Contents

1. [Understanding infinite loops](#1-understanding-infinite-loops)
2. [Intentionally indefinite loops](#2-intentionally-indefinite-loops)
3. [Accidental infinite loops](#3-accidental-infinite-loops)
4. [Ensuring loop termination](#4-ensuring-loop-termination)
5. [Blocking JavaScript execution](#5-blocking-javascript-execution)
6. [Developing reliable loop implementations](#6-developing-reliable-loop-implementations)
7. [Summary](#7-summary)
8. [Integrated exercises](#8-integrated-exercises)

## 1. Understanding infinite loops

A loop terminates when its condition becomes false or an exit such as break is executed. Consider counting from one to five.

```javascript
let number = 1;
while (number <= 5) {
  console.log(number);
  number++;
}
console.log("Finished");
```

Output: 1, 2, 3, 4, 5, Finished, on separate lines. Removing the update leaves number equal to one forever. An infinite loop continues indefinitely under its execution conditions. A long but finite loop is different: counting to one million has a reachable stopping boundary.

## 2. Intentionally indefinite loops

A permanently true header does not imply an infinite execution. Start with 10 and add 25 until at least 100.

```javascript
let amount = 10;
while (true) {
  amount += 25;
  console.log(amount);
  if (amount >= 100) break;
}
```

Output: 35, 60, 85, 110. If the initial amount is 120, this implementation unnecessarily adds 25. Move the check before the addition to allow zero iterations. An ordinary while (amount < 100) is simpler here. The omitted condition in for (;;) also behaves as true.

### Several exit points

Process [20, -5, 30, 50, -1, 100], skipping nonpositive values except -1, which terminates processing. Stop at total 100 or exhaustion.

```javascript
const transactions = [20, -5, 30, 50, -1, 100];
let i = 0;
let total = 0;
let accepted = 0;
while (true) {
  if (i >= transactions.length) break;
  const amount = transactions[i];
  i++;
  if (amount === -1) break;
  if (amount <= 0) continue;
  total += amount;
  accepted++;
  if (total >= 100) break;
}
console.log(total, i, accepted);
```

Output: 100 4 3. Check availability before reading, sentinel before accumulation, and threshold after accumulation. Advance the index before continue.

Practice: print multiples of seven from 7 through 35 using break. Then process [10, -5, 20, -1, 100] under the same transaction rules; expect total 30, examined 4, accepted 2, sentinel termination.

## 3. Accidental infinite loops

Do not execute incorrect snippets in this section. Trace them manually.

### Missing updates and wrong direction

A loop with number = 2 and condition number <= 10 must increase number by two to print 2, 4, 6, 8, 10. Without the update it repeats two. For a countdown from five to one, number++ moves away from the boundary; use number--.

### Unreachable equality

Starting at zero and adding four produces 0, 4, 8, 12, ... . A condition number !== 10 never reaches its intended exit. If crossing is permitted, use number < 10 and finish at 12. If exact completion is required and smaller steps are allowed, reduce the last step.

```javascript
let number = 0;
while (number < 10) {
  let step = 4;
  if (number + step > 10) step = 10 - number;
  number += step;
}
console.log(number);
```

Output: 10.

### Updates skipped by continue

Incorrect:

```javascript
const numbers = [5, -2, 8];
let i = 0;
while (i < numbers.length) {
  if (numbers[i] <= 0) continue;
  console.log(numbers[i]);
  i++;
}
```

After printing five, i remains one because continue skips the update. Correct by reading the value and advancing first:

```javascript
const numbers = [5, -2, 8];
let i = 0;
while (i < numbers.length) {
  const number = numbers[i];
  i++;
  if (number <= 0) continue;
  console.log(number);
}
```

Output: 5, 8. Alternatively, use an if block without continue, or a for loop whose update expression executes after continue.

### Wrong variable, reset and shadowing

If remaining controls a countdown, updating total instead does not change the condition. Resetting count to zero on each iteration also prevents cumulative progress. Declaring let i inside the body shadows an outer i; updating the inner binding does not update the condition's binding. Initialize persistent state outside the loop and update the correct binding.

### Moving boundaries

Appending to the same array while traversing its changing length can prevent the index from catching the boundary. The incorrect header for (let i = 0; i < numbers.length; i++) combined with numbers.push(numbers[i] * 2) increases both index and length. Eventually resource or implementation limits may interrupt execution. Save the original length when only original elements should be processed.

```javascript
const numbers = [1, 2, 3];
const originalLength = numbers.length;
for (let i = 0; i < originalLength; i++) {
  numbers.push(numbers[i] * 2);
}
console.log(numbers);
```

Output: [1, 2, 3, 2, 4, 6].

### Assignment instead of comparison

while (running = true) assigns true every time the condition is checked. Setting running to false inside the body cannot terminate it. Use while (running) to test the existing Boolean value.

### Cyclic states

Alternating between on and off never produces finished. If four transitions are required, count transitions independently.

```javascript
let state = "on";
let transitions = 0;
while (transitions < 4) {
  if (state === "on") state = "off";
  else state = "on";
  transitions++;
  console.log(state);
}
```

Output: off, on, off, on.

Practice: correct a countdown starting at ten with i++; correct amount = 5 with amount !== 20 and amount += 4 to reach or exceed 20 (21); filter [0, 5, 0, 8] without skipping the index update (5, 8); append modified copies of [2, 4, 6] to produce [2, 4, 6, 3, 5, 7].

## 4. Ensuring loop termination

A useful way to reason about termination is to identify a nonnegative integer quantity that decreases on every continuing iteration. For a traversal, length - i is the number of remaining elements. It begins finite, decreases by one, and cannot decrease indefinitely while remaining nonnegative. This is a progress measure. A changing variable alone is not enough: a cyclic state changes without approaching completion.

### Progress on every path

Process [0, -2, 5, 8], accepting positives. The index must advance for invalid and valid values alike.

```javascript
const values = [0, -2, 5, 8];
let i = 0;
let total = 0;
while (i < values.length) {
  const value = values[i];
  i++;
  if (value <= 0) continue;
  total += value;
}
console.log(total, i);
```

Output: 13 4. Remaining elements decrease from four to zero. The total need not change on every iteration; the index does.

### Validate assumptions before repetition

A fixed positive integer deposit can reach a higher small integer target. Zero or negative deposits cannot. Check initial success before rejecting the update.

```javascript
let savings = 20;
const target = 100;
const deposit = 0;
if (savings >= target) {
  console.log("Already reached");
} else if (deposit <= 0) {
  console.log("Cannot reach target");
} else {
  while (savings < target) savings += deposit;
  console.log(savings);
}
```

Output: Cannot reach target. This reasoning assumes small finite integers.

### Iteration limits and unsuccessful completion

Start at zero and move halfway toward ten, with tolerance 0.1 and at most three steps. A limit guarantees a bounded number of body executions, provided each body finishes. It does not guarantee accuracy.

```javascript
let estimate = 0;
let steps = 0;
const limit = 3;
while (Math.abs(10 - estimate) > 0.1 && steps < limit) {
  estimate += (10 - estimate) / 2;
  steps++;
}
console.log(estimate, steps, Math.abs(10 - estimate) <= 0.1);
```

Output: 8.75 3 false. With limit seven the result is 9.921875 7 true. A limit is a safeguard, not a correction for an incorrect algorithm.

### Numerical stagnation

JavaScript Number values have finite precision. At 9007199254740992, adding one produces the same representable value. This can defeat a seemingly positive update. Try only this finite demonstration:

```javascript
const current = 9007199254740992;
console.log(current + 1 === current);
```

Output: true. Detect lack of progress before repeating.

```javascript
let current = 9007199254740992;
const target = 9007199254740996;
while (current < target) {
  const next = current + 1;
  if (next <= current) {
    console.log("No progress");
    break;
  }
  current = next;
}
```

Output: No progress. This detects the failure rather than achieving the target. Detailed numerical representation is deferred to a later lesson.

Practice: trace remaining elements for an empty array and [0, 5, 0]; implement a countdown from 73 to zero with releases of at most 20 (53, 33, 13, 0); repeat the approximation with limits zero, three and seven; explain why a limit does not establish correctness.

## 5. Blocking JavaScript execution

Synchronous code executes in order. While a loop is running, the statements following it cannot execute on the same JavaScript thread. In a browser, the main thread commonly also handles interaction and rendering. A long synchronous loop can delay these tasks; an infinite loop can leave the page unresponsive. Other threads or processes may continue.

```javascript
console.log("Before");
let total = 0;
for (let i = 1; i <= 1000; i++) {
  total += i;
}
console.log(total);
console.log("After");
```

Output: Before, 500500, After. The final statement waits for the loop. A loop that waits for a button click by repeatedly checking an unchanged Boolean flag is unsuitable: the event handler on the same thread cannot run until the current synchronous work yields control. Event handlers and asynchronous scheduling are future topics; do not implement waiting with a busy loop.

If a program becomes unresponsive, stop the running process or use the browser's stop/reload controls where available. Do not deliberately run an infinite example. Debug using a small bounded trace instead.

## 6. Developing reliable loop implementations

Process [20, -5, 0, 30, -1, 100] until total reaches 100, a sentinel appears, data ends or four attempts are consumed. Positive amounts count; invalid amounts are rejected; cancellation consumes an attempt but is not rejected. Success and cancellation outrank the limit, which outranks exhaustion.

```javascript
const proposals = [20, -5, 0, 30, -1, 100];
const limit = 4;
let used = 0;
let total = 0;
let accepted = 0;
let rejected = 0;
let cancelled = false;
while (used < proposals.length && used < limit && total < 100) {
  const value = proposals[used];
  used++;
  if (value === -1) {
    cancelled = true;
    break;
  }
  if (value <= 0) {
    rejected++;
    continue;
  }
  total += value;
  accepted++;
}
let reason = "Data exhausted";
if (cancelled) reason = "Cancelled";
else if (total >= 100) reason = "Target reached";
else if (used >= limit) reason = "Attempt limit reached";
console.log(total, used, accepted, rejected, reason);
```

Output: 50 4 2 2 Attempt limit reached. Every continuing path consumes one attempt. The number of remaining permitted attempts decreases, so termination does not depend on accepted deposits increasing. Test empty data, immediate sentinel, zero limit, target on final attempt, and invalid-only data.

## 7. Summary

A reachable exit, correct update direction, progress on every continuing path, stable or reachable boundaries, valid initial assumptions and explicit handling of unsuccessful termination are essential. A finite loop can still block execution for a long time. An iteration limit bounds repetition but does not prove the result correct.

## 8. Integrated exercises

1. Process [0, -5, 10, 20, -1, 100] with sentinel -1, positive-only acceptance and limit five. Expect total 30, attempts 5, accepted 2, rejected 2, cancellation.
2. Append doubled copies of original [2, 5, 8] to the same array. Expect [2, 5, 8, 4, 10, 16]. Explain why saving length matters.
3. Start at 100 and approach 20 by halfway adjustments, tolerance 0.1, limit eight. Expect 20.3125, eight steps, failure. With limit ten expect 20.078125, ten steps, success.
4. Count down from 95 to exactly 20 with releases of at most 30. Expect 65, 35, 20. Test initial 20 (zero operations) and zero release limit (failure if above target).
5. Toggle on/off six times starting on. Expect final on, six transitions. Explain why the state alone is not a progress measure.
6. Design a bounded manual trace for a loop that resets its counter to zero before incrementing it. Explain the repeated state and correct it to count to five.
7. Explain why repeatedly checking a flag in a synchronous loop cannot wait for a same-thread browser click handler to change it. No implementation of the waiting loop is required.

[Previous](13-looping-until-a-condition-is-met.md) | Next: Nested Loops (planned)
