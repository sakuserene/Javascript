# Chapter 02 - Array Methods and Plain Loops

## Part 5 - Filtering, Partitioning and Conditional Collection

[Previous: Part 4](array-methods-and-loops-part-4.md) | [Next: Part 6](array-methods-and-loops-part-6.md)

## Table of contents

1. Understanding filtering
2. The filter() method
3. Implementing filter() using plain loops
4. Filtering primitive values
5. Filtering arrays of objects
6. Filtering using multiple conditions
7. Filtering using dynamic conditions
8. Filtering numerical ranges
9. Filtering strings
10. Filtering nested object properties
11. Filtering using membership conditions
12. Removing duplicate values
13. Removing duplicate objects
14. Filtering sparse arrays and mutation behavior
15. Partitioning arrays
16. Multiple-category partitioning
17. In-place filtering and array compaction
18. Unstable partitioning
19. Intersection, union, difference and symmetric difference
20. Multiset operations
21. Combining filtering with other operations
22. Advanced conditional collection algorithms
23. Performance and memory considerations
24. Common mistakes and edge cases
25. Progressive exercises and solutions
26. Coverage tracker and references

## 1. Understanding filtering

Filtering examines a collection and retains elements satisfying a condition. Visit, evaluate, collect if accepted, and continue.

```javascript
const temperatures = [24, 31, 28, 35, 29, 32];
const result = [];
for (let i = 0; i < temperatures.length; i++) {
    if (temperatures[i] >= 30) {
        result.push(temperatures[i]);
    }
}
console.log(result);
// [31, 35, 32]
```

`i` reads the current index; `length` bounds traversal; `>=` compares values; `push` appends accepted elements.

| Index | Value | Accepted | Result |
|---|---|---|---|
|0|24|No|[]|
|1|31|Yes|[31]|
|2|28|No|[31]|
|3|35|Yes|[31,35]|
|4|29|No|[31,35]|
|5|32|Yes|[31,35,32]|

Without array methods:

```javascript
const result = [];
let writeIndex = 0;
for (let i = 0; i < temperatures.length; i++) {
    if (temperatures[i] >= 30) {
        result[writeIndex] = temperatures[i];
        writeIndex++;
    }
}
```

Static demonstration: `[8,14,27,35,19,42,11,30]` with threshold 20 yields `[27,35,42,30]`. `find()` returns the first match or undefined; `filter()` returns all matches or an empty array.

## 2. The filter() method

```javascript
array.filter(callbackFn, thisArg);
const result = array.filter(function (element, index, array) {
    return condition;
}, thisArg);
```

The callback receives value, index and source. Optional `thisArg` supplies `this` to ordinary functions, not arrow functions. The method returns a new shallow array of original elements whose callback results are truthy. It preserves order, skips missing indexed properties, captures initial length and does not directly mutate the source. It is generic and was introduced in ES5.

```javascript
const batteryLevels = [85, 12, 45, 8, 63, 19];
console.log(batteryLevels.filter(level => level < 20));
// [12, 8, 19]
console.log([0,1,2,3,4,5].filter(n => n % 2));
// [1,3,5]
console.log([2,4,6].filter(n => n * 10));
// [2,4,6], not transformed values
const measurements = [18,21,19,24,22,26];
console.log(measurements.filter((value,index) => index % 2 === 0));
// [18,19,22]
const readings = [10,15,12,20,18,25];
console.log(readings.filter((value,index,array) => index > 0 && value > array[index-1]));
// [15,20,25]
const configuration = { min: 20, max: 50 };
console.log([10,25,45,60,35].filter(function (number) {
    return number >= this.min && number <= this.max;
}, configuration));
// [25,45,35]
const collection = {0:12,1:5,2:30,length:3};
console.log(Array.prototype.filter.call(collection, value => value >= 10));
// [12,30]
```

A predicate tests a condition. `&&` short-circuits, so the preceding-element example does not read a negative index. A specialized loop can visit even indexes using `i += 2`.

```javascript
const plants = [{name:'Rose',healthy:true},{name:'Lily',healthy:false}];
const healthyPlants = plants.filter(plant => plant.healthy);
healthyPlants[0].name = 'Orchid';
console.log(plants[0].name); // Orchid
console.log(plants === healthyPlants); // false
```

Both arrays reference the same selected object. Use `structuredClone` when independent copies are needed and supported.

## 3. Implementing filter() using plain loops

