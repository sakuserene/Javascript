Excellent. We'll follow this as our course structure and move one module at a time. I won't skip steps, and I won't assume you already understand callbacks.
# Module 1 — What is a Callback?
Before writing any JavaScript, let's answer one question:
Why did callback functions even need to exist?
Understanding this makes the rest of the course much easier.
Imagine JavaScript Without Callbacks
Suppose you order food at a restaurant.
Without callbacks, you might do this:
```Walk into restaurant

↓

Order food

↓

Stand beside the chef

↓

Watch them cook

↓

Wait...

↓

Wait...

↓

Wait...

↓

Food is ready

↓

Eat
While waiting...
```
You cannot:
Talk to friends
Read your phone
Drink water
Do anything else
Everything stops until the food is ready.
This is called blocking.
Now imagine the real world.
Order food

↓

Chef starts cooking

↓

You go chat with friends

↓

You drink soda

↓

You watch football

↓

Food becomes ready

↓

Waiter calls your name

↓

You come back

↓

Eat
Notice something important.
You didn't keep checking if the food was ready.
Instead...
Someone informed you.
That notification is the callback.
What Exactly Is a Callback?
A callback is simply:
A function that is given to another function so it can be called later when something happens.
Read that again.
Not now.
Later.
When something happens.
Think of it like this:
You

↓

Give your phone number

↓

Restaurant keeps it

↓

Food becomes ready

↓

Restaurant calls you

↓

You return
Your phone number represents the callback function.
The restaurant decides when to use it.
Laundry Example
Imagine washing clothes.
Put clothes in machine

↓

Press Start

↓

Machine begins washing

↓

You leave

↓

Watch TV

↓

Cook food

↓

Read a book

↓

Machine finishes

↓

BEEP!
Question:
Did you stand beside the machine for 30 minutes?
No.
The washing machine notified you.
The beep is like a callback.
The callback says:
"I'm finished.

You can continue."
Phone Call Example
Your friend says:
"I'll call you when I arrive."
Do you do this?
Did you arrive?

Did you arrive?

Did you arrive?

Did you arrive?

Did you arrive?
No.
Instead...
Friend travels

↓

You continue your work

↓

Friend arrives

↓

Friend calls you

↓

You answer
The phone call is the callback.
Your friend decides when to call.
Taxi Example
Imagine booking a taxi.
Request Taxi

↓

Driver accepts

↓

Driver is coming

↓

You continue preparing

↓

Wear shoes

↓

Lock house

↓

Taxi arrives

↓

Driver calls

↓

You leave
The driver's phone call is the callback.
Hospital Example
You visit a hospital.
Register

↓

Sit down

↓

Read a magazine

↓

Doctor finishes another patient

↓

Nurse calls your name

↓

You enter
The nurse calling your name is the callback.
You didn't keep opening the doctor's door asking,
"Can I come now?"
School Example
Your teacher says:
I'll mark your exams.

I'll call everyone tomorrow.
Next day...
Teacher finishes marking

↓

Calls your name

↓

Gives results
That announcement is the callback.
What Problem Do Callbacks Solve?
Imagine if callbacks didn't exist.
You'd have to constantly ask:
Finished?

Finished?

Finished?

Finished?

Finished?
This is called polling (constantly checking).
Callbacks solve this problem.
Instead of checking repeatedly,
the system tells you when it's ready.
Real Life Summary
Callbacks are everywhere.
Restaurant
        ↓
Waiter calls you

Laundry
        ↓
Machine beeps

Hospital
        ↓
Nurse calls you

Taxi
        ↓
Driver calls you

School
        ↓
Teacher announces results

Phone
        ↓
Friend calls

Email
        ↓
Notification arrives
Every one of these follows the same pattern:
Start work

↓

Continue doing other things

↓

Work finishes

↓

Someone notifies you

↓

You respond
That notification is the callback.
How Does This Relate to JavaScript?
Imagine JavaScript needs to:
Download a file
Wait for a button click
Wait 5 seconds
Upload a photo
Read a file
Wait for a user to type
Should JavaScript stop everything and wait?
No.
Instead it says:
"When you're finished...

call this function."
That function is called a callback function.
Visual Summary
Start Task
     │
     ▼
Task is running...
     │
     ▼
JavaScript keeps doing other work
     │
     ▼
Task finishes
     │
     ▼
Callback Function Runs
Or another way:
You give someone instructions

↓

They keep your instructions

↓

Something happens later

↓

They follow your instructions
Those "instructions" are your callback function.
Key Takeaways
By the end of this module, you should understand:
A callback is a function passed to another function to be executed later.
Callbacks exist so JavaScript doesn't have to stop and wait.
They allow JavaScript to continue doing other work while waiting for events or tasks to finish.
The callback runs only when the required event or task has completed.
Check Your Understanding
Answer these questions in your own words before we move to Module 2:
Why do callback functions exist?
In the restaurant example, what represents the callback?
In the laundry example, what represents the callback?
What problem would occur if callbacks didn't exist?
Why is repeatedly asking "Is it finished?" less efficient than using a callback?
In your own words, what is a callback function?
Once you've answered these, we'll move to Module 2: Functions Are Values, where you'll discover the crucial idea that makes callbacks possible: in JavaScript, functions can be treated like data.
