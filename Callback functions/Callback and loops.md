# Module 6 — Callbacks and Loops
This is where callbacks start becoming extremely useful.
Until now, you've been passing functions manually like this:
run(sayHello);
But now you'll see something amazing:
JavaScript itself can call your callback repeatedly inside a loop.
This is exactly how methods like forEach(), map(), and filter() work.
First, Let's Review a Normal for Loop
Suppose we have an array.
const fruits = ["Apple", "Banana", "Orange"];
A normal for loop looks like this:
const fruits = ["Apple", "Banana", "Orange"];

for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}
Output
Apple
Banana
Orange
Execution Flow
Start

↓

i = 0

↓

Print Apple

↓

i = 1

↓

Print Banana

↓

i = 2

↓

Print Orange

↓

Loop Ends
Notice something.
You wrote the loop.
You controlled:
where it starts
where it ends
when it repeats
for...of Loop
JavaScript also gives us another loop.
const fruits = ["Apple", "Banana", "Orange"];

for (const fruit of fruits) {
    console.log(fruit);
}
Output
Apple
Banana
Orange
Flow
Apple

↓

Banana

↓

Orange
Again,
You control the loop.
Now Meet forEach()
Look carefully.
const fruits = ["Apple", "Banana", "Orange"];

fruits.forEach(function (fruit) {
    console.log(fruit);
});
Output
Apple
Banana
Orange
Looks similar...
But something important has changed.
Where Is the Loop?
Look again.
fruits.forEach(function (fruit) {
    console.log(fruit);
});
Can you see:
for (...)
No.
Can you see:
while (...)
No.
So...
Who is looping?
Answer:
forEach() is looping internally.
Understanding forEach()
Imagine JavaScript secretly does something like this:
for (let i = 0; i < fruits.length; i++) {
    callback(fruits[i]);
}
You never write this loop.
JavaScript writes it for you.
Visual Diagram
fruits

↓

forEach()

↓

Apple

↓

Callback Runs

↓

Banana

↓

Callback Runs

↓

Orange

↓

Callback Runs
Notice:
The callback runs once for every item.
Let's Name the Callback
Instead of writing the callback directly:
fruits.forEach(function (fruit) {
    console.log(fruit);
});
Let's create a separate function.
function printFruit(fruit) {
    console.log(fruit);
}

const fruits = ["Apple", "Banana", "Orange"];

fruits.forEach(printFruit);
Output
Apple
Banana
Orange
Execution Flow
Step 1
forEach(printFruit)
↓
JavaScript stores the callback.
↓
Reads first item.
↓
Calls
printFruit("Apple");
↓
Reads second item.
↓
Calls
printFruit("Banana");
↓
Reads third item.
↓
Calls
printFruit("Orange");
Internally
Imagine JavaScript doing this:
printFruit("Apple");

printFruit("Banana");

printFruit("Orange");
You didn't write those lines.
forEach() did.
Why Use a Callback?
Imagine forEach() without callbacks.
It might only know how to print.
fruits.forEach();
How would JavaScript know what you want?
Should it:
Print?
Save?
Delete?
Count?
Update?
It doesn't know.
So you give it instructions.
Those instructions are the callback.
Different Callbacks, Same Loop
Suppose we have:
const numbers = [1, 2, 3];
Callback 1
function print(number) {
    console.log(number);
}

numbers.forEach(print);
Output
1
2
3
Callback 2
function square(number) {
    console.log(number * number);
}

numbers.forEach(square);
Output
1
4
9
Callback 3
function double(number) {
    console.log(number * 2);
}

numbers.forEach(double);
Output
2
4
6
Notice something amazing.
The loop never changed.
Only the callback changed.
Real-World Analogy
Imagine a teacher with three students.
Alice

Bob

John
The teacher says:
"One at a time, come to my desk."
The teacher controls the order.
But what happens at the desk depends on the instructions.
Scenario 1
Student arrives

↓

Teacher says

"Collect your book"
Scenario 2
Student arrives

↓

Teacher says

"Submit homework"
Scenario 3
Student arrives

↓

Teacher says

"Answer the question"
The teacher is like forEach().
The instruction is the callback.
forEach() Calls the Callback
Remember this forever.
You do not call the callback.
This is wrong:
numbers.forEach(print());
Why?
Because
print()
runs immediately.
Instead:
numbers.forEach(print);
Now JavaScript decides when to call print().
Callback Receives the Current Item
Look carefully.
numbers.forEach(function(number) {
    console.log(number);
});
Where does number come from?
You never assigned it.
JavaScript does.
First iteration
number = 1
Second iteration
number = 2
Third iteration
number = 3
JavaScript automatically passes the current item to your callback.
Visual Execution
numbers

↓

[1,2,3]

↓

forEach()

↓

Callback(1)

↓

Callback(2)

↓

Callback(3)

↓

Finished
Comparing for and forEach()
for Loop
forEach()
You write the loop
JavaScript writes the loop
You control i
JavaScript controls iteration
You access array[i]
JavaScript passes each item to the callback
More manual
More expressive and reusable
Key Takeaways
By the end of this module, you should know:
forEach() uses a callback function.
JavaScript loops through the array internally.
Your callback runs once for each element.
The callback receives the current item automatically.
Different callbacks can be used with the same forEach() loop to achieve different results.
Check Your Understanding
Without running the code, answer these:
1. How many times will showNumber run?
const numbers = [10, 20, 30];

function showNumber(number) {
    console.log(number);
}

numbers.forEach(showNumber);
2. Who controls the loop in forEach()?
3. Why is this incorrect?
numbers.forEach(showNumber());
4. Where does the number parameter come from?
5. If you replace showNumber with a function that multiplies each number by 10, does forEach() need to change? Why?
Practice Exercises
Easy
Create an array of five colors and print each color using forEach().
Create an array of names and print "Hello, <name>" for each person using a callback.
Create an array of numbers and print their doubles using forEach().
Medium
Create an array of student names and write two different callbacks:
One prints the names in uppercase.
One prints "Present: <name>". Use forEach() with each callback.
Create an array of prices and print each price with a currency symbol (for example, KSh 500) using a callback.
Explain, in your own words, why forEach() needs a callback instead of knowing what to do by itself.
Before Module 7
You have now reached an important milestone.
You understand manual callbacks and callbacks used by loops.
In Module 7, we'll build on this knowledge to explore the most commonly used array methods—map(), filter(), find(), some(), every(), reduce(), and sort()—one at a time. You'll see that each one uses callbacks for a different purpose, and you'll learn why each method exists instead of trying to use forEach() for everything.
