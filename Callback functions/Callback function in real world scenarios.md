# Module 5 — Callback Functions in Real-World Scenarios
Up to this point, you've learned how callback functions work.
Now we'll answer an important question:
Where are callback functions actually used in real life?
You'll discover that callbacks are everywhere. They let a system say:
"When this task is finished, I'll let you know."
We'll go through 10 scenarios. For each one we'll answer:
Who starts the work?
Who finishes the work?
Who gets notified?
Where is the callback?
Example 1 — Restaurant Order
Imagine you order pizza.
Real World Flow
You order pizza

↓

Chef starts cooking

↓

You sit with your friends

↓

Pizza is ready

↓

Waiter calls your name

↓

You collect your pizza
Questions
Who starts the work?
You, by placing the order.
Who finishes the work?
The chef.
Who gets notified?
You.
Where is the callback?
The waiter calling your name.
JavaScript Version
function notifyCustomer() {
    console.log("Your pizza is ready!");
}

function preparePizza(callback) {
    console.log("Cooking pizza...");
    callback();
}

preparePizza(notifyCustomer);
Flow
preparePizza()

↓

Cooking pizza...

↓

callback()

↓

notifyCustomer()

↓

Your pizza is ready!
Notice:
preparePizza() decides when to call notifyCustomer().
notifyCustomer is the callback.
Example 2 — Online Shopping
You order shoes online.
Flow
Place Order

↓

Warehouse packs item

↓

Courier delivers

↓

You receive SMS

↓

Collect package
Who starts the work?
You.
Who finishes?
The courier.
Who gets notified?
You.
Callback?
The SMS notification.
JavaScript
function sendSMS() {
    console.log("Your package has arrived.");
}

function deliverPackage(callback) {
    console.log("Delivering package...");
    callback();
}

deliverPackage(sendSMS);
Example 3 — Bank Transfer
Imagine sending money.
Transfer Money

↓

Bank processes payment

↓

Payment completes

↓

Bank sends confirmation

↓

You receive message
The confirmation message is the callback.
function sendConfirmation() {
    console.log("Transfer successful.");
}

function processTransfer(callback) {
    console.log("Processing transfer...");
    callback();
}

processTransfer(sendConfirmation);
Example 4 — Hospital Queue
Register

↓

Doctor treats patients

↓

Doctor becomes available

↓

Nurse calls your name

↓

Consultation starts
Callback:
The nurse calling your name.
function callPatient() {
    console.log("Next patient please.");
}

function waitForDoctor(callback) {
    console.log("Waiting...");
    callback();
}

waitForDoctor(callPatient);
Example 5 — Taxi Booking
Book Taxi

↓

Driver accepts trip

↓

Driver travels

↓

Driver arrives

↓

Driver calls you

↓

Journey starts
Callback:
The driver's phone call.
function notifyPassenger() {
    console.log("Your driver has arrived.");
}

function findDriver(callback) {
    console.log("Finding driver...");
    callback();
}

findDriver(notifyPassenger);
Example 6 — School Exam Results
Take Exam

↓

Teacher marks papers

↓

Results completed

↓

Teacher announces results

↓

Students receive grades
Callback:
Teacher announcing the results.
function announceResults() {
    console.log("Results are ready.");
}

function markExams(callback) {
    console.log("Marking exams...");
    callback();
}

markExams(announceResults);
Example 7 — Sending an Email
Write Email

↓

Click Send

↓

Server sends email

↓

Delivery successful

↓

Show success message
Callback:
The success message.
function emailSent() {
    console.log("Email sent successfully.");
}

function sendEmail(callback) {
    console.log("Sending email...");
    callback();
}

sendEmail(emailSent);
Example 8 — Uploading a File
Choose File

↓

Upload begins

↓

File uploads

↓

Show success message
Callback:
The success message after the upload finishes.
function uploadComplete() {
    console.log("Upload complete.");
}

function uploadFile(callback) {
    console.log("Uploading...");
    callback();
}

uploadFile(uploadComplete);
Example 9 — Downloading a File
Click Download

↓

Download starts

↓

Download finishes

↓

Open file
Callback:
Opening the downloaded file.
function openFile() {
    console.log("Opening file...");
}

function download(callback) {
    console.log("Downloading...");
    callback();
}

download(openFile);
Example 10 — Printing a Document
Click Print

↓

Printer prints pages

↓

Printing finishes

↓

Printer displays "Done"
Callback:
The completion notification.
function printingFinished() {
    console.log("Printing completed.");
}

function printDocument(callback) {
    console.log("Printing...");
    callback();
}

printDocument(printingFinished);
What Do All These Examples Have in Common?
Every example follows the same pattern:
Start a task

↓

Task takes some time

↓

Task finishes

↓

Callback function runs

↓

User is notified or next action happens
The only thing that changes is what the callback does.
Callback Pattern in JavaScript
You'll see this pattern repeatedly:
function someTask(callback) {
    console.log("Doing some work...");

    callback();
}
Then you pass different functions:
function taskOne() {
    console.log("Task One");
}

function taskTwo() {
    console.log("Task Two");
}

someTask(taskOne);

someTask(taskTwo);
Output:
Doing some work...
Task One

Doing some work...
Task Two
someTask() never changes.
Only the callback changes.
Why Is This Powerful?
Imagine writing one function that can:
Send an email
Save data
Print a receipt
Update the screen
Show a notification
You don't need five different versions of the same code.
Instead, you reuse one function and change its behavior by passing a different callback.
This makes your code:
More reusable
Easier to maintain
More flexible
Real JavaScript Examples You'll Soon Recognize
These all use callbacks:
button.addEventListener("click", handleClick);
setTimeout(showMessage, 3000);
numbers.forEach(printNumber);
numbers.map(double);
numbers.filter(isEven);
In every case:
A function is passed.
Another function decides when to call it.
Summary
By the end of this module, you should understand that callbacks are not just a JavaScript concept—they model a common pattern in real life:
Start a task.
Let the task run.
When it's finished, execute another function.
That's exactly how browsers, user interfaces, timers, and many JavaScript APIs work.
Check Your Understanding
Answer these before moving to Module 6:
In the taxi example, what is the callback?
In the hospital example, who decides when the callback runs?
In the email example, why shouldn't the success message appear before the email is sent?
What do all 10 examples have in common?
Why is using a callback more flexible than hardcoding a specific function?
Practice Exercises
Easy
Create a cookFood(callback) function and pass a callback that prints "Dinner is ready!".
Create a washClothes(callback) function and pass a callback that prints "Laundry finished!".
Create a bookHotel(callback) function and pass a callback that prints "Booking confirmed!".
Create a chargePhone(callback) function and pass a callback that prints "Battery is full!".
Draw the flow for any one of the examples using arrows.
Medium
Create a processOrder(callback) function and write three different callbacks: sendReceipt, updateInventory, and notifyCustomer. Call processOrder() with each callback separately.
Create a completeAssignment(callback) function and pass different callbacks such as celebrate, submitWork, and takeBreak.
Explain why the same function (processOrder) can work with different callbacks without being modified.
In Module 6, we'll connect callbacks with loops and array methods, showing why methods like forEach() require callback functions and how JavaScript automatically calls your callback for each item in an array.
