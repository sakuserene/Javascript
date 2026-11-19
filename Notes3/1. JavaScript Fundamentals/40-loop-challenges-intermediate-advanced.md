# 40 JavaScript Loop Challenges: Intermediate to Advanced

Real-world practice with **10 for-loop**, **10 nested-loop**, **10 deep-nested-loop**, and **10 iteration-pattern** challenges. Each includes a question, runnable JavaScript answer, expected output, and step-by-step explanation.

> Requirements: Modern JavaScript (Node.js 18+ or a modern browser console). All datasets are illustrative. Currency examples use simple numbers for teaching; production financial software should use integer minor units or a decimal library.

## Table of Contents

- [1. For Loops](#for-loops)
- [2. Nested Loops](#nested-loops)
- [3. Deep Nested Loops (3+ Levels)](#deep-nested-loops-3-levels)
- [4. Iteration Patterns and Algorithms](#iteration-patterns-and-algorithms)
- [Comparisons and complexity](#comparisons-and-complexity)
- [Further practice](#further-practice)

---

## 1. For Loops

### 1.1 Shopping cart subtotal

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const prices = [19.99, 5.5, 12.25];
```

**Code answer**
```javascript
const prices = [19.99, 5.5, 12.25];
let total = 0;
for (let i = 0; i < prices.length; i++) total += prices[i];
console.log(total.toFixed(2));
```

**Expected output**
```text
37.74
```

**Step-by-step explanation**
1. Initialize total
2. Visit each price by index
3. Accumulate
4. Format at the end.
5. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.2 Tiered delivery charges

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const orders = [25, 75, 120, 49];
```

**Code answer**
```javascript
const orders = [25, 75, 120, 49];
const fees = [];
for (let i = 0; i < orders.length; i++) fees.push(orders[i] >= 100 ? 0 : orders[i] >= 50 ? 5 : 10);
console.log(fees);
```

**Expected output**
```text
[ 10, 5, 0, 10 ]
```

**Step-by-step explanation**
1. Walk through orders
2. Apply thresholds in descending order
3. Store each fee.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.3 Inventory reorder alerts

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const stock = [12, 3, 0, 8, 2];
```

**Code answer**
```javascript
const stock = [12, 3, 0, 8, 2];
const alerts = [];
for (let i = 0; i < stock.length; i++) if (stock[i] < 5) alerts.push(i);
console.log(alerts);
```

**Expected output**
```text
[ 1, 2, 4 ]
```

**Step-by-step explanation**
1. Compare every item with the threshold
2. Preserve indexes for follow-up.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.4 Moving three-day sales average

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const sales = [10, 20, 30, 40, 50];
```

**Code answer**
```javascript
const sales = [10, 20, 30, 40, 50];
const averages = [];
for (let i = 2; i < sales.length; i++) {
  averages.push((sales[i - 2] + sales[i - 1] + sales[i]) / 3);
}
console.log(averages);
```

**Expected output**
```text
[ 20, 30, 40 ]
```

**Step-by-step explanation**
1. Start when a full window exists
2. Combine current and two preceding days
3. Append mean.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.5 Bank account balance projection

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const changes = [100, -25, -10, 50];
```

**Code answer**
```javascript
const changes = [100, -25, -10, 50];
let balance = 200;
const history = [];
for (let i = 0; i < changes.length; i++) {
  balance += changes[i];
  history.push(balance);
}
console.log(history);
```

**Expected output**
```text
[ 300, 275, 265, 315 ]
```

**Step-by-step explanation**
1. Carry the previous balance forward
2. Apply each transaction once
3. Save snapshots.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.6 Customer loyalty points

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const purchases = [23, 59, 110, 40];
```

**Code answer**
```javascript
const purchases = [23, 59, 110, 40];
let points = 0;
for (let i = 0; i < purchases.length; i++) points += Math.floor(purchases[i] / 10);
console.log(points);
```

**Expected output**
```text
22
```

**Step-by-step explanation**
1. Convert each purchase into whole tens
2. Accumulate points
3. Discard fractional points.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.7 Sensor spike detection

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const readings = [10, 11, 35, 12, 50];
```

**Code answer**
```javascript
const readings = [10, 11, 35, 12, 50];
const spikes = [];
for (let i = 1; i < readings.length; i++) if (readings[i] - readings[i - 1] > 20) spikes.push(i);
console.log(spikes);
```

**Expected output**
```text
[ 2, 4 ]
```

**Step-by-step explanation**
1. Begin at second reading
2. Compare to immediate predecessor
3. Record abrupt increases.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.8 Pagination offsets

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const totalItems = 23;
const pageSize = 5;
```

**Code answer**
```javascript
const totalItems = 23;
const pageSize = 5;
const offsets = [];
for (let offset = 0; offset < totalItems; offset += pageSize) offsets.push(offset);
console.log(offsets);
```

**Expected output**
```text
[ 0, 5, 10, 15, 20 ]
```

**Step-by-step explanation**
1. Increment by page size, not one
2. Stop when offset reaches total items.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.9 Compound monthly growth

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const initial = 1000;
const monthlyRate = 0.01;
```

**Code answer**
```javascript
const initial = 1000;
const monthlyRate = 0.01;
let amount = initial;
for (let month = 1; month <= 3; month++) amount *= 1 + monthlyRate;
console.log(amount.toFixed(2));
```

**Expected output**
```text
1030.30
```

**Step-by-step explanation**
1. Multiply the previous amount by the growth factor for each month
2. Round only for display.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

### 1.10 Longest consecutive attendance streak

**Question:** Given the following data, implement the task using a traditional `for` loop. Predict the output before running it.

**Starting data**
```javascript
const attended = [true, true, false, true, true, true, false];
```

**Code answer**
```javascript
const attended = [true, true, false, true, true, true, false];
let run = 0, best = 0;
for (let i = 0; i < attended.length; i++) {
  run = attended[i] ? run + 1 : 0;
  best = Math.max(best, run);
}
console.log(best);
```

**Expected output**
```text
3
```

**Step-by-step explanation**
1. Track the current streak
2. Reset on absence
3. Update maximum after every day.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n) time for n elements, with output storage depending on the task.

---

## 2. Nested Loops

### 2.1 Cart with quantities and discounts

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const cart = [{price:10,qty:2},{price:5,qty:3}];
const rates = [0.1, 0.2];
```

**Code answer**
```javascript
const cart = [{price:10,qty:2},{price:5,qty:3}];
const rates = [0.1, 0.2];
const totals = [];
for (let i = 0; i < rates.length; i++) {
  let sum = 0;
  for (let j = 0; j < cart.length; j++) sum += cart[j].price * cart[j].qty * (1 - rates[i]);
  totals.push(sum.toFixed(2));
}
console.log(totals);
```

**Expected output**
```text
[ '31.50', '28.00' ]
```

**Step-by-step explanation**
1. Outer loop selects a discount scenario
2. Inner loop prices each cart line
3. Store each scenario total.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.2 Employee shift coverage

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const shifts = [[1,0,1],[1,1,0],[0,1,1]];
```

**Code answer**
```javascript
const shifts = [[1,0,1],[1,1,0],[0,1,1]];
const counts = [];
for (let day = 0; day < shifts.length; day++) {
  let covered = 0;
  for (let worker = 0; worker < shifts[day].length; worker++) covered += shifts[day][worker];
  counts.push(covered);
}
console.log(counts);
```

**Expected output**
```text
[ 2, 2, 2 ]
```

**Step-by-step explanation**
1. Outer loop visits days
2. Inner loop counts scheduled workers
3. Store coverage per day.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.3 Warehouse stock grid

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const bins = [[4,2,1],[0,3,5]];
```

**Code answer**
```javascript
const bins = [[4,2,1],[0,3,5]];
let total = 0;
for (let row = 0; row < bins.length; row++) {
  for (let col = 0; col < bins[row].length; col++) total += bins[row][col];
}
console.log(total);
```

**Expected output**
```text
15
```

**Step-by-step explanation**
1. Traverse each row, then each bin
2. Accumulate all stock units.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.4 Product pair bundles

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const products = ["A", "B", "C", "D"];
```

**Code answer**
```javascript
const products = ["A", "B", "C", "D"];
const pairs = [];
for (let i = 0; i < products.length; i++) {
  for (let j = i + 1; j < products.length; j++) pairs.push(products[i] + products[j]);
}
console.log(pairs);
```

**Expected output**
```text
[ 'AB', 'AC', 'AD', 'BC', 'BD', 'CD' ]
```

**Step-by-step explanation**
1. Start inner index after outer index
2. This avoids self-pairs and duplicate reversed pairs.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.5 Class test scores by student

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const scores = [[70,80],[90,100],[60,75]];
```

**Code answer**
```javascript
const scores = [[70,80],[90,100],[60,75]];
const averages = [];
for (let i = 0; i < scores.length; i++) {
  let sum = 0;
  for (let j = 0; j < scores[i].length; j++) sum += scores[i][j];
  averages.push(sum / scores[i].length);
}
console.log(averages);
```

**Expected output**
```text
[ 75, 95, 67.5 ]
```

**Step-by-step explanation**
1. Process one student per outer iteration
2. Sum tests in the inner loop
3. Divide by test count.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.6 Find duplicate transaction IDs

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const ids = ["T1", "T2", "T3", "T2"];
```

**Code answer**
```javascript
const ids = ["T1", "T2", "T3", "T2"];
const duplicates = [];
for (let i = 0; i < ids.length; i++) {
  for (let j = i + 1; j < ids.length; j++) if (ids[i] === ids[j]) duplicates.push(ids[i]);
}
console.log(duplicates);
```

**Expected output**
```text
[ 'T2' ]
```

**Step-by-step explanation**
1. Compare each ID only with later IDs
2. Report equal pairs. A Set is faster for large datasets.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.7 Compare travel costs

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const distances = [5, 10];
const rates = [2, 3, 4];
```

**Code answer**
```javascript
const distances = [5, 10];
const rates = [2, 3, 4];
const matrix = [];
for (let i = 0; i < distances.length; i++) {
  const row = [];
  for (let j = 0; j < rates.length; j++) row.push(distances[i] * rates[j]);
  matrix.push(row);
}
console.log(matrix);
```

**Expected output**
```text
[ [ 10, 15, 20 ], [ 20, 30, 40 ] ]
```

**Step-by-step explanation**
1. Outer loop chooses trip distance
2. Inner loop applies each rate
3. Create a cost matrix.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.8 Image pixel thresholding

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const pixels = [[20,180],[130,70]];
```

**Code answer**
```javascript
const pixels = [[20,180],[130,70]];
const mask = [];
for (let r = 0; r < pixels.length; r++) {
  const row = [];
  for (let c = 0; c < pixels[r].length; c++) row.push(pixels[r][c] >= 128 ? 1 : 0);
  mask.push(row);
}
console.log(mask);
```

**Expected output**
```text
[ [ 0, 1 ], [ 1, 0 ] ]
```

**Step-by-step explanation**
1. Visit each pixel coordinate
2. Classify against a threshold
3. Preserve the grid shape.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.9 Seating reservation lookup

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const seats = [["X","O","O"],["O","X","O"]];
```

**Code answer**
```javascript
const seats = [["X","O","O"],["O","X","O"]];
const available = [];
for (let r = 0; r < seats.length; r++) {
  for (let c = 0; c < seats[r].length; c++) if (seats[r][c] === "O") available.push(`${r}-${c}`);
}
console.log(available);
```

**Expected output**
```text
[ '0-1', '0-2', '1-0', '1-2' ]
```

**Step-by-step explanation**
1. Outer loop selects row
2. Inner loop checks seats
3. Collect coordinates of open seats.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

### 2.10 Cross-sell recommendations

**Question:** Given the following data, implement the task using two nested loops. Predict the output before running it.

**Starting data**
```javascript
const bought = ["bread", "milk"];
const catalog = ["milk", "eggs", "bread", "tea"];
```

**Code answer**
```javascript
const bought = ["bread", "milk"];
const catalog = ["milk", "eggs", "bread", "tea"];
const suggestions = [];
for (let i = 0; i < catalog.length; i++) {
  let owned = false;
  for (let j = 0; j < bought.length; j++) if (catalog[i] === bought[j]) owned = true;
  if (!owned) suggestions.push(catalog[i]);
}
console.log(suggestions);
```

**Expected output**
```text
[ 'eggs', 'tea' ]
```

**Step-by-step explanation**
1. For every catalog item, scan purchases
2. Add only items not already bought.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(n x m) time for two collection dimensions; triangular pair scans are O(n^2).

---

## 3. Deep Nested Loops (3+ Levels)

### 3.1 Multi-store multi-day sales

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const data = [[[10,20],[5,15]],[[8,12],[7,13]]];
```

**Code answer**
```javascript
const data = [[[10,20],[5,15]],[[8,12],[7,13]]];
let total = 0;
for (let store = 0; store < data.length; store++) {
  for (let day = 0; day < data[store].length; day++) {
    for (let sale = 0; sale < data[store][day].length; sale++) total += data[store][day][sale];
  }
}
console.log(total);
```

**Expected output**
```text
90
```

**Step-by-step explanation**
1. Traverse stores, then days, then individual sales
2. Sum every leaf value.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.2 Hotel floors rooms beds

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const beds = [[[1,1],[1,0]],[[0,1],[1,1]]];
```

**Code answer**
```javascript
const beds = [[[1,1],[1,0]],[[0,1],[1,1]]];
let occupied = 0;
for (let floor = 0; floor < beds.length; floor++) {
  for (let room = 0; room < beds[floor].length; room++) {
    for (let bed = 0; bed < beds[floor][room].length; bed++) occupied += beds[floor][room][bed];
  }
}
console.log(occupied);
```

**Expected output**
```text
6
```

**Step-by-step explanation**
1. Outer loop visits floors
2. Middle rooms
3. Inner bed occupancy flags.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.3 Regional quarterly monthly revenue

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const revenue = [[[100,200],[300,400]],[[50,75],[125,150]]];
```

**Code answer**
```javascript
const revenue = [[[100,200],[300,400]],[[50,75],[125,150]]];
const regionTotals = [];
for (let r = 0; r < revenue.length; r++) {
  let sum = 0;
  for (let q = 0; q < revenue[r].length; q++) {
    for (let m = 0; m < revenue[r][q].length; m++) sum += revenue[r][q][m];
  }
  regionTotals.push(sum);
}
console.log(regionTotals);
```

**Expected output**
```text
[ 1000, 400 ]
```

**Step-by-step explanation**
1. Aggregate months inside quarters inside regions
2. Reset region sum at the outer level.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.4 Course cohort student grades

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const grades = [[[80,90],[70,60]],[[100,90],[80,70]]];
```

**Code answer**
```javascript
const grades = [[[80,90],[70,60]],[[100,90],[80,70]]];
const cohortMeans = [];
for (let c = 0; c < grades.length; c++) {
  let sum = 0, count = 0;
  for (let s = 0; s < grades[c].length; s++) {
    for (let t = 0; t < grades[c][s].length; t++) { sum += grades[c][s][t]; count++; }
  }
  cohortMeans.push(sum / count);
}
console.log(cohortMeans);
```

**Expected output**
```text
[ 75, 85 ]
```

**Step-by-step explanation**
1. Count tests at the deepest level
2. Calculate each cohort mean after its students are processed.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.5 3D temperature cube

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const cube = [[[20,22],[24,26]],[[28,30],[32,34]]];
```

**Code answer**
```javascript
const cube = [[[20,22],[24,26]],[[28,30],[32,34]]];
let max = -Infinity;
for (let z = 0; z < cube.length; z++) {
  for (let y = 0; y < cube[z].length; y++) {
    for (let x = 0; x < cube[z][y].length; x++) max = Math.max(max, cube[z][y][x]);
  }
}
console.log(max);
```

**Expected output**
```text
34
```

**Step-by-step explanation**
1. Inspect each depth, row, and column coordinate
2. Update the global maximum.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.6 Factory shift machine output

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const output = [[[3,4],[5,6]],[[2,3],[4,5]]];
```

**Code answer**
```javascript
const output = [[[3,4],[5,6]],[[2,3],[4,5]]];
const shiftTotals = [];
for (let shift = 0; shift < output.length; shift++) {
  let sum = 0;
  for (let machine = 0; machine < output[shift].length; machine++) {
    for (let batch = 0; batch < output[shift][machine].length; batch++) sum += output[shift][machine][batch];
  }
  shiftTotals.push(sum);
}
console.log(shiftTotals);
```

**Expected output**
```text
[ 18, 14 ]
```

**Step-by-step explanation**
1. Reset sum per shift
2. Accumulate all machine batches
3. Append shift total.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.7 E-commerce categories products variants

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const stock = [[[2,3],[1,4]],[[5,0],[2,2]]];
```

**Code answer**
```javascript
const stock = [[[2,3],[1,4]],[[5,0],[2,2]]];
let low = 0;
for (let category = 0; category < stock.length; category++) {
  for (let product = 0; product < stock[category].length; product++) {
    for (let variant = 0; variant < stock[category][product].length; variant++) if (stock[category][product][variant] < 3) low++;
  }
}
console.log(low);
```

**Expected output**
```text
4
```

**Step-by-step explanation**
1. Inspect every variant under its product and category
2. Count stock values below three.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.8 Event venue section row seat capacity

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const seats = [[[1,0],[1,1]],[[0,0],[1,0]]];
```

**Code answer**
```javascript
const seats = [[[1,0],[1,1]],[[0,0],[1,0]]];
let free = 0;
for (let section = 0; section < seats.length; section++) {
  for (let row = 0; row < seats[section].length; row++) {
    for (let seat = 0; seat < seats[section][row].length; seat++) if (seats[section][row][seat] === 0) free++;
  }
}
console.log(free);
```

**Expected output**
```text
4
```

**Step-by-step explanation**
1. Visit section, row, then seat
2. Zero denotes availability
3. Count available seats.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.9 Experiment configurations

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const regions = ["N", "S"];
const channels = ["web", "app"];
const variants = ["A", "B"];
```

**Code answer**
```javascript
const regions = ["N", "S"];
const channels = ["web", "app"];
const variants = ["A", "B"];
const combinations = [];
for (let r = 0; r < regions.length; r++) {
  for (let c = 0; c < channels.length; c++) {
    for (let v = 0; v < variants.length; v++) combinations.push(`${regions[r]}-${channels[c]}-${variants[v]}`);
  }
}
console.log(combinations.length, combinations[0], combinations.at(-1));
```

**Expected output**
```text
8 N-web-A S-app-B
```

**Step-by-step explanation**
1. Form the Cartesian product of regions, channels, and variants
2. 2 x 2 x 2 combinations.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

### 3.10 Route options with cost constraints

**Question:** Given the following data, implement the task using three nested loops. Predict the output before running it.

**Starting data**
```javascript
const starts = [1,2];
const middles = [3,4];
const ends = [5,6];
```

**Code answer**
```javascript
const starts = [1,2];
const middles = [3,4];
const ends = [5,6];
const routes = [];
for (let a = 0; a < starts.length; a++) {
  for (let b = 0; b < middles.length; b++) {
    for (let c = 0; c < ends.length; c++) {
      const cost = starts[a] + middles[b] + ends[c];
      if (cost <= 10) routes.push(cost);
    }
  }
}
console.log(routes);
```

**Expected output**
```text
[ 9, 10, 10 ]
```

**Step-by-step explanation**
1. Enumerate each route combination
2. Calculate its cost
3. Retain only routes meeting the limit.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Usually O(a x b x c) time for three dimensions.

---

## 4. Iteration Patterns and Algorithms

### 4.1 for...of transaction reconciliation

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const expected = [10,20,30];
const received = [10,25,30];
```

**Code answer**
```javascript
const expected = [10,20,30];
const received = [10,25,30];
let mismatches = 0;
for (const [index, amount] of expected.entries()) if (amount !== received[index]) mismatches++;
console.log(mismatches);
```

**Expected output**
```text
1
```

**Step-by-step explanation**
1. Entries() yields index and value
2. Compare each expected amount with received at that index.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.2 Map inventory to reorder quantities

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const inventory = [{sku:"A",qty:2},{sku:"B",qty:9},{sku:"C",qty:4}];
```

**Code answer**
```javascript
const inventory = [{sku:"A",qty:2},{sku:"B",qty:9},{sku:"C",qty:4}];
const orders = inventory.map(item => ({sku:item.sku, reorder:Math.max(0, 10 - item.qty)}));
console.log(orders);
```

**Expected output**
```text
[ { sku: 'A', reorder: 8 }, { sku: 'B', reorder: 1 }, { sku: 'C', reorder: 6 } ]
```

**Step-by-step explanation**
1. Map visits each item
2. Calculate shortage relative to target ten
3. Return a new object.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.3 Filter overdue invoices

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const invoices = [{id:1,days:5},{id:2,days:40},{id:3,days:65}];
```

**Code answer**
```javascript
const invoices = [{id:1,days:5},{id:2,days:40},{id:3,days:65}];
const overdue = invoices.filter(invoice => invoice.days > 30).map(invoice => invoice.id);
console.log(overdue);
```

**Expected output**
```text
[ 2, 3 ]
```

**Step-by-step explanation**
1. Filter retains invoices older than 30 days
2. Map extracts their IDs.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.4 Reduce daily expenses

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const expenses = [12.5, 8, 19.5];
```

**Code answer**
```javascript
const expenses = [12.5, 8, 19.5];
const total = expenses.reduce((sum, expense) => sum + expense, 0);
console.log(total);
```

**Expected output**
```text
40
```

**Step-by-step explanation**
1. Reduce carries an accumulator
2. Add each expense starting from zero.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.5 Find first failed quality check

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const checks = [{id:"A",ok:true},{id:"B",ok:false},{id:"C",ok:false}];
```

**Code answer**
```javascript
const checks = [{id:"A",ok:true},{id:"B",ok:false},{id:"C",ok:false}];
const failed = checks.find(check => !check.ok);
console.log(failed.id);
```

**Expected output**
```text
B
```

**Step-by-step explanation**
1. Find visits checks in order and stops at the first failed predicate.
2. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.6 Check all permissions

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const permissions = [true, true, false];
```

**Code answer**
```javascript
const permissions = [true, true, false];
console.log(permissions.every(Boolean));
```

**Expected output**
```text
false
```

**Step-by-step explanation**
1. Every tests each permission and short-circuits on the first falsy value.
2. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.7 Detect any high-risk reading

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const readings = [21, 22, 99, 24];
```

**Code answer**
```javascript
const readings = [21, 22, 99, 24];
console.log(readings.some(value => value > 80));
```

**Expected output**
```text
true
```

**Step-by-step explanation**
1. Some checks readings until one exceeds the threshold, then stops.
2. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.8 Count words using for...of

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
const sentence = "red blue red green red";
```

**Code answer**
```javascript
const sentence = "red blue red green red";
const counts = new Map();
for (const word of sentence.split(" ")) counts.set(word, (counts.get(word) ?? 0) + 1);
console.log([...counts]);
```

**Expected output**
```text
[ [ 'red', 3 ], [ 'blue', 1 ], [ 'green', 1 ] ]
```

**Step-by-step explanation**
1. Split into words
2. Iterate values
3. Increment frequency in a Map
4. Inspect entries.
5. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.9 Streaming events with a generator

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
function* events() { yield "start"; yield "process"; yield "done"; }
```

**Code answer**
```javascript
function* events() { yield "start"; yield "process"; yield "done"; }
const log = [];
for (const event of events()) log.push(event.toUpperCase());
console.log(log);
```

**Expected output**
```text
[ 'START', 'PROCESS', 'DONE' ]
```

**Step-by-step explanation**
1. A generator yields one value at a time
2. For...of consumes each yield in order.
3. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

### 4.10 Async iteration of paginated results

**Question:** Given the following data, implement the task using the named iteration technique. Predict the output before running it.

**Starting data**
```javascript
async function* pages() { yield [1,2]; yield [3]; yield [4,5]; }
```

**Code answer**
```javascript
async function* pages() { yield [1,2]; yield [3]; yield [4,5]; }
async function main() {
  const all = [];
  for await (const page of pages()) for (const item of page) all.push(item);
  console.log(all);
}
main();
```

**Expected output**
```text
[ 1, 2, 3, 4, 5 ]
```

**Step-by-step explanation**
1. For await...of consumes each asynchronous page
2. The inner loop flattens page items
3. Main starts the async work.
4. Verify the expected output by running the complete snippet; change the data to test boundary cases.

**Complexity note:** Often O(n) time; short-circuiting operations may stop earlier.

---

## Comparisons and complexity

| Pattern | Typical use | Time cost | Caution |
|---|---|---|---|
| `for` | Indexed traversal, windows, pagination | O(n) | Bounds and off-by-one errors |
| Two nested loops | Grids, pair comparisons, matrices | O(nm) | Duplicate pairs, quadratic growth |
| Three nested loops | 3D data, combinations | O(abc) | Combinatorial explosion |
| `for...of` | Iterable values | O(n) | Does not automatically expose index |
| `map` / `filter` | Transform / select | O(n) | Produce new arrays |
| `reduce` | Aggregate | O(n) | Choose correct initial accumulator |
| `find` / `some` / `every` | Search and predicates | Up to O(n) | Short-circuiting |
| `for await...of` | Asynchronous streams | Depends on I/O | Use inside async functions |

## Further practice

1. Add input validation for missing, negative, or malformed values.
2. Write unit tests for empty arrays, one-element arrays, and duplicate values.
3. Replace pairwise duplicate detection with a `Set` and compare O(n^2) versus O(n).
4. Turn one three-level loop into reusable functions and compare readability.
5. Benchmark large inputs, but avoid timing console output as the primary work.
6. For financial examples, store money in cents and format only when displaying.

---

**Course navigation:** [Previous: Lesson 8 - Loops and Iteration](08-loops-and-iteration.md) | [Next: Lesson 9 - Functions](09-functions-and-function-expressions.md)

**Encoding:** ASCII-only text, UTF-8 file.
