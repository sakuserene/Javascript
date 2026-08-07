# Module 4 — Building a Callback Step by Step
This is one of the most important modules in the course.
Many tutorials jump straight to callback functions.
We won't.
Instead, we'll build a callback gradually, so you understand why each step is needed.
Step 1 — A Normal Function
Let's begin with a simple function.
function greet() {
    console.log("Hello!");
}

greet();
What happens?
Program Starts
      ↓
Create greet()
      ↓
Call greet()
      ↓
Print "Hello!"
      ↓
Program Ends
Output:
Hello!
Here, you decide when greet() runs.
Step 2 — Add Another Function
Now create another function.
function greet() {
    console.log("Hello!");
}

function run() {
    greet();
}

run();
Execution Flow
run()

↓

greet()

↓

Hello!
Output:
Hello!
Observation
run() always calls greet().
It cannot call anything else.
Step 3 — The Problem
Suppose tomorrow you want:
function wave() {
    console.log("👋");
}
Can run() execute wave()?
Not with the current code.
You would have to edit run() every time.
function run() {
    wave();
}
Tomorrow:
function run() {
    clap();
}
Next week:
function run() {
    jump();
}
This is not flexible.
Step 4 — Make run() Flexible
Instead of hardcoding the function, let someone else decide.
function greet() {
    console.log("Hello!");
}

function run(myFunction) {
    myFunction();
}

run(greet);
Now run() doesn't care which function it receives.
It simply executes it.
Flow
greet

↓

run(greet)

↓

myFunction = greet

↓

myFunction()

↓

Hello!
Step 5 — Pass a Different Function
Let's create another function.
function greet() {
    console.log("Hello!");
}

function wave() {
    console.log("👋");
}

function run(myFunction) {
    myFunction();
}

run(greet);

run(wave);
Output:
Hello!
👋
Flow
First call:
run(greet)

↓

myFunction = greet

↓

Hello!
Second call:
run(wave)

↓

myFunction = wave

↓

👋
Notice:
The same run() function works with different functions.
This is the power of callbacks.
Step 6 — Add More Functions
function greet() {
    console.log("Hello!");
}

function wave() {
    console.log("👋");
}

function clap() {
    console.log("👏");
}

function run(myFunction) {
    myFunction();
}

run(greet);
run(wave);
run(clap);
Output:
Hello!
👋
👏
run() never changed.
Only the callback changed.
Real-World Example — TV Remote
Imagine run() is a TV remote.
The remote doesn't know what you want.
You decide by pressing a button.
TV Remote

↓

Power Button

↓

TV Turns On
or
TV Remote

↓

Volume Button

↓

Volume Increases
or
TV Remote

↓

Channel Button

↓

Channel Changes
The remote is like:
run()
The button you press is the callback.
Real-World Example — Universal Charger
Think of a universal charger.
It works with:
Phone A
Phone B
Phone C
The charger doesn't change.
Only the device changes.
Similarly:
run(greet);

run(wave);

run(clap);
The run() function stays the same.
Only the callback changes.
Why Is This Useful?
Imagine writing:
run(greet);

run(downloadFile);

run(sendEmail);

run(saveData);

run(printReceipt);
One function can work with many different actions.
That's much more reusable than writing separate functions for every task.
Step 7 — Using Parameters with Callbacks
Callbacks can also receive data.
function greet(name) {
    console.log("Hello " + name);
}

function run(myFunction) {
    myFunction("Adi");
}

run(greet);
Output:
Hello Adi
Flow
run(greet)

↓

myFunction = greet

↓

myFunction("Adi")

↓

greet("Adi")

↓

Hello Adi
Key Takeaways
By the end of this module, you should understand:
A callback starts as a normal function.
Another function can receive that function as a parameter.
The receiving function decides when to execute it.
The same function (run) can execute many different callbacks.
Callbacks make code flexible, reusable, and easier to maintain.
Check Your Understanding
Before moving to Module 5, answer these:
Why is this version less flexible?
function run() {
    greet();
}
What makes this version more flexible?
function run(myFunction) {
    myFunction();
}
What will this print?
function smile() {
    console.log("😊");
}

function run(fn) {
    fn();
}

run(smile);
Does run() know in advance which function it will execute? Why or why not?
Practice Exercises
Easy
Create dance(), sing(), and jump() functions, then execute each using a single run() callback function.
Modify run() so it passes your name to the callback.
Create a callback that prints your favorite programming language.
Medium
Create a function repeat(callback) that calls the callback twice.
Create a function executeThreeTimes(callback) that calls the callback three times.
Write a callback that receives a number and prints its square, then pass it to another function.
In Module 5, we'll move beyond simple examples and explore 10 real-world scenarios (restaurant orders, online shopping, banking, hospitals, taxi booking, email sending, file uploads, downloads, printing, and more) to see how callback functions naturally model real systems.
