# Module 7 — Array Methods That Use Callbacks
Welcome to one of the most practical modules in JavaScript.
From this point onward, you'll start recognizing callback functions in almost every JavaScript project.
We'll study one array method at a time.
Today's lesson is:
Part 1 — forEach()
Although we've already introduced it, now we'll understand it deeply.
Why Does forEach() Exist?
Suppose you have a list of students.
const students = ["Adi", "John", "Mary"];
You want to print every student's name.
Without forEach(), you write:
for (let i = 0; i < students.length; i++) {
    console.log(students[i]);
}
This works.
But JavaScript developers noticed something.
Most loops look almost identical.
Only this part changes:
console.log(students[i]);
Sometimes you print.
Sometimes you calculate.
Sometimes you update.
Sometimes you save.
Everything else is the same.
So JavaScript created forEach().
What Does forEach() Do?
forEach() says:
"I'll take care of the loop."
You only tell me:
"What should I do for each item?"
That instruction is the callback.
Basic Syntax
array.forEach(callback);
Example:
const fruits = ["Apple", "Banana", "Orange"];

function printFruit(fruit) {
    console.log(fruit);
}

fruits.forEach(printFruit);
Output
Apple
Banana
Orange
What Happens Internally?
Imagine JavaScript secretly writes this:
printFruit("Apple");

printFruit("Banana");

printFruit("Orange");
Or even:
for (let i = 0; i < fruits.length; i++) {
    printFruit(fruits[i]);
}
You never see this code.
forEach() does it for you.
Visual Animation
fruits

↓

["Apple", "Banana", "Orange"]

↓

forEach()

↓

Callback("Apple")

↓

Callback("Banana")

↓

Callback("Orange")

↓

Finished
The Callback Receives the Current Item
Look carefully.
const fruits = ["Apple", "Banana", "Orange"];

fruits.forEach(function (fruit) {
    console.log(fruit);
});
Where did fruit come from?
You didn't create it.
JavaScript did.
Iteration 1
fruit = "Apple"
Iteration 2
fruit = "Banana"
Iteration 3
fruit = "Orange"
JavaScript automatically passes the current item into your callback.
Callback Can Also Receive the Index
There is a second parameter.
const fruits = ["Apple", "Banana", "Orange"];

fruits.forEach(function (fruit, index) {
    console.log(index, fruit);
});
Output
0 Apple
1 Banana
2 Orange
Flow
First iteration
fruit = "Apple"

index = 0
Second iteration
fruit = "Banana"

index = 1
Third iteration
fruit = "Orange"

index = 2
Callback Can Also Receive the Entire Array
There is even a third parameter.
const fruits = ["Apple", "Banana", "Orange"];

fruits.forEach(function (fruit, index, array) {
    console.log(array);
});
Output
["Apple", "Banana", "Orange"]
["Apple", "Banana", "Orange"]
["Apple", "Banana", "Orange"]
Notice:
The callback receives:
Current item
Current index
Entire array
Why Would You Need the Index?
Suppose you want numbered output.
const students = ["Ali", "Jane", "Peter"];

students.forEach(function(student, index) {
    console.log((index + 1) + ". " + student);
});
Output
1. Ali
2. Jane
3. Peter
Without the index, numbering would be harder.
Real-World Example — Classroom Attendance
Imagine a teacher.
Students:
Alice

Brian

Carol
Teacher walks through the attendance list.
For every student:
Call Name

↓

Mark Present
JavaScript does exactly the same.
Student 1

↓

Callback Runs

↓

Student 2

↓

Callback Runs

↓

Student 3

↓

Callback Runs
Common Mistake
Wrong:
fruits.forEach(printFruit());
Why?
Because:
printFruit()
runs immediately.
Then its return value is passed.
Correct:
fruits.forEach(printFruit);
Pass the function itself.
Let forEach() decide when to execute it.
What Can You Do Inside forEach()?
Anything!
Example 1 — Print
numbers.forEach(function(number) {
    console.log(number);
});
Example 2 — Multiply
numbers.forEach(function(number) {
    console.log(number * 2);
});
Example 3 — Check Even Numbers
numbers.forEach(function(number) {
    if (number % 2 === 0) {
        console.log(number + " is even");
    }
});
Example 4 — Build a New Sentence
const names = ["Adi", "John"];

names.forEach(function(name) {
    console.log("Welcome " + name);
});
Output
Welcome Adi
Welcome John
Important Limitation of forEach()
Many beginners make this mistake.
const numbers = [1, 2, 3];

const result = numbers.forEach(function(number) {
    return number * 2;
});

console.log(result);
What do you think result will be?
Many people expect:
[2, 4, 6]
But the actual output is:
undefined
Why?
Because forEach() is designed to perform an action, not to create a new array.
If you want a new array, you'll use map(), which we'll learn next.
Summary
forEach():
Loops through every element in an array.
Executes your callback once per element.
Automatically passes:
the current item,
the current index (optional),
and the entire array (optional).
Is best used for actions like printing, logging, updating the DOM, or calling other functions.
Does not return a new array.
Check Your Understanding
Without running the code, answer these questions:
1. What will this print?
const numbers = [5, 10, 15];

numbers.forEach(function(number) {
    console.log(number);
});
2. What are the three parameters that a forEach() callback can receive?
3. Why is this incorrect?
numbers.forEach(showNumber());
4. What is the output?
const names = ["Sam", "Grace"];

names.forEach(function(name, index) {
    console.log(index + ": " + name);
});
5. Does forEach() create a new array? Why or why not?
Practice Exercises
Easy
Create an array of five cities and print each city using forEach().
Print each city together with its index.
Create an array of ages and print whether each age is an adult (18+) or a minor.
Medium
Create an array of products and print "Product 1: Laptop", "Product 2: Phone", etc., using the index.
Create an array of exam scores and print "Pass" if the score is 50 or above, otherwise print "Fail".
Explain, in your own words, why forEach() is better than writing a for loop every time for simple actions.
Next Lesson
The next method is map(), and it's one of the most important array methods in JavaScript.
You'll learn:
Why forEach() cannot create a new array.
How map() solves that problem.
How callbacks work inside map().
When to choose map() instead of forEach().
This is where callback functions become even more powerful in real-world applications.