```javascript
function manualFilter(array, callback, thisArg) {
    if (typeof callback !== 'function') {
        throw new TypeError('Callback must be a function');
    }
    const length = array.length;
    const result = [];
    for (let i = 0; i < length; i++) {
        if (!(i in array)) continue;
        const value = array[i];
        if (callback.call(thisArg, value, i, array)) {
            result[result.length] = value;
        }
    }
    return result;
}
console.log(manualFilter([4,9,12,17,20], n => n % 2 === 0));
// [4,12,20]
```

The initial length is captured; `in` checks property existence; the value is saved before the callback; `call` supplies arguments and receiver. This educational implementation is not a complete specification polyfill.

```javascript
const numbers = [10,20,30];
const result = numbers.filter((value,index,array) => {
    array[index] = 100;
    return true;
});
console.log(result); // [10,20,30]
console.log(numbers); // [100,100,100]
```

## 4. Filtering primitive values

```javascript
console.log([-4,3,0,-2,8,5].filter(n => n > 0)); // [3,8,5]
console.log([3,5,15,20,30,42,45].filter(n => n % 3 === 0 && n % 5 === 0)); // [15,30,45]
const values = [12,'hello',true,null,undefined,35,'world',false,NaN];
console.log(values.filter(v => typeof v === 'number')); // [12,35,NaN]
console.log(values.filter(Number.isFinite)); // [12,35]
console.log([0,12,'','JavaScript',false,true,null,undefined,NaN,[],{}].filter(Boolean));
// [12,'JavaScript',true,[],{}]
console.log([0,false,'',null,undefined,25].filter(v => v !== null && v !== undefined));
// [0,false,'',25]
```

Falsy values include false, 0, -0, 0n, empty string, null, undefined and NaN. Empty arrays and objects are truthy. Do not confuse missing values with falsy values.

## 5. Filtering arrays of objects

```javascript
const books = [
    {id:1,title:'Astronomy',available:true},
    {id:2,title:'Chemistry',available:false},
    {id:3,title:'Geography',available:true},
    {id:4,title:'Biology',available:false}
];
const availableBooks = books.filter(book => book.available === true);
// Astronomy and Geography
const result = [];
for (const book of books) {
    if (book.available === true) result.push(book);
}
const observations = [{species:'Zebra',count:12},{species:'Lion',count:3},{species:'Elephant',count:8},{species:'Leopard',count:1}];
console.log(observations.filter(o => o.count >= 5));
// Zebra and Elephant
const profiles = [{name:'Daniel',email:'daniel@example.com'},{name:'Sarah'},{name:'David',email:undefined},{name:'Grace',email:'grace@example.com'}];
console.log(profiles.filter(p => Object.hasOwn(p,'email')));
// Daniel, David, Grace
```

`Object.hasOwn` checks own property existence; `in` includes inherited properties; `!== undefined` checks the retrieved value; Boolean checks truthiness.

## 6. Filtering using multiple conditions

```javascript
const candidates = [{name:'Alex',experience:5,available:true},{name:'Maya',experience:2,available:true},{name:'Noah',experience:4,available:false},{name:'Emma',experience:3,available:true}];
console.log(candidates.filter(c => c.experience >= 3 && c.available === true));
// Alex and Emma
const tasks = [{id:1,urgent:true,overdue:false},{id:2,urgent:false,overdue:false},{id:3,urgent:false,overdue:true},{id:4,urgent:true,overdue:true}];
console.log(tasks.filter(t => t.urgent || t.overdue)); // IDs 1,3,4
const devices = [{name:'Router',connected:true},{name:'Printer',connected:false},{name:'Camera',connected:false}];
console.log(devices.filter(d => !d.connected)); // Printer, Camera
```

AND requires both conditions, OR requires at least one, NOT reverses truthiness. For published science or technology documents excluding archived documents:

```javascript
const result = documents.filter(document => {
    const validCategory = document.category === 'science' || document.category === 'technology';
    return document.published && validCategory && !document.archived;
});
```

## 7. Filtering using dynamic conditions

```javascript
function filterAbove(array, minimum) {
    return array.filter(value => value >= minimum);
}
function filterByProperty(array, property, expectedValue) {
    return array.filter(item => item?.[property] === expectedValue);
}
function filterCourses(courses, options = {}) {
    return courses.filter(course => {
        if (options.category !== undefined && course.category !== options.category) return false;
        if (options.minRating !== undefined && course.rating < options.minRating) return false;
        if (options.maxHours !== undefined && course.hours > options.maxHours) return false;
        return true;
    });
}
```

Computed property access uses the supplied property name. Optional chaining protects against nullish receivers. Explicit undefined checks preserve valid zero-valued options.

