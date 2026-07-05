# Module 7 (Part 3) — filter()
So far you've learned:
✅ forEach() → Do something with every item.
✅ map() → Transform every item into something new.
Now let's learn a third method that solves a completely different problem.
filter() → Keep only the items that match a condition.
Why Does filter() Exist?
Imagine you have a list of numbers.
const numbers = [10, 15, 20, 25, 30];
You only want the even numbers.
Desired result:
[10, 20, 30]
Should you use forEach()?
You could, but you'd have to create a new array manually.
Should you use map()?
No. map() transforms every item—it doesn't remove items.
This is exactly why filter() exists.
What Does filter() Do?
filter() asks one question for every item:
"Should I keep this item?"
Your callback answers with:
true → Keep it.
false → Throw it away.
Basic Syntax
array.filter(callback);
First Example
const numbers = [10, 15, 20, 25, 30];

const evenNumbers = numbers.filter(function(number) {
    return number % 2 === 0;
});

console.log(evenNumbers);
Output
[10, 20, 30]
Let's Understand the Callback
This line:
return number % 2 === 0;
does not return the number.
It returns either:
true
or
false
Let's see each iteration.
Iteration 1
number = 10

↓

10 % 2 === 0

↓

true

↓

Keep 10
New array:
[10]
Iteration 2
number = 15

↓

15 % 2 === 0

↓

false

↓

Discard 15
Array stays:
[10]
Iteration 3
number = 20

↓

20 % 2 === 0

↓

true

↓

Keep 20
Array becomes:
[10, 20]
Iteration 4
number = 25

↓

false

↓

Discard
Iteration 5
number = 30

↓

true

↓

Keep
Final result:
[10, 20, 30]
Visual Flow
Original Array

[10, 15, 20, 25, 30]

        │
        ▼

     filter()

        │
        ▼

10

↓

true

↓

KEEP

----------------

15

↓

false

↓

REMOVE

----------------

20

↓

true

↓

KEEP

----------------

25

↓

false

↓

REMOVE

----------------

30

↓

true

↓

KEEP

        │
        ▼

New Array

[10, 20, 30]
Real-World Example 1 — Students Who Passed
Suppose these are exam scores.
const scores = [35, 60, 48, 90, 75];
Passing score is 50.
const passed = scores.filter(function(score) {
    return score >= 50;
});

console.log(passed);
Output
[60, 90, 75]
Students who scored below 50 are removed.
Real-World Example 2 — Adults
const ages = [12, 18, 20, 15, 25];
Keep adults only.
const adults = ages.filter(function(age) {
    return age >= 18;
});

console.log(adults);
Output
[18, 20, 25]
Real-World Example 3 — Shopping Website
Products:
const prices = [500, 1500, 250, 3000, 800];
Customer wants products below KSh 1000.
const affordable = prices.filter(function(price) {
    return price < 1000;
});

console.log(affordable);
Output
[500, 250, 800]
This is exactly how many online stores implement price filters.
Real-World Example 4 — Todo App
const tasks = [
    { title: "Study", completed: true },
    { title: "Exercise", completed: false },
    { title: "Read", completed: true }
];
Show only completed tasks.
const completedTasks = tasks.filter(function(task) {
    return task.completed;
});

console.log(completedTasks);
Output
[
  { title: "Study", completed: true },
  { title: "Read", completed: true }
]
filter() Returns a New Array
Original:
const numbers = [1, 2, 3, 4];
Filtered:
const even = numbers.filter(function(number) {
    return number % 2 === 0;
});
Now:
numbers
is still
[1, 2, 3, 4]
while
even
is
[2, 4]
The original array is unchanged.
Comparing forEach(), map(), and filter()
Suppose we have:
const numbers = [1, 2, 3, 4];
forEach()
numbers.forEach(function(number) {
    console.log(number);
});
Purpose:
✔ Perform an action.
map()
const doubled = numbers.map(function(number) {
    return number * 2;
});
Purpose:
✔ Transform every item.
Output:
[2, 4, 6, 8]
filter()
const even = numbers.filter(function(number) {
    return number % 2 === 0;
});
Purpose:
✔ Keep only matching items.
Output:
[2, 4]
Common Beginner Mistake
Some beginners write:
const even = numbers.filter(function(number) {
    console.log(number);
});
Output:
[]
Why?
Because the callback doesn't return true or false.
Always return a condition.
Correct:
const even = numbers.filter(function(number) {
    return number % 2 === 0;
});
Easy Way to Remember
Imagine a security guard at a concert.
People arrive one by one.
For each person:
Check Ticket

↓

Valid?

↓

Yes

↓

Enter

OR

↓

No

↓

Stay Outside
The security guard is like filter().
The ticket check is your callback.
The callback decides:
true → Let them in.
false → Keep them out.
Summary
Use filter() when you want to:
Keep only some items.
Remove unwanted items.
Search lists.
Build filtered results.
The callback must return:
true → Keep.
false → Remove.
Check Your Understanding
Without running the code:
1. What will this print?
const numbers = [5, 8, 10, 13];

const result = numbers.filter(function(number) {
    return number > 8;
});

console.log(result);
2. What happens if the callback always returns true?
3. What happens if the callback always returns false?
4. Does filter() modify the original array?
5. Why can't map() replace filter()?
Practice Exercises
Easy
Create an array of numbers and use filter() to keep only odd numbers.
Create an array of ages and keep only people who are 18 or older.
Create an array of names and keep only names longer than 4 characters.
Medium
Create an array of products:
const products = [
    { name: "Laptop", price: 60000 },
    { name: "Mouse", price: 1200 },
    { name: "Keyboard", price: 2500 },
    { name: "USB Cable", price: 500 }
];
Use filter() to keep only products costing more than KSh 2,000.
Create an array of students with name and score, then use filter() to keep only students who passed (score ≥ 50).
Explain, in your own words, why filter() needs a callback and why that callback must return true or false.
What's Next?
Next, you'll learn find().
While filter() returns all matching items, find() returns only the first matching item. This is extremely useful when searching for a specific user, product, task, or record in an array.
