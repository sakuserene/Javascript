# Module 2 — Functions Are Values

This module is the foundation of callback functions.
If you deeply understand this module, callbacks will become much easier.
The Big Idea
Most beginners think:
"Functions only exist to run code."
That is true...
But it is not the whole truth.
In JavaScript,
Functions are values.
Just like:
Numbers
Strings
Booleans
Arrays
Objects
Functions are also values.
That means they can be:
Stored
Passed around
Returned
Assigned to variables
This is what makes callbacks possible.
Let's Compare Different Values
A number is a value.
let age = 20;
Memory:
age
 │
 ▼
20
A string is also a value.
let name = "John";
Memory:
name
 │
 ▼
"John"
An array is a value.
let fruits = ["Apple", "Orange"];
Memory:
fruits
 │
 ▼
["Apple", "Orange"]
Now the surprising part...
A function is also a value.
function greet() {
    console.log("Hello");
}
This creates a function object in memory.
greet
 │
 ▼
Function Object
Notice:
We have not called it yet.
We've only created it.
Creating vs Executing
Look carefully.
function greet() {
    console.log("Hello");
}
This creates the function.
Nothing is printed.
Now:
greet();
Output:
Hello
Now the function executes.
Think of a TV Remote
Imagine this remote.
📺 TV Remote
Owning the remote does not turn on the TV.
Only pressing the power button does.
Similarly:
greet
means:
"Here is the function."
While
greet();
means:
"Run the function."
This is one of the most important ideas in JavaScript.
Functions Can Be Stored in Variables
Because functions are values...
We can store them.
function greet() {
    console.log("Hello");
}

let sayHello = greet;
Memory:
greet
   │
   ▼
Function

sayHello
   │
   ▼
Same Function
Both variables point to the same function.
Now:
sayHello();
Output:
Hello
Notice:
We never copied the function.
Both variables reference the same function.
Functions Can Be Passed Around
Suppose I have this function.
function greet() {
    console.log("Hello");
}
Now another function.
function useFunction(myFunction) {
    console.log("I received a function.");
}
Calling:
useFunction(greet);
Diagram:
greet
 │
 ▼
Passed
 │
 ▼
useFunction()
Nothing has executed yet.
We only passed the function.
Why No Parentheses?
This confuses almost every beginner.
Look carefully.
Example 1
useFunction(greet);
Means:
Pass the function.
Diagram:
greet

↓

useFunction receives it

↓

Stores it
Example 2
useFunction(greet());
Means:
Run greet()

↓

Return its result

↓

Pass the result
Very different.
A Delivery Analogy
Imagine you own a bakery.
You have a recipe.
Recipe
Giving someone the recipe means:
They can bake later.
That is:
greet
Now imagine baking the cake yourself.
Then giving them the cake.
That is:
greet()
Recipe ≠ Cake
Likewise:
Function ≠ Function Execution
Visual Comparison
Passing a Function
greet

↓

Another function receives it

↓

Can execute it later
Executing a Function
greet()

↓

Runs immediately

↓

Produces output
Functions Can Be Returned
Functions can even create and return other functions.
function createGreeting() {
    return function () {
        console.log("Hello");
    };
}
Let's understand this step by step.
When JavaScript sees:
return function () {
    console.log("Hello");
};
It returns a function, not the text "Hello".
Now:
let greeting = createGreeting();
Memory:
createGreeting()

↓

Returns Function

↓

Stored in greeting
Now:
greeting();
Output:
Hello
Again, notice:
The returned value was a function.
Why Does This Matter?
Callbacks only work because JavaScript allows functions to behave like values.
Imagine this:
Number

↓

Can be stored

↓

Can be passed
Now replace "Number" with "Function":
Function

↓

Can be stored

↓

Can be passed

↓

Can be returned

↓

Can be executed later
This is exactly what callback functions rely on.
Real-World Analogy
Imagine your manager says:
"When the delivery truck arrives, call me."
He isn't asking you to call him now.
He's giving you instructions to use later.
Those instructions are like passing a function.
Manager gives instruction

↓

You keep it

↓

Truck arrives

↓

You follow instruction

↓

Manager gets called
That's the essence of a callback.
Summary
By the end of this module, you should understand:
Functions are values in JavaScript.
A function can be stored in a variable.
A function can be passed to another function.
A function can be returned from another function.
greet means "the function itself."
greet() means "run the function now."
This ability to treat functions like values is what makes callback functions possible.
Check Your Understanding
Try answering these without running the code:
1. What will this print?
function greet() {
    console.log("Hello");
}

greet();
2. What does this line do?
let sayHello = greet;
3. Which one passes the function?
greet
or
greet()
4. Which one executes the function?
greet
or
greet()
5. Why are callback functions possible in JavaScript?
Practice Exercises
Easy
Create a function called welcome that prints "Welcome!" and call it.
Store the welcome function in another variable called showMessage and call it.
Create a function goodbye and assign it to another variable. Call the new variable.
Write two functions and explain which one is being passed and which one is being executed.
Without running the code, predict the output of three small examples involving greet and greet().
Medium
Create a function that returns another function, then call the returned function.
Write a function that accepts another function as a parameter but does not execute it.
Modify the previous example so it executes the received function.
Draw the memory diagram for a function stored in two variables.
Explain, in your own words, the difference between "passing a function" and "calling a function."
Once you're comfortable with this, we'll move to Module 3, where you'll write your first real callback function and see exactly how one function passes another function and decides when to execute it.