```javascript
const numbers = [5,10,15,20,25,30,35];
const conditions = [n => n >= 10,n => n <= 30,n => n % 2 === 0];
console.log(numbers.filter(n => conditions.every(condition => condition(n))));
// [10,20,30]
function filterByAllConditions(array, conditions) {
    const result = [];
    for (const value of array) {
        let accepted = true;
        for (const condition of conditions) {
            if (!condition(value)) {
                accepted = false;
                break;
            }
        }
        if (accepted) result.push(value);
    }
    return result;
}
```

Worst-case O(n*m). Use some() for at least one predicate.

## 8. Filtering numerical ranges

```javascript
function filterRange(array,min,max) {
    return array.filter(value => value >= min && value <= max);
}
console.log(filterRange([5,3,8,1],1,4)); // [3,1]
```

Inclusive: `>= min && <= max`; exclusive: `> min && < max`; half-open: `>= min && < max` or `> min && <= max`. Outside: `< min || > max`.

```javascript
const frequencies = [12,18,25,32,48,55,70];
const ranges = [{min:10,max:20},{min:45,max:60}];
const result = [];
for (const frequency of frequencies) {
    for (const range of ranges) {
        if (frequency >= range.min && frequency <= range.max) {
            result.push(frequency);
            break;
        }
    }
}
console.log(result); // [12,18,48,55]
```

## 9. Filtering strings

```javascript
console.log(['sun','mountain','river','sky','forest'].filter(w => w.length >= 5));
// ['mountain','river','forest']
const files = ['backup-january.zip','report.pdf','backup-february.zip','notes.txt'];
console.log(files.filter(f => f.startsWith('backup-')));
console.log(['landscape.jpg','manual.pdf','portrait.jpg','diagram.svg'].filter(f => f.endsWith('.jpg')));
const titles = ['Learning JavaScript','Advanced Python','JavaScript Patterns','Database Systems'];
const query = 'JAVASCRIPT'.trim().toLowerCase();
console.log(titles.filter(t => t.toLowerCase().includes(query)));
// ['Learning JavaScript','JavaScript Patterns']
const pattern = /^[A-Z]{2}\d{3}$/;
console.log(['AB123','XY456','123AB','ABC12','CD789'].filter(code => pattern.test(code)));
// ['AB123','XY456','CD789']
```

String length counts UTF-16 code units. Regex g and y flags can make test() stateful.

## 10. Filtering nested object properties

```javascript
const stations = [{name:'Station A',location:{region:'north'}},{name:'Station B',location:{region:'south'}},{name:'Station C'},{name:'Station D',location:{region:'north'}}];
console.log(stations.filter(s => s.location?.region === 'north').map(s => s.name));
// ['Station A','Station D']
const result = projects.filter(project => project.tasks.some(task => !task.completed));
const completedProjects = projects.filter(project => project.tasks.length > 0 && project.tasks.every(task => task.completed));
```

Plain nested loop: visit projects, visit tasks, collect project and break on the first incomplete task. Empty arrays satisfy every() vacuously.

## 11. Filtering using membership conditions

```javascript
const colors = ['red','blue','green','yellow','purple'];
const selected = ['blue','yellow'];
console.log(colors.filter(c => selected.includes(c))); // ['blue','yellow']
const blocked = ['green','purple'];
console.log(colors.filter(c => !blocked.includes(c))); // ['red','blue','yellow']
const result = [];
for (const color of colors) {
    for (const selectedColor of selected) {
        if (color === selectedColor) {
            result.push(color);
            break;
        }
    }
}
const selectedSet = new Set(selected);
console.log(colors.filter(c => selectedSet.has(c)));
```

Nested membership costs O(n*m); hash-based Set membership typically O(n+m). Set and includes use SameValueZero, unlike strict equality for NaN.

## 12. Removing duplicate values

```javascript
const numbers = [4,7,4,2,7,9,2];
console.log(numbers.filter((v,i,a) => a.indexOf(v) === i)); // [4,7,2,9]
function uniqueValues(array) {
    const result = [];
    for (const value of array) {
        if (!result.includes(value)) result.push(value);
    }
    return result;
}
console.log([...new Set(numbers)]);
const seen = new Set();
const unique = numbers.filter(value => {
    if (seen.has(value)) return false;
    seen.add(value);
    return true;
});
function uniqueLast(array) {
    const seen = new Set();
    const result = [];
    for (let i = array.length - 1; i >= 0; i--) {
        if (!seen.has(array[i])) {
            seen.add(array[i]);
            result.push(array[i]);
        }
    }
    return result.reverse();
}
console.log(uniqueLast(['A','B','A','C','B'])); // ['A','C','B']
```

