# Module 3 — Passing Functions (Your First Real Callback)

Now you know something very important:
Functions are values.
Since they are values, they can be passed to other functions just like numbers or strings.
This is the beginning of callback functions.
Step 1 — Passing a Number
Let's start with something familiar.
function printValue(value) {
    console.log(value);
}

printValue(10);
What happens?
printValue(10)

↓

value = 10

↓

console.log(10)

↓

10
The function receives the number 10.
Step 2 — Passing a String
function printValue(value) {
    console.log(value);
}

printValue("Hello");
Flow:
"Hello"

↓

value

↓

console.log()

↓

Hello
Again, the function receives a value.
Step 3 — Passing an Array
function printItems(items) {
    console.log(items);
}

printItems(["Apple", "Banana"]);
Flow:
["Apple", "Banana"]

↓

items

↓

console.log()
Still just passing a value.
Step 4 — Passing a Function
Now for the exciting part.
function greet() {
    console.log("Hello");
}

function execute(callback) {
    console.log("Inside execute");
}

execute(greet);
Look carefully.
We are passing:
greet
not
greet()
Nothing prints "Hello".
Why?
Because we only passed the function.
We did not execute it.
Visual Diagram
greet
(Function)

↓

Passed

↓

execute(callback)

↓

callback now refers to greet
Think of callback as another name for the same function.
Memory:
greet
   │
   ▼
Function

callback
   │
   ▼
Same Function
callback and greet point to the same function.
Step 5 — Execute the Callback
Now change the code.
function greet() {
    console.log("Hello");
}

function execute(callback) {
    callback();
}

execute(greet);
Let's follow it slowly.
Line 1
function greet() {
Create a function called greet.
Nothing runs yet.
Line 2
console.log("Hello");
This line is inside the function.
It will only run if greet() is called.
Line 3
function execute(callback) {
Create another function.
Its parameter is called callback.
This parameter will hold a function.
Line 4
callback();
Now execute the function stored inside callback.
Line 5
execute(greet);
JavaScript performs these steps:
Step 1

greet

↓

Passed into execute

↓

callback = greet

↓

callback()

↓

greet()

↓

console.log("Hello")

↓

Hello
Output:
Hello
The Magic
Notice something incredible.
We never wrote:
greet();
Instead we wrote:
callback();
Since callback refers to greet, JavaScript runs greet().
Another Example
function sayGoodMorning() {
    console.log("Good Morning");
}

function run(fn) {
    fn();
}

run(sayGoodMorning);
Flow:
sayGoodMorning

↓

run()

↓

fn

↓

fn()

↓

Good Morning
Output:
Good Morning
Another Example
function clap() {
    console.log("👏");
}

function perform(action) {
    action();
}

perform(clap);
Flow:
clap

↓

action

↓

action()

↓

👏
Again, action is just another variable pointing to the clap function.
Why Is It Called a Callback?
Suppose someone says:
"When you're finished cleaning, call me."
You don't call immediately.
You call later.
Similarly:
Program starts

↓

Another function receives your callback

↓

It waits

↓

Later...

↓

It calls your function
That's why it's called a callback.
It "calls back" your function when it's ready.
Real-World Example 1 — Restaurant
Imagine this code conceptually:
Customer orders food

↓

Restaurant receives customer's phone number

↓

Chef cooks

↓

Food ready

↓

Restaurant calls customer
Mapping to JavaScript:
Phone number

↓

Callback function

Restaurant

↓

execute()

Food ready

↓

callback()
The restaurant decides when to call.
Real-World Example 2 — Taxi
Book taxi

↓

Driver receives your number

↓

Driver drives

↓

Driver arrives

↓

Calls you
In JavaScript:
Your callback

↓

Taxi system stores it

↓

Taxi arrives

↓

callback()
Real-World Example 3 — Hospital
Register

↓

Nurse stores your details

↓

Doctor becomes free

↓

Nurse calls your name
The nurse is like the function that executes your callback.
Passing vs Executing
This is the biggest source of confusion.
Passing
execute(greet);
Diagram:
greet

↓

Pass function

↓

execute receives it
Nothing runs yet.
Executing
greet();
Diagram:
greet()

↓

Runs immediately

↓

Prints Hello
Common Beginner Mistake
Consider this code:
function greet() {
    console.log("Hello");
}

function execute(callback) {
    callback();
}

execute(greet());
What happens?
Step 1
greet() runs immediately.
Output:
Hello
Step 2
greet() doesn't return a function. It returns undefined.
So JavaScript effectively does:
execute(undefined);
Inside execute:
callback();
becomes:
undefined();
Result:
TypeError: callback is not a function
The Correct Way
execute(greet);
Pass the function itself, not the result of calling it.
Mental Model
When you see this:
someFunction(otherFunction);
Read it as:
"Here is a function. You decide when to run it."
When you see this:
otherFunction();
Read it as:
"Run this function now."
Summary
By the end of this module, you should know:
A callback is a function passed into another function.
The receiving function decides when to execute it.
greet means "pass the function."
greet() means "execute the function immediately."
Parameters like callback, fn, or action are just variables that can hold a function.
Check Your Understanding
Without running the code, answer these:
1. What is printed?
function hello() {
    console.log("Hi");
}

function run(fn) {
    fn();
}

run(hello);
2. Which line actually executes the hello function?
run(hello);
fn();
3. Why does run(hello()) cause a problem?
4. In this code:
function clap() {
    console.log("👏");
}

function perform(action) {
    action();
}

perform(clap);
What does the variable action refer to?
Practice Exercises
Easy
Create a function sayHello and pass it to another function called run.
Create a function wave that prints "👋" and execute it through a callback.
Rename the callback parameter from callback to myFunction and verify it still works.
Explain the difference between run(sayHello) and run(sayHello()).
Draw a flow diagram showing how a callback moves from one function to another.
Medium
Create three functions: add, subtract, and multiply. Write a calculate function that accepts one of them as a callback and executes it.
Write a performTask function that receives different callbacks like cleanRoom, washCar, or study, and runs whichever one is passed.
Explain, in your own words, why callback parameter names (callback, fn, action) don't matter as long as they refer to the passed function.
In Module 4, we'll build callback functions step by step, starting from ordinary functions and gradually transforming them into callbacks so you can clearly see the evolution rather than jumping straight to the final pattern.
