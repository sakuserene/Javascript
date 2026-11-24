# JavaScript Arrays

Source: [JavaScript.info â€” Arrays](https://javascript.info/array), with additional notes supplied by the user.

## Table of Contents

- [Introduction](#introduction)
- [Declaration](#declaration)
- [Get last elements with at](#get-last-elements-with-at)
- [Adding elements and return values](#adding-elements-and-return-values)
- [Methods pop/push, shift/unshift](#methods-poppush-shiftunshift)
- [Internals](#internals)
- [Performance](#performance)
- [Loops](#loops)
- [A word about length](#a-word-about-length)
- [new Array()](#new-array)
- [Multidimensional arrays](#multidimensional-arrays)
- [toString](#tostring)
- [Don't compare arrays with ==](#dont-compare-arrays-with-)
- [Summary](#summary)
- [Tasks](#tasks)

## Introduction

Objects allow you to store keyed collections of values. But quite often we need an *ordered collection*, where we have a first, second, third element and so on. For example, we need to store a list of users, goods or HTML elements.

Objects are not designed to manage elements by their position. There exists a special data structure named `Array` to store ordered collections.

## Declaration

There are two syntaxes for creating an empty array:

```javascript
let arr = new Array();
let arr = [];
```

Almost all the time, the second syntax is used. We can supply initial elements in the brackets:

```javascript
let fruits = ["Apple", "Orange", "Plum"];
```

Array elements are numbered, starting with zero. We can get an element by its number in square brackets:

```javascript
let fruits = ["Apple", "Orange", "Plum"];

alert(fruits[0]); // Apple
alert(fruits[1]); // Orange
alert(fruits[2]); // Plum
```

We can replace an element:

```javascript
fruits[2] = 'Pear'; // ["Apple", "Orange", "Pear"]
```

Or add a new one:

```javascript
fruits[3] = 'Lemon'; // ["Apple", "Orange", "Pear", "Lemon"]
```

The `length` property gives the length of the array:

```javascript
let fruits = ["Apple", "Orange", "Plum"];
alert(fruits.length); // 3
alert(fruits); // Apple,Orange,Plum
```

An array can store elements of any type:

```javascript
let arr = ['Apple', { name: 'John' }, true, function() { alert('hello'); }];

alert(arr[1].name); // John
arr[3](); // hello
```

### Trailing comma

An array, just like an object, may end with a comma:

```javascript
let fruits = [
  "Apple",
  "Orange",
  "Plum",
];
```

The trailing comma style makes it easier to insert or remove items because all lines become alike.

## Get last elements with at

Older browsers may need [polyfills](https://javascript.info/polyfills) for `at()`.

Unlike some languages, JavaScript does not interpret `fruits[-1]` as the last element. Normally it returns `undefined`.

```javascript
let fruits = ["Apple", "Orange", "Plum"];

alert(fruits[fruits.length - 1]); // Plum
alert(fruits.at(-1)); // Plum
```

For integer indexes, `arr.at(i)` works like `arr[i]` for nonnegative indexes within the array, and negative indexes count backward from the end.

## Adding elements and return values

For ordinary arrays, `fruits.push(item)` and `fruits[fruits.length] = item` both append a single element. They differ in return value and support for multiple arguments.

### push() returns the new length

```javascript
const fruits = ['apple', 'banana'];
const result = fruits.push('orange');
console.log(fruits); // ['apple', 'banana', 'orange']
console.log(result); // 3
```

### Index assignment evaluates to the assigned value

An assignment expression `x = y` evaluates to the assigned value.

```javascript
const fruits = ['apple', 'banana'];
const result = (fruits[fruits.length] = 'orange');
console.log(fruits); // ['apple', 'banana', 'orange']
console.log(result); // 'orange'
```

### Why this matters in practice

```javascript
const addWithPush = (arr, item) => arr.push(item);
const addWithIndex = (arr, item) => (arr[arr.length] = item);

console.log(addWithPush(['apple', 'banana'], 'orange')); // 3
console.log(addWithIndex(['apple', 'banana'], 'orange')); // orange
```

### Multiple items

```javascript
const listA = ['a'];
listA.push('b', 'c');
console.log(listA); // ['a', 'b', 'c']

const listB = ['a'];
// listB[listB.length] = 'b', 'c'; // Only assigns 'b'
listB[listB.length] = 'b';
listB[listB.length] = 'c';
console.log(listB); // ['a', 'b', 'c']
```

### Common bug when returning from a function

```javascript
const getUpdatedListWrong = (arr, item) => arr.push(item);
console.log(getUpdatedListWrong(['x', 'y'], 'z')); // 3, not the array
```

Use `push()` when appending items. To create a new array without modifying the original, use `[...arr, item]`.

### Length versus index

```javascript
const fruits = ['apple', 'banana', 'orange'];
console.log(fruits.length); // 3
console.log(fruits[0]); // apple
console.log(fruits[1]); // banana
console.log(fruits[2]); // orange
```

Indexes start at zero. An empty array has length zero. For a dense array, length is the number of items and is one greater than the last index. For `['apple', 'banana']`, `fruits[fruits.length]` targets index 2, the next position.

## Methods pop/push, shift/unshift

A queue is an ordered collection that supports `push` to append and `shift` to remove the first element. It follows FIFO (First-In-First-Out), for example a queue of messages.

A stack supports `push` to add and `pop` to remove from the end, like a pack of cards. It follows LIFO (Last-In-First-Out).

JavaScript arrays support both ends, allowing them to be used as a deque.

### pop

Extracts the last element and returns it:

```javascript
let fruits = ["Apple", "Orange", "Pear"];
alert(fruits.pop()); // Pear
alert(fruits); // Apple,Orange
```

Unlike `at(-1)`, `pop()` removes the element.

### push

Appends an element and returns the new length:

```javascript
let fruits = ["Apple", "Orange"];
fruits.push("Pear");
alert(fruits); // Apple,Orange,Pear
```

### shift

Extracts the first element and returns it:

```javascript
let fruits = ["Apple", "Orange", "Pear"];
alert(fruits.shift()); // Apple
alert(fruits); // Orange,Pear
```

### unshift

Adds elements to the beginning and returns the new length:

```javascript
let fruits = ["Orange", "Pear"];
fruits.unshift('Apple');
alert(fruits); // Apple,Orange,Pear
```

Both `push` and `unshift` accept multiple elements:

```javascript
let fruits = ["Apple"];
fruits.push("Orange", "Peach");
fruits.unshift("Pineapple", "Lemon");
alert(fruits); // Pineapple,Lemon,Apple,Orange,Peach
```

## Internals

An array is a special kind of object. The square brackets used in `arr[0]` come from object property access syntax, like `obj[key]`. Arrays provide special methods for ordered collections and a `length` property.

Arrays are objects and assigning one variable to another copies the reference:

```javascript
let fruits = ["Banana"];
let arr = fruits;
alert(arr === fruits); // true
arr.push("Pear");
alert(fruits); // Banana,Pear
```

Engines can optimize arrays containing contiguous ordered data. Misusing arrays can interfere with these optimizations, depending on the engine:

```javascript
let fruits = [];
fruits[99999] = 5;
fruits.age = 25;
```

Avoid unnecessary named properties, large gaps between indexes and sparse arrays created by filling distant indexes first. Use regular objects when you need arbitrary keys.

## Performance

`push` and `pop` are generally faster than `shift` and `unshift` on ordinary large arrays.

```javascript
fruits.shift();
```

Conceptually, shift removes index 0, moves subsequent elements left and updates length. Unshift moves existing elements right to make room.

```javascript
fruits.pop();
```

Pop removes the final element and shortens length without renumbering other elements. Push similarly appends without shifting existing elements. Actual performance depends on the engine.

## Loops

### for

```javascript
let arr = ["Apple", "Orange", "Pear"];
for (let i = 0; i < arr.length; i++) {
  alert(arr[i]);
}
```

### for...of

```javascript
let fruits = ["Apple", "Orange", "Plum"];
for (let fruit of fruits) {
  alert(fruit);
}
```

`for...of` provides values directly, rather than their indexes.

### for...in

```javascript
let arr = ["Apple", "Orange", "Pear"];
for (let key in arr) {
  alert(arr[key]);
}
```

Generally avoid `for...in` for arrays: it enumerates enumerable string properties, including non-index and inherited properties. Array-like objects can also have additional properties. Performance varies by engine; there is no universal speed ratio.

## A word about length

Length updates automatically. It is not necessarily the count of existing values: sparse arrays can contain holes.

```javascript
let fruits = [];
fruits[123] = "Apple";
alert(fruits.length); // 124
```

Length is writable. Increasing it creates empty slots; decreasing it truncates the array irreversibly.

```javascript
let arr = [1, 2, 3, 4, 5];
arr.length = 2;
alert(arr); // 1,2
arr.length = 5;
alert(arr[3]); // undefined
```

Clear an array with `arr.length = 0;`.

## new Array()

```javascript
let arr = new Array("Apple", "Pear", "etc");
```

A single valid numeric length argument creates an array with empty slots, not an array containing that number:

```javascript
let arr = new Array(2);
alert(arr[0]); // undefined
alert(arr.length); // 2
```

Prefer square brackets unless you specifically need the constructor.

## Multidimensional arrays

Arrays can contain other arrays, for example a matrix:

```javascript
let matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
alert(matrix[0][1]); // 2
```

## toString

Arrays have their own `toString()` implementation that normally returns comma-separated elements.

```javascript
let arr = [1, 2, 3];
alert(arr); // 1,2,3
alert(String(arr) === '1,2,3'); // true
```

```javascript
alert([] + 1); // "1"
alert([1] + 1); // "11"
alert([1, 2] + 1); // "1,21"
```

Ordinary arrays convert to strings during these operations. The binary plus operator concatenates when one primitive operand is a string:

```javascript
alert("" + 1); // "1"
alert("1" + 1); // "11"
alert("1,2" + 1); // "1,21"
```

## Don't compare arrays with ==

Equality operators do not compare array elements individually. Two array objects are equal only if they refer to the same object. Loose equality can convert objects to primitives when compared with primitives (except null and undefined).

```javascript
alert([] == []); // false
alert([0] == [0]); // false
alert(0 == []); // true
alert('0' == []); // false
```

The empty array converts to an empty string in the last two comparisons:

```javascript
alert(0 == ''); // true
alert('0' == ''); // false
```

Strict equality does not perform type conversion. To compare array contents, compare items individually using loops or iteration methods.

## Summary

| Operation | Description | Return value |
| --- | --- | --- |
| `arr[i]` | Access by index | Element |
| `arr.at(i)` | Access with positive or negative index | Element or undefined |
| `arr.push(...items)` | Append items | New length |
| `arr.pop()` | Remove last item | Removed item or undefined |
| `arr.shift()` | Remove first item | Removed item or undefined |
| `arr.unshift(...items)` | Prepend items | New length |
| `arr.length` | Array length | Number |

Use array literals for declarations. Shortening length truncates the array. Use `for` for indexed iteration or `for...of` for values. Avoid `for...in` for ordinary array traversal. Compare contents item by item rather than using equality or relational operators.

Next chapter: [Array methods](https://javascript.info/array-methods).

## Tasks

### 1. Is array copied?

**Importance: 3/5**

What is this code going to show?

```javascript
let fruits = ["Apples", "Pear", "Orange"];
let shoppingCart = fruits;
shoppingCart.push("Banana");
alert(fruits.length); // ?
```

### 2. Array operations

**Importance: 5/5**

1. Create an array `styles` with items Jazz and Blues.
2. Append Rock-n-Roll to the end.
3. Replace the middle value with Classics. Your code should work for any odd-length array.
4. Remove the first value and show it.
5. Prepend Rap and Reggae.

Expected progression:

```text
Jazz, Blues
Jazz, Blues, Rock-n-Roll
Jazz, Classics, Rock-n-Roll
Classics, Rock-n-Roll
Rap, Reggae, Classics, Rock-n-Roll
```

### 3. Calling in an array context

**Importance: 5/5**

What is the result? Why?

```javascript
let arr = ["a", "b"];
arr.push(function() {
  alert(this);
});
arr[2](); // ?
```

### 4. Sum input numbers

**Importance: 4/5**

Write `sumInput()` that asks for values using `prompt`, stores them in an array, stops on non-numeric input, an empty string or Cancel, and calculates and returns the sum. Zero is a valid input.

### 5. A maximal subarray

**Importance: 2/5**

Given an array of numbers, find the contiguous subarray with the maximal sum. Write `getMaxSubSum(arr)` to return that sum.

```javascript
getMaxSubSum([-1, 2, 3, -9]); // 5
getMaxSubSum([2, -1, 2, 3, -9]); // 6
getMaxSubSum([-1, 2, 3, -9, 11]); // 11
getMaxSubSum([-2, -1, 1, 2]); // 3
getMaxSubSum([100, -9, 2, -3, 5]); // 100
getMaxSubSum([1, 2, 3]); // 6
getMaxSubSum([-1, -2, -3]); // 0
```

If all items are negative, take the empty subarray, whose sum is zero. Try an O(nÂ²) or O(n) solution.

[Original exercises and solutions](https://javascript.info/array#tasks)