indexOf-based deduplication is quadratic and loses NaN. Set preserves insertion order.

## 13. Removing duplicate objects

```javascript
function uniqueBy(array,getKey) {
    const seen = new Set();
    const result = [];
    for (const value of array) {
        const key = getKey(value);
        if (!seen.has(key)) {
            seen.add(key);
            result.push(value);
        }
    }
    return result;
}
const records = [{id:1,name:'Alex'},{id:2,name:'Maya'},{id:1,name:'Alexander'},{id:3,name:'Emma'}];
console.log(uniqueBy(records,r => r.id)); // Alex, Maya, Emma
const index = new Map();
for (const record of records) index.set(record.id,record);
console.log([...index.values()]); // Alexander, Maya, Emma
```

Object equality uses identity. Map replacement preserves first-key insertion order. For composite keys, use nested maps/sets to avoid separator collisions:

```javascript
const seen = new Map();
const result = [];
for (const observation of observations) {
    const {station,date} = observation;
    if (!seen.has(station)) seen.set(station,new Set());
    const dates = seen.get(station);
    if (!dates.has(date)) {
        dates.add(date);
        result.push(observation);
    }
}
```

## 14. Filtering sparse arrays and mutation behavior

```javascript
console.log([10,,30,undefined].filter(() => true)); // [10,30,undefined]
console.log([1,,3,,5].filter(() => true)); // [1,3,5]
console.log([...[1,,3,,5]]); // [1,undefined,3,undefined,5]
const numbers = [1,2,3];
console.log(numbers.filter((v,i,a) => {
    if (i === 0) a.push(4);
    return true;
})); // [1,2,3]
```

Initial length is captured. Deleted future properties are skipped; changed future values are read when visited. An inherited indexed property can make an otherwise empty slot visible. Avoid conflicting mutation.

## 15. Partitioning arrays

```javascript
function partition(array,predicate) {
    const accepted = [];
    const rejected = [];
    for (let i = 0; i < array.length; i++) {
        if (!(i in array)) continue;
        const value = array[i];
        if (predicate(value,i,array)) accepted.push(value);
        else rejected.push(value);
    }
    return [accepted,rejected];
}
const [passed,failed] = partition([45,80,62,30,95],n => n >= 50);
console.log(passed); // [80,62,95]
console.log(failed); // [45,30]
```

Two filter calls traverse twice; one partition loop evaluates once per element. Static threshold example: [42,85,61,29,93,55,18,74] at 50 yields accepted [85,61,93,55,74], rejected [42,29,18].

## 16. Multiple-category partitioning

```javascript
const temperatures = [12,25,35,8,22,40,18];
const cold = [], moderate = [], hot = [];
for (const temperature of temperatures) {
    if (temperature < 20) cold.push(temperature);
    else if (temperature < 30) moderate.push(temperature);
    else hot.push(temperature);
}
console.log({cold,moderate,hot});
// {cold:[12,8,18],moderate:[25,22],hot:[35,40]}
const groups = new Map();
for (const book of books) {
    if (!groups.has(book.genre)) groups.set(book.genre,[]);
    groups.get(book.genre).push(book);
}
```

Modern alternatives: `Map.groupBy(books, book => book.genre)` and `Object.groupBy(books, book => book.genre)`. Object.groupBy returns a null-prototype object. Separate if statements allow overlapping groups; else-if makes them exclusive.

## 17. In-place filtering and array compaction

Forward splice can skip shifted elements. Decrement the index after deletion or traverse backward. Repeated splice can be quadratic.

```javascript
function filterInPlace(array,predicate) {
    const length = array.length;
    let write = 0;
    for (let read = 0; read < length; read++) {
        if (!(read in array)) continue;
        const value = array[read];
        if (predicate(value,read,array)) {
            array[write] = value;
            write++;
        }
    }
    array.length = write;
    return array;
}
console.log(filterInPlace([8,-3,12,-5,20,7],n => n >= 0)); // [8,12,20,7]
function filterRangeInPlace(array,min,max) {
    let write = 0;
    for (let read = 0; read < array.length; read++) {
        const value = array[read];
        if (value >= min && value <= max) array[write++] = value;
    }
    array.length = write;
}
```

| Read | Value | Write after | Array |
|---|---|---|---|
|0|8|1|[8,-3,12,-5,20,7]|
|1|-3|1|[8,-3,12,-5,20,7]|
|2|12|2|[8,12,12,-5,20,7]|
|3|-5|2|[8,12,12,-5,20,7]|
|4|20|3|[8,12,20,-5,20,7]|
|5|7|4|[8,12,20,7,20,7]|

Truncate to length 4. O(n) time, O(1) auxiliary space, stable order. Assumes writable array and nonconflicting predicate.

## 18. Unstable partitioning

```javascript
function partitionInPlace(array,predicate) {
    let left = 0;
    let right = array.length - 1;
    while (left <= right) {
        if (predicate(array[left])) left++;
        else if (!predicate(array[right])) right--;
        else {
            [array[left],array[right]] = [array[right],array[left]];
            left++;
            right--;
        }
    }
    return left;
}
const numbers = [7,4,9,2,6,3,8];
console.log(partitionInPlace(numbers,n => n % 2 === 0)); // 4
console.log(numbers); // [8,4,6,2,9,3,7]
```

Swap 7 and 8, advance past 4, retreat past 3, swap 9 and 6, advance past 2. Boundary 4. O(n) time and O(1) auxiliary space; relative order not guaranteed.

## 19. Intersection, union, difference and symmetric difference

```javascript
const A = [1,2,3,4], B = [3,4,5,6];
const setA = new Set(A), setB = new Set(B);
console.log(A.filter(v => setB.has(v))); // [3,4]
console.log([...new Set([...A,...B])]); // [1,2,3,4,5,6]
console.log(A.filter(v => !setB.has(v))); // [1,2]
console.log([...A.filter(v => !setB.has(v)),...B.filter(v => !setA.has(v))]); // [1,2,5,6]
```

For unique semantics iterate Sets rather than duplicate-containing arrays. Modern Set methods: intersection, union, difference, symmetricDifference, isSubsetOf, isSupersetOf, isDisjointFrom. Require compatible runtime.

## 20. Multiset operations

```javascript
function multisetIntersection(a,b) {
    const counts = new Map(), result = [];
    for (const value of b) counts.set(value,(counts.get(value) ?? 0)+1);
    for (const value of a) {
        const remaining = counts.get(value) ?? 0;
        if (remaining > 0) {
            result.push(value);
            counts.set(value,remaining-1);
        }
    }
    return result;
}
function multisetDifference(a,b) {
    const counts = new Map(), result = [];
    for (const value of b) counts.set(value,(counts.get(value) ?? 0)+1);
    for (const value of a) {
        const remaining = counts.get(value) ?? 0;
        if (remaining > 0) counts.set(value,remaining-1);
        else result.push(value);
    }
    return result;
}
console.log(multisetIntersection([1,1,2,2,3],[1,2,2,4])); // [1,2,2]
console.log(multisetDifference([1,1,2,2,3],[1,2,2,4])); // [1,3]
```

Frequency rules: intersection minimum, union maximum, sum addition, difference nonnegative subtraction, symmetric difference absolute difference.

## 21. Combining filtering with other operations

```javascript
const names = volunteers.filter(v => v.active).map(v => v.name);
const count = [10,5,20,3,30].filter(n => n >= 10).length; // 3
const total = sessions.filter(s => s.completed).reduce((sum,s) => sum+s.duration,0);
const first = [5,12,18,25,30].find(n => n > 10 && n % 5 === 0); // 25
const result = {values:[],count:0,sum:0,min:null,max:null,average:null};
for (const value of [12,5,28,30,8,18]) {
    if (value >= 10) {
        result.values.push(value);
        result.count++;
        result.sum += value;
        if (result.min === null || value < result.min) result.min = value;
        if (result.max === null || value > result.max) result.max = value;
    }
}
if (result.count > 0) result.average = result.sum/result.count;
console.log(result);
// {values:[12,28,30,18],count:4,sum:88,min:12,max:30,average:22}
```

Loop fusion combines collection, counting, accumulation and candidate tracking. If only counting, avoid creating an array.

## 22. Advanced conditional collection algorithms

```javascript
function takeMatching(array,predicate,limit) {
    if (!Number.isSafeInteger(limit) || limit < 0) throw new RangeError('Invalid limit');
    const result = [];
    if (limit === 0) return result;
    for (const value of array) {
        if (predicate(value)) {
            result.push(value);
            if (result.length === limit) break;
        }
    }
    return result;
}
function skipMatching(array,predicate,skip) {
    if (!Number.isSafeInteger(skip) || skip < 0) throw new RangeError('Invalid skip count');
    const result = [];
    let matched = 0;
    for (const value of array) {
        if (predicate(value) && ++matched > skip) result.push(value);
    }
    return result;
}
function takeWhile(array,predicate) {
    const result = [];
    for (const value of array) {
        if (!predicate(value)) break;
        result.push(value);
    }
    return result;
}
function dropWhile(array,predicate) {
    let index = 0;
    while (index < array.length && predicate(array[index])) index++;
    return array.slice(index);
}
console.log(takeMatching([-2,5,-1,8,3,12,7],n => n > 0,3)); // [5,8,3]
console.log(skipMatching([2,5,4,8,7,10],n => n % 2 === 0,2)); // [8,10]
console.log(takeWhile([4,8,12,3,20,24],n => n >= 4)); // [4,8,12]
console.log(dropWhile([0,0,5,0,8],n => n === 0)); // [5,0,8]
const heights = [10,25,15,40,20,30,10];
console.log(heights.filter((v,i,a) => i > 0 && i < a.length-1 && v > a[i-1] && v > a[i+1])); // [25,40,30]
const groups = [];
let current = [];
for (const number of [2,5,-1,8,3,4,-2,7]) {
    if (number > 0) current.push(number);
    else if (current.length > 0) {
        groups.push(current);
        current = [];
    }
}
if (current.length > 0) groups.push(current);
console.log(groups); // [[2,5],[8,3,4],[7]]
const values = ['A','B','A','C','B','A'];
const counts = new Map();
for (const value of values) counts.set(value,(counts.get(value) ?? 0)+1);
console.log(values.filter(v => counts.get(v) >= 2)); // ['A','B','A','B','A']
async function filterAsync(array,predicate) {
    const result = [];
    for (let i = 0; i < array.length; i++) {
        if (!(i in array)) continue;
        const value = array[i];
        if (await predicate(value,i,array)) result.push(value);
    }
    return result;
}
async function filterAsyncConcurrent(array,predicate) {
    const values = [...array];
    const decisions = await Promise.all(values.map(predicate));
    return values.filter((value,index) => decisions[index]);
}
```

Native filter does not await promises; promises are truthy. Concurrent example assumes dense arrays. Prefix operations stop at the first failure; ordinary filtering does not. Run collection preserves group boundaries.

## 23. Performance and memory considerations

| Operation | Time | Extra space |
|---|---|---|
|filter or plain loop|O(n)|O(k)|
|In-place compaction|O(n)|O(1)|
|Binary partition|O(n)|O(n)|
|Unstable partition|O(n)|O(1)|
|Repeated splice|O(n^2) worst case|Varies|
|Nested membership|O(n*m)|O(k)|
|Set membership|Expected O(n+m)|O(m+k)|
|indexOf deduplication|O(n^2)|O(k)|
|Set deduplication|Expected O(n)|O(k)|

```javascript
const result = numbers.filter(n => n > 0).map(n => n*2).filter(n => n >= 20);
// Fused equivalent for dense arrays and pure callbacks:
const fused = [];
for (const number of numbers) {
    if (number > 0) {
        const transformed = number*2;
        if (transformed >= 20) fused.push(transformed);
    }
}
```

For 100,000 elements and 50% first-stage acceptance, chaining visits 200,000 array elements versus 100,000 in the fused loop. Both are O(n). Benchmark actual performance.

## 24. Common mistakes and edge cases

1. Forgetting return in a block-bodied callback.
2. Expecting filter to transform values; use map.
3. Expecting filter to mutate the original array.
4. Assignment (`=`) instead of comparison (`===`).
5. Testing an empty array as Boolean instead of checking length.
6. Removing meaningful falsy values with Boolean.
7. Incorrect AND/OR precedence; use parentheses.
8. Comparing objects by structure using includes; compare keys.
9. Passing async predicates directly to filter.
10. Passing String.prototype.includes without a receiver; wrap in an arrow function.
11. Assuming sparse slots are always visited.
12. Confusing grouping, partitioning and overlapping classification.

## 25. Progressive exercises and solutions

### Level 1 - Fundamental filtering

1. Collect all positive numbers.
2. Collect all negative numbers.
3. Collect all even numbers.
4. Collect all odd numbers.
5. Collect numbers divisible by both 4 and 6.
6. Collect finite numbers from a mixed array.
7. Remove null and undefined without removing other falsy values.
8. Collect elements at even indexes.
9. Collect strings longer than five code units.
10. Implement filter() using a basic plain loop.

### Level 2 - Objects and conditions

11. Collect available products.
12. Collect active users above a specified age.
13. Filter objects by a dynamically supplied property.
14. Collect objects that own a specified property.
15. Filter records using three AND conditions.
16. Filter records using combined AND and OR conditions.
17. Implement optional filtering criteria.
18. Filter strings using a case-insensitive search query.
19. Collect objects matching a nested property.
20. Collect projects containing at least one incomplete task.

### Level 3 - Membership and uniqueness

21. Collect elements belonging to an allowed list.
22. Remove elements belonging to a blocked list.
23. Implement membership filtering using nested loops.
24. Optimize membership filtering using Set.
25. Remove duplicate primitive values without Set.
26. Remove duplicate values using Set.
27. Retain only the last occurrence of each value.
28. Remove duplicate objects by identifier.
29. Implement a reusable uniqueBy() function.
30. Remove duplicates using two independent properties.

### Level 4 - Partitioning and mutation

31. Partition numbers into positive and nonpositive groups.
32. Partition students into passed and failed groups.
33. Implement reusable binary partitioning.
34. Separate values into three numerical ranges.
35. Group objects by dynamic categories.
36. Classify elements into overlapping categories.
37. Remove matching elements using backward splice().
38. Implement stable in-place filtering.
39. Implement filterRangeInPlace().
40. Implement unstable two-pointer partitioning.

### Level 5 - Collection algorithms

41. Implement unique intersection.
42. Implement unique union.
43. Implement directional difference.
44. Implement symmetric difference.
45. Implement multiset intersection.
46. Implement multiset difference.
47. Collect the first N matching elements.
48. Skip the first N matching elements.
49. Implement takeWhile().
50. Implement dropWhile().

### Level 6 - Advanced problem-solving

51. Collect all strict local peaks.
52. Collect all strict local valleys.
53. Collect consecutive runs satisfying a predicate.
54. Retain values occurring at least three times.
55. Retain values occurring exactly once.
56. Filter using an arbitrary number of predicates.
57. Generate conditional count, sum, minimum and maximum in one loop.
58. Implement paginated filtering without building all matching results.
59. Implement stable in-place deduplication.
60. Implement sequential asynchronous filtering.

### Selected solution 1 - Exercise 17

```javascript
function filterVehicles(vehicles, options = {}) {
    const result = [];
    for (const vehicle of vehicles) {
        if (options.manufacturer !== undefined && vehicle.manufacturer !== options.manufacturer) continue;
        if (options.minYear !== undefined && vehicle.year < options.minYear) continue;
        if (options.fuel !== undefined && vehicle.fuel !== options.fuel) continue;
        result.push(vehicle);
    }
    return result;
}
const vehicles = [
    {manufacturer:'A',year:2020,fuel:'electric'},
    {manufacturer:'B',year:2024,fuel:'diesel'},
    {manufacturer:'A',year:2025,fuel:'electric'}
];
console.log(filterVehicles(vehicles,{manufacturer:'A',minYear:2023}));
// [{manufacturer:'A',year:2025,fuel:'electric'}]
```

### Selected solution 2 - Exercise 33

```javascript
function partition(array,predicate) {
    const accepted = [], rejected = [];
    for (const value of array) {
        const destination = predicate(value) ? accepted : rejected;
        destination.push(value);
    }
    return {accepted,rejected};
}
console.log(partition([5,12,3,18,7],n => n >= 10));
// {accepted:[12,18],rejected:[5,3,7]}
```

### Selected solution 3 - Exercise 45

```javascript
function intersectWithDuplicates(a,b) {
    const frequencies = new Map(), result = [];
    for (const value of b) frequencies.set(value,(frequencies.get(value) ?? 0)+1);
    for (const value of a) {
        const available = frequencies.get(value) ?? 0;
        if (available === 0) continue;
        result.push(value);
        frequencies.set(value,available-1);
    }
    return result;
}
console.log(intersectWithDuplicates([2,2,2,5,8],[2,2,8,9])); // [2,2,8]
```

### Selected solution 4 - Exercise 52

```javascript
function findValleys(array) {
    const result = [];
    for (let i = 1; i < array.length-1; i++) {
        if (array[i] < array[i-1] && array[i] < array[i+1]) {
            result.push({index:i,value:array[i]});
        }
    }
    return result;
}
console.log(findValleys([20,8,15,4,18,6,12]));
// [{index:1,value:8},{index:3,value:4},{index:5,value:6}]
```

### Selected solution 5 - Exercise 55

```javascript
function uniqueOccurrences(array) {
    const counts = new Map();
    for (const value of array) counts.set(value,(counts.get(value) ?? 0)+1);
    const result = [];
    for (const value of array) {
        if (counts.get(value) === 1) result.push(value);
    }
    return result;
}
console.log(uniqueOccurrences([4,7,4,2,9,7,12])); // [2,9,12]
```

### Selected solution 6 - Exercise 58

```javascript
function filterPage(array,predicate,page,pageSize) {
    if (!Number.isSafeInteger(page) || page < 1) throw new RangeError('Invalid page');
    if (!Number.isSafeInteger(pageSize) || pageSize < 1) throw new RangeError('Invalid page size');
    const skip = (page-1)*pageSize;
    if (!Number.isSafeInteger(skip)) throw new RangeError('Page offset is too large');
    let matched = 0;
    const result = [];
    for (const value of array) {
        if (!predicate(value)) continue;
        if (matched < skip) {
            matched++;
            continue;
        }
        result.push(value);
        if (result.length === pageSize) break;
    }
    return result;
}
console.log(filterPage([2,3,4,5,6,7,8,9,10,11,12,13,14],n => n % 2 === 0,2,3));
// [8,10,12]
```

Stores at most pageSize elements. Total page count requires a separate count. Static pagination for even integers 1 through 24, page size 3: page 1 [2,4,6], page 2 [8,10,12], page 3 [14,16,18], page 4 [20,22,24].

### Selected solution 7 - Exercise 59

```javascript
function uniqueInPlace(array) {
    const seen = new Set();
    let write = 0;
    for (let read = 0; read < array.length; read++) {
        const value = array[read];
        if (!seen.has(value)) {
            seen.add(value);
            array[write] = value;
            write++;
        }
    }
    array.length = write;
    return array;
}
console.log(uniqueInPlace([4,7,4,2,7,9,2])); // [4,7,2,9]
```

Expected O(n) time and O(k) lookup storage.

## 26. Coverage tracker and references

### Capability inventory

- [x] Conditional collection
- [x] Primitive filtering
- [x] Object filtering
- [x] Index-based filtering
- [x] Neighbor-based filtering
- [x] Multiple conditions
- [x] Dynamic property filtering
- [x] Optional criteria
- [x] Predicate composition
- [x] Range filtering
- [x] Multiple-range filtering
- [x] String filtering
- [x] Nested property filtering
- [x] Nested collection filtering
- [x] Membership filtering
- [x] Exclusion filtering
- [x] Primitive deduplication
- [x] Object deduplication
- [x] Composite-key deduplication
- [x] Sparse array filtering
- [x] Binary partitioning
- [x] Multi-category partitioning
- [x] Dynamic grouping
- [x] Overlapping classification
- [x] Stable in-place compaction
- [x] Unstable partitioning
- [x] Intersection
- [x] Union
- [x] Difference
- [x] Symmetric difference
- [x] Multiset operations
- [x] Conditional transformation
- [x] Conditional counting
- [x] Conditional accumulation
- [x] Conditional statistics
- [x] Limited collection
- [x] Conditional pagination
- [x] Prefix collection
- [x] Consecutive-run collection
- [x] Frequency-based filtering
- [x] Asynchronous filtering

### API coverage and deferred material

filter, callback parameters, truthiness, thisArg, generic invocation, sparse behavior, mutation, shallow copying and manual implementation are covered. includes, indexOf, lastIndexOf, Set and Map methods are applied. Object.groupBy, Map.groupBy and modern Set operations are introduced. some, every, map and reduce receive further coverage later.

Deferred: complete Map and Set APIs, advanced nested classification, lazy iterators, recursive tree filtering, streaming, concurrency limits, DOM filtering, regular expressions, array species and typed arrays.

### Quick reference

```javascript
array.filter(predicate);
array.filter(value => !predicate(value));
array.filter(item => item.active);
array.filter((value,index) => index % 2 === 0);
array.filter(value => value >= min && value <= max);
array.filter(value => value !== null && value !== undefined);
array.filter(Boolean);
[...new Set(array)];
const lookup = new Set(b);
const intersection = [...new Set(a)].filter(value => lookup.has(value));
const difference = a.filter(value => !lookup.has(value));
array.filter(predicate).length;
array.filter(predicate).map(transform);
array.filter(predicate).reduce(reducer,initialValue);
```

### References

- [JavaScript.info - Array methods](https://javascript.info/array-methods)
- [MDN - Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
- [MDN - Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [MDN - Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [ECMAScript - Indexed collections](https://tc39.es/ecma262/multipage/indexed-collections.html)

26 sections, 41 capabilities, 60 exercises, 7 selected solutions.

[Previous: Part 4](array-methods-and-loops-part-4.md) | [Next: Part 6 - Transforming Arrays and Data Structures](array-methods-and-loops-part-6.md)
