# Unit 1 - Lesson 4: JavaScript Strings and String Methods

**Level:** Beginner to intermediate  
**Previous:** [Lesson 3 - Type Conversion and Type Coercion](03-type-conversion-and-type-coercion.md)  
**Next:** [Lesson 5 - JavaScript Numbers and Math Methods](05-numbers-and-math-methods.md)

> GitHub-ready notes. Diagrams and punctuation use ASCII-compatible characters. All examples are independent and generic. Exercises include model solutions at the end; these are not assessments of submitted work.

## Table of Contents

1. [Lesson Overview and Learning Objectives](#lesson-overview-and-learning-objectives)
2. [What Is a String?](#what-is-a-string)
3. [Creating Strings](#creating-strings)
4. [Escape Sequences](#escape-sequences)
5. [String Length](#string-length)
6. [String Indexing](#string-indexing)
7. [The charAt() Method](#the-charat-method)
8. [The at() Method](#the-at-method)
9. [String Immutability](#string-immutability)
10. [Extracting with slice()](#extracting-with-slice)
11. [Extracting with substring()](#extracting-with-substring)
12. [The Legacy substr() Method](#the-legacy-substr-method)
13. [Searching Within Strings](#searching-within-strings)
14. [Changing Letter Case](#changing-letter-case)
15. [Removing Whitespace](#removing-whitespace)
16. [Replacing String Content](#replacing-string-content)
17. [Splitting Strings into Arrays](#splitting-strings-into-arrays)
18. [Joining Arrays into Strings](#joining-arrays-into-strings)
19. [Template Literals](#template-literals)
20. [Additional Useful String Methods](#additional-useful-string-methods)
21. [Unicode and JavaScript Strings](#unicode-and-javascript-strings)
22. [Method Chaining](#method-chaining)
23. [Practical String Processing Examples](#practical-string-processing-examples)
24. [Common Beginner Mistakes](#common-beginner-mistakes)
25. [Knowledge Assessment - 10 Questions](#knowledge-assessment---10-questions)
26. [Practical Coding Exercises - 10 Exercises](#practical-coding-exercises---10-exercises)
27. [Knowledge Assessment Answer Key](#knowledge-assessment-answer-key)
28. [Detailed Model Solutions](#detailed-model-solutions)
29. [Lesson Summary](#lesson-summary)
30. [Lesson Navigation](#lesson-navigation)

## Lesson Overview and Learning Objectives

In previous lessons we studied variables, types, operators, and conversion. This lesson explains how JavaScript represents text and provides methods to access, search, extract, transform, and combine strings.

```javascript
let message = "Learning JavaScript";
console.log(message.length);       // 19
console.log(message.toUpperCase()); // LEARNING JAVASCRIPT
console.log(message.slice(0, 8));  // Learning
```

**Objectives:** (1) explain strings and string literals; (2) use length and indexes; (3) compare brackets, `charAt()` and `at()`; (4) use `slice()`, `substring()`, and recognize legacy `substr()`; (5) search with `includes()`, `indexOf()`, `lastIndexOf()`, `startsWith()`, `endsWith()`; (6) transform with case, trim, and replacement methods; (7) split and join text; (8) interpolate with template literals; (9) understand immutability and Unicode limitations; (10) combine methods in practical programs.

## What Is a String?

A **string** is a primitive value representing textual data: letters, digits, whitespace, punctuation, and Unicode text.

```javascript
let name = "Alice";
let city = "London";
let message = "Welcome to JavaScript!";
let code = "12345";
console.log(typeof name); // string
```

Digits inside quotation marks are text, not numbers:

```javascript
console.log(100 + 50);     // 150
console.log("100" + "50"); // 10050
```

**Primitive types (ASCII tree):**

```text
JavaScript Primitive Types
|-- String
|-- Number
|-- Boolean
|-- Undefined
|-- Null
|-- BigInt
`-- Symbol
```

Although a string is primitive, JavaScript allows method and property access on it through temporary object-like handling (often called autoboxing).

```javascript
let language = "JavaScript";
console.log(language.length);        // 10
console.log(language.toUpperCase()); // JAVASCRIPT
```

## Creating Strings

JavaScript string literals use single quotes, double quotes, or backticks:

```javascript
let a = 'Alice';
let b = "Alice";
let c = `Alice`;
console.log(a, b, c); // Alice Alice Alice
```

Single and double quotes are equivalent for ordinary strings. Backticks enable interpolation and multiline text. Choose a consistent style.

To include quotation marks, alternate quote styles or escape the inner quotes:

```javascript
let first = 'Alice said, "Hello!"';
let second = "Alice said, \"Hello!\"";
console.log(first);  // Alice said, "Hello!"
console.log(second); // Alice said, "Hello!"
```

## Escape Sequences

Escape sequences begin with a backslash and represent special characters.

| Sequence | Meaning |
|---|---|
| `\n` | Newline |
| `\t` | Horizontal tab |
| `\\` | Literal backslash |
| `\"` | Double quote |
| `\'` | Single quote |
| `\r` | Carriage return |
| `\b` | Backspace control character |
| `\uXXXX` | Unicode escape with four hexadecimal digits |

```javascript
console.log("Hello\nWorld");
// Hello
// World

console.log("Name:\tAlice"); // Name:   Alice (spacing varies)
console.log("C:\\Users\\Alice"); // C:\Users\Alice
```

A single backslash is special in a string literal; use `\\` when you need a literal backslash.

## String Length

The `length` property reports the number of **UTF-16 code units**, not always the number of visible characters.

```javascript
console.log("JavaScript".length); // 10
console.log("Hello World".length); // 11 (includes the space)
console.log("".length); // 0
```

```text
String:     J a v a S c r i p t
Index:      0 1 2 3 4 5 6 7 8 9
Length:    10
```

The final index of a nonempty ASCII string is `length - 1`. Some Unicode symbols occupy multiple code units; see the Unicode section.

## String Indexing

Indexes start at **0**, not 1.

```text
Character: H E L L O
Index:     0 1 2 3 4
```

```javascript
let word = "HELLO";
console.log(word[0]); // H
console.log(word[1]); // E
console.log(word[4]); // O
console.log(word[10]); // undefined
```

Get the final code unit with:

```javascript
let word = "JavaScript";
console.log(word[word.length - 1]); // t
```

Why subtract one? Length is 10, while the indexes run from 0 to 9.

## The charAt() Method

`charAt(index)` returns the UTF-16 code unit at a position as a string.

```javascript
let word = "JavaScript";
console.log(word.charAt(0)); // J
console.log(word.charAt(4)); // S
console.log(word.charAt(9)); // t
```

For an invalid index, `charAt()` returns an empty string rather than `undefined`:

```javascript
let word = "Hello";
console.log(word[10]);       // undefined
console.log(word.charAt(10)); // ""
console.log(word.charAt(-1)); // ""
```

Unlike `at()`, `charAt()` does not support negative indexing from the end.

## The at() Method

`at(index)` supports both positive and negative positions.

```javascript
let word = "JavaScript";
console.log(word.at(0));  // J
console.log(word.at(4));  // S
console.log(word.at(-1)); // t
console.log(word.at(-2)); // p
```

```text
String:          J   a   v   a   S   c   r   i   p   t
Positive index:  0   1   2   3   4   5   6   7   8   9
Negative index: -10 -9  -8  -7  -6  -5  -4  -3  -2  -1
```

| Access | Negative indexes | Out of range |
|---|---|---|
| `str[index]` | No | `undefined` |
| `str.charAt(index)` | No | `""` |
| `str.at(index)` | Yes | `undefined` |

All three work with UTF-16 code units, not necessarily whole visible characters.

## String Immutability

Strings are **immutable**: you cannot change a string value in place.

```javascript
let word = "Hello";
word[0] = "Y";
console.log(word); // Hello in non-strict mode
```

In strict mode, assignment to a string index throws a `TypeError`. To make a changed string, build a new value and reassign the variable:

```javascript
let word = "Hello";
word = "Y" + word.slice(1);
console.log(word); // Yello
```

String methods return values rather than modifying the original string:

```javascript
let message = "hello";
let upper = message.toUpperCase();
console.log(message); // hello
console.log(upper);   // HELLO
```

## Extracting with slice()

`slice(startIndex, endIndex)` returns a substring. The start is **included**; the end is **excluded**.

```javascript
let language = "JavaScript";
console.log(language.slice(0, 4)); // Java
console.log(language.slice(4));    // Script
console.log(language.slice(-6));   // Script
console.log(language.slice(-6, -3)); // Scr
console.log(language.slice(7, 3)); // ""
```

```text
String:   J a v a S c r i p t
Index:    0 1 2 3 4 5 6 7 8 9
slice(0,4)
Included: ^ ^ ^ ^
Result:   Java
```

For a 10-unit string, index `-6` means position 4 and `-3` means position 7. If the resolved start is after the end, `slice()` returns an empty string. Omitting the end continues to the end.

## Extracting with substring()

`substring(startIndex, endIndex)` also excludes the end position, but it behaves differently with reversed and negative indexes.

```javascript
let word = "JavaScript";
console.log(word.substring(0, 4)); // Java
console.log(word.substring(7, 3)); // aScr
console.log(word.substring(-4));   // JavaScript
```

`substring(7,3)` swaps the positions and extracts indexes 3 through 6. Negative positions are treated as zero.

| Behavior | `slice()` | `substring()` |
|---|---|---|
| End excluded | Yes | Yes |
| Negative indexes | Count from end | Treated as zero |
| Reversed start/end | Empty string | Swaps indexes |
| Mutates original | No | No |

Prefer `slice()` for most new extraction code.

## The Legacy substr() Method

`substr(startIndex, length)` is a **legacy** method. The second argument is a character count, not an end index.

```javascript
let word = "JavaScript";
console.log(word.substr(4, 6)); // Script
console.log(word.slice(4, 10)); // Script (modern alternative)
```

Recognize `substr()` in older code, but use `slice()` in new programs.

## Searching Within Strings

Search methods locate or test substrings. They are case-sensitive by default.

```javascript
let message = "Learning JavaScript";
console.log(message.includes("JavaScript")); // true
console.log(message.includes("Python"));     // false

let text = "Hello World";
console.log(text.includes("hello")); // false
console.log(text.indexOf("World"));   // 6
console.log(text.indexOf("Python"));  // -1
```

Use `toLowerCase()` on both sides for a simple case-insensitive search:

```javascript
console.log("Hello World".toLowerCase().includes("hello")); // true
```

`lastIndexOf()` finds the final match:

```javascript
let word = "banana";
console.log(word.indexOf("a"));     // 1
console.log(word.lastIndexOf("a")); // 5
```

```text
String: b a n a n a
Index:  0 1 2 3 4 5
          ^       ^
        first    last
```

`startsWith()` and `endsWith()` check the beginning and end:

```javascript
let filename = "report.pdf";
console.log(filename.startsWith("report")); // true
console.log(filename.startsWith("image"));  // false
console.log(filename.endsWith(".pdf"));     // true
console.log(filename.endsWith(".jpg"));     // false
```

| Method | Return value | Purpose |
|---|---|---|
| `includes()` | Boolean | Is substring present? |
| `indexOf()` | Index or -1 | First occurrence |
| `lastIndexOf()` | Index or -1 | Last occurrence |
| `startsWith()` | Boolean | Beginning matches? |
| `endsWith()` | Boolean | Ending matches? |

## Changing Letter Case

`toUpperCase()` and `toLowerCase()` return strings with converted letter case.

```javascript
console.log("hello world".toUpperCase()); // HELLO WORLD
console.log("HELLO WORLD".toLowerCase()); // hello world
```

Case-sensitive comparison:

```javascript
let firstName = "ALICE";
let secondName = "alice";
console.log(firstName === secondName); // false
console.log(firstName.toLowerCase() === secondName.toLowerCase()); // true
```

These methods are useful for basic normalization. More complex international text comparisons may need locale-aware APIs.

## Removing Whitespace

Whitespace includes spaces, tabs, and line breaks. `trim()` removes whitespace from both ends, not from the middle.

```javascript
let name = "   Alice   ";
console.log(name.trim()); // Alice
let message = "   Hello   World   ";
console.log(message.trim()); // Hello   World
```

`trimStart()` removes only leading whitespace; `trimEnd()` removes only trailing whitespace.

```javascript
let name = "   Alice   ";
console.log(JSON.stringify(name.trimStart())); // "Alice   "
console.log(JSON.stringify(name.trimEnd()));   // "   Alice"
```

| Method | Leading | Trailing |
|---|---|---|
| `trim()` | Removes | Removes |
| `trimStart()` | Removes | Preserves |
| `trimEnd()` | Preserves | Removes |

## Replacing String Content

With a string search value, `replace()` changes the first occurrence; `replaceAll()` changes every occurrence.

```javascript
let message = "I like Java";
console.log(message.replace("Java", "JavaScript"));
// I like JavaScript

let text = "cat cat cat";
console.log(text.replace("cat", "dog"));    // dog cat cat
console.log(text.replaceAll("cat", "dog")); // dog dog dog
```

Both methods return new strings:

```javascript
let message = "Hello World";
let result = message.replace("World", "Alice");
console.log(message); // Hello World
console.log(result);  // Hello Alice
```

`replace()` also accepts regular expressions; global replacement can be achieved using an appropriate regular expression. `replaceAll()` with a regex requires the global flag.

## Splitting Strings into Arrays

`split(separator)` converts a string into an array of substrings.

```javascript
let sentence = "I love JavaScript";
let words = sentence.split(" ");
console.log(words); // ["I", "love", "JavaScript"]
```

```text
"I love JavaScript"
        |
        | split(" ")
        v
["I", "love", "JavaScript"]
```

More examples:

```javascript
console.log("apple,banana,orange".split(","));
// ["apple", "banana", "orange"]
console.log("Hello".split(""));
// ["H", "e", "l", "l", "o"]
console.log("apple,banana,orange,mango".split(",", 2));
// ["apple", "banana"]
```

`split("")` divides UTF-16 code units and may split some Unicode symbols incorrectly. A second argument limits the number of returned elements.

## Joining Arrays into Strings

`join(separator)` is an **array method** that combines elements into one string.

```javascript
let words = ["Hello", "World"];
console.log(words.join(" ")); // Hello World

let fruits = ["apple", "banana", "orange"];
console.log(fruits.join(", ")); // apple, banana, orange
```

Combine `split()` and `join()` to transform separators:

```javascript
let sentence = "I love JavaScript";
let words = sentence.split(" ");
console.log(words.join("-")); // I-love-JavaScript
```

```text
"I love JavaScript"
        |
        | split(" ")
        v
["I", "love", "JavaScript"]
        |
        | join("-")
        v
"I-love-JavaScript"
```

## Template Literals

Template literals use backticks and support `${expression}` interpolation and multiline text.

```javascript
let name = "Alice";
let age = 25;
let message = `My name is ${name} and I am ${age} years old.`;
console.log(message);
// My name is Alice and I am 25 years old.
```

Expressions can be evaluated inside interpolation:

```javascript
let a = 10;
let b = 5;
console.log(`The sum is ${a + b}`); // The sum is 15
```

Multiline template literal:

```javascript
let message = `Hello,
Welcome to JavaScript.
Enjoy learning!`;
console.log(message);
```

Output:

```text
Hello,
Welcome to JavaScript.
Enjoy learning!
```

Escape interpolation when you want literal `${name}`:

```javascript
let message = `Use \${name} for interpolation`;
console.log(message); // Use ${name} for interpolation
```

## Additional Useful String Methods

`repeat(count)` repeats text:

```javascript
console.log("*".repeat(5)); // *****
```

`padStart()` pads at the beginning; `padEnd()` pads at the end:

```javascript
console.log("7".padStart(3, "0")); // 007
console.log("7".padEnd(3, "0"));   // 700
```

`concat()` combines strings, though template literals are often clearer:

```javascript
let firstName = "Alice";
let lastName = "Johnson";
console.log(firstName.concat(" ", lastName)); // Alice Johnson
```

`match()` searches using a regular expression:

```javascript
let message = "Order 123";
let match = message.match(/\d+/);
console.log(match[0]); // 123
```

The full result of `match()` is an array with additional match information. Regular expressions will be covered separately.

## Unicode and JavaScript Strings

JavaScript strings are UTF-16 code-unit sequences. Some visible symbols take two code units, and some displayed grapheme clusters contain multiple code points.

```javascript
let symbol = "\u{1F600}";
console.log(symbol.length);         // 2
console.log(symbol.codePointAt(0)); // 128512
console.log(Array.from(symbol).length); // 1
```

`Array.from()` iterates by Unicode code point, not raw UTF-16 code unit. A user-perceived character can still comprise several code points (for example, some combined emoji). For advanced segmentation, consider `Intl.Segmenter` in supported environments.

**Key rule:** Do not assume `length`, `charAt()`, `at()`, or `slice()` always correspond to visible characters.

## Method Chaining

Method chaining calls a method on the value returned by a previous method.

```javascript
let name = "   ALICE   ";
let result = name.trim().toLowerCase();
console.log(result); // alice
```

```text
"   ALICE   "
      |
      | trim()
      v
"ALICE"
      |
      | toLowerCase()
      v
"alice"
```

Another example:

```javascript
let sentence = "   I LOVE JAVASCRIPT   ";
let result = sentence
  .trim()
  .toLowerCase()
  .replace("javascript", "programming");
console.log(result); // i love programming
```

Execution: trim ends, convert case, replace substring, assign returned value. The original string is not mutated.

## Practical String Processing Examples

### Example 1 - Extract a file extension

```javascript
let filename = "document.pdf";
let extension = filename.slice(filename.lastIndexOf(".") + 1);
console.log(extension); // pdf
```

`lastIndexOf(".")` finds the final period, `+ 1` moves past it, and `slice()` takes the remainder. This assumes an extension exists.

### Example 2 - Normalize a username

```javascript
let username = "   Alice_123   ";
let normalizedUsername = username.trim().toLowerCase();
console.log(normalizedUsername); // alice_123
```

### Example 3 - Extract an email domain (simple illustration)

```javascript
let email = "alice@example.com";
let domain = email.split("@")[1];
console.log(domain); // example.com
```

This is not full email validation.

### Example 4 - Count words

```javascript
let sentence = "I love learning JavaScript";
let words = sentence.trim().split(/\s+/);
console.log(words.length); // 4
```

A blank input needs an additional check because splitting an empty string this way returns an array with one empty element.

### Example 5 - Generate initials

```javascript
let fullName = "Alice Johnson";
let names = fullName.split(" ");
let initials = names[0].charAt(0) + names[1].charAt(0);
console.log(initials); // AJ
```

This demonstration assumes exactly two nonempty name parts.

## Common Beginner Mistakes

1. **Indexing starts at zero.** `"Hello"[1]` is `"e"`, not `"H"`.
2. **The end of `slice()` is excluded.** `"JavaScript".slice(0,4)` is `"Java"`.
3. **Strings are immutable.** Assigning `word[0] = "Y"` does not modify the string; strict mode throws.
4. **Search is case-sensitive.** `"Hello".includes("hello")` is `false`.
5. **`split()` and `join()` are different.** `split()` is a string method returning an array; `join()` is an array method returning a string.
6. **Store the returned transformation.**

```javascript
let message = "hello";
message.toUpperCase();
console.log(message); // hello

message = message.toUpperCase();
console.log(message); // HELLO
```

## Knowledge Assessment - 10 Questions

Answer without running the code first. The answer key and reasoning appear after the coding exercises.

### Question 1 - What is the output?

```javascript
let word = "JavaScript";
console.log(word.length);
```

- A. `9`
- B. `10`
- C. `11`
- D. `undefined`

### Question 2 - What is printed?

```javascript
let word = "Hello";
console.log(word[1]);
```

- A. `H`
- B. `e`
- C. `l`
- D. `undefined`

### Question 3 - What is the result?

```javascript
console.log("JavaScript".slice(0, 4));
```

- A. `JavaS`
- B. `Java`
- C. `Script`
- D. `avaS`

### Question 4 - What is printed?

```javascript
console.log("JavaScript".slice(-6));
```

- A. `Java`
- B. `Script`
- C. `ipt`
- D. `JavaScript`

### Question 5 - What is the output?

```javascript
console.log("banana".lastIndexOf("a"));
```

- A. `1`
- B. `3`
- C. `5`
- D. `-1`

### Question 6 - What is returned?

```javascript
console.log("Hello".includes("hello"));
```

- A. `true`
- B. `false`
- C. `-1`
- D. `undefined`

### Question 7 - What is printed?

```javascript
let text = "  Alice  ";
console.log(text.trim());
```

- A. `"  Alice  "`
- B. `Alice`
- C. `ALICE`
- D. `undefined`

### Question 8 - What is the result?

```javascript
console.log("cat cat".replace("cat", "dog"));
```

- A. `dog dog`
- B. `dog cat`
- C. `cat dog`
- D. `cat cat`

### Question 9 - What is printed?

```javascript
console.log("a,b,c".split(","));
```

- A. `"abc"`
- B. `["a", "b", "c"]`
- C. `"a b c"`
- D. `3`

### Question 10 - What is the output?

```javascript
let text = "hello";
text.toUpperCase();
console.log(text);
```

- A. `HELLO`
- B. `hello`
- C. `undefined`
- D. `Error`

## Practical Coding Exercises - 10 Exercises

### Exercise 1 - String indexing

Given `let language = "JavaScript";`, print the first, fifth, and last characters. Use bracket notation, `charAt()`, and `at()` at least once each.

### Exercise 2 - String extraction

Given `let message = "Learning JavaScript";`, use `slice()` to print `"Learning"`, `"JavaScript"`, and `"Script"`.

### Exercise 3 - String searching

Given `let sentence = "JavaScript is a powerful language";`, check whether it contains `"powerful"`, find the starting index of `"language"`, test whether it starts with `"JavaScript"`, and whether it ends with `"language"`.

### Exercise 4 - String transformation

Given `let username = "   ALICE_123   ";`, remove leading/trailing whitespace and convert to lowercase. Expected: `alice_123`.

### Exercise 5 - Replacing text

Given `let sentence = "Java is good. Java is popular.";`, replace every `"Java"` with `"JavaScript"`. Expected: `JavaScript is good. JavaScript is popular.`

### Exercise 6 - Split and join

Given `let fruits = "apple,banana,orange,mango";`, split into an array and join with `" | "`. Expected: `apple | banana | orange | mango`.

### Exercise 7 - Template literals

Given `name = "Alice"`, `age = 25`, and `course = "JavaScript"`, use a template literal to print `My name is Alice, I am 25 years old, and I am learning JavaScript.`

### Exercise 8 - Predict the output

Without executing, predict each output and explain: 

```javascript
let word = "Programming";
console.log(word.length);
console.log(word.slice(0, 7));
console.log(word.slice(-4));
console.log(word.charAt(3));
console.log(word.at(-1));
```

### Exercise 9 - Debugging

Explain why this code prints lowercase text, then fix it:

```javascript
let message = "hello world";
message.toUpperCase();
console.log(message);
```

Expected after correction: `HELLO WORLD`.

### Exercise 10 - Mini project - Username generator

Given `let fullName = "   Alice Johnson   ";`, trim, lowercase, split into words, join with underscores, append `"_2026"`, and print `alice_johnson_2026`. **Challenge:** Handle multiple consecutive spaces between names.

## Knowledge Assessment Answer Key

**Question 1: B - `10`.** JavaScript has ten UTF-16 code units.

**Question 2: B - `e`.** Index 1 is the second character.

**Question 3: B - `Java`.** slice includes index 0 but excludes index 4.

**Question 4: B - `Script`.** Negative six begins six units before the end.

**Question 5: C - `5`.** The last a is at index 5.

**Question 6: B - `false`.** includes is case-sensitive.

**Question 7: B - `Alice`.** trim removes whitespace from both ends.

**Question 8: B - `dog cat`.** replace with a string search value replaces only the first occurrence.

**Question 9: B - `["a", "b", "c"]`.** split returns an array of three strings.

**Question 10: B - `hello`.** The returned uppercase value was not stored; the original string remains unchanged.

## Detailed Model Solutions

### Model Solution 1 - String indexing

```javascript
let language = "JavaScript";
console.log(language[0]);     // J
console.log(language.charAt(4)); // S
console.log(language.at(-1)); // t
```

**Why:** Index zero is first; index four is fifth; negative one counts backward from the end.

### Model Solution 2 - String extraction

```javascript
let message = "Learning JavaScript";
console.log(message.slice(0, 8)); // Learning
console.log(message.slice(9));    // JavaScript
console.log(message.slice(13));   // Script
```

**Why:** `Learning` occupies indexes 0-7; the space is index 8; `JavaScript` starts at 9; `Script` starts at 13. The end index is excluded.

### Model Solution 3 - String searching

```javascript
let sentence = "JavaScript is a powerful language";
console.log(sentence.includes("powerful"));       // true
console.log(sentence.indexOf("language"));       // 25
console.log(sentence.startsWith("JavaScript")); // true
console.log(sentence.endsWith("language"));     // true
```

**Why:** `includes` checks presence, `indexOf` returns the first starting index, and the other methods check the boundaries. `language` starts at index 25.

### Model Solution 4 - String transformation

```javascript
let username = "   ALICE_123   ";
let normalized = username.trim().toLowerCase();
console.log(normalized); // alice_123
```

**Why:** `trim()` removes surrounding whitespace, then `toLowerCase()` transforms the returned string. The original remains unchanged unless reassigned.

### Model Solution 5 - Replacing text

```javascript
let sentence = "Java is good. Java is popular.";
let result = sentence.replaceAll("Java", "JavaScript");
console.log(result);
// JavaScript is good. JavaScript is popular.
```

**Why:** `replaceAll()` replaces both occurrences. Plain `replace()` would only replace the first.

### Model Solution 6 - Split and join

```javascript
let fruits = "apple,banana,orange,mango";
let parts = fruits.split(",");
let result = parts.join(" | ");
console.log(result); // apple | banana | orange | mango
```

**Why:** `split()` creates an array, while `join()` reconstructs a string using the new separator.

### Model Solution 7 - Template literals

```javascript
let name = "Alice";
let age = 25;
let course = "JavaScript";
console.log(`My name is ${name}, I am ${age} years old, and I am learning ${course}.`);
// My name is Alice, I am 25 years old, and I am learning JavaScript.
```

**Why:** Each `${...}` expression is evaluated and interpolated into the template literal.

### Model Solution 8 - Predict the output

```text
11
Program
ming
g
g
```

**Why:** `Programming` has 11 letters. `slice(0,7)` includes indexes 0 through 6 (`Program`). `slice(-4)` returns `ming`. `charAt(3)` reads the fourth letter (`g`). `at(-1)` reads the final letter (`g`).

### Model Solution 9 - Debugging

```javascript
let message = "hello world";
message = message.toUpperCase();
console.log(message); // HELLO WORLD
```

**Why:** `toUpperCase()` returns a new string; calling it without assigning its result leaves the original value unchanged.

### Model Solution 10 - Mini project - Username generator

```javascript
let fullName = "   Alice Johnson   ";
let username = fullName.trim().toLowerCase().split(" ").join("_") + "_2026";
console.log(username); // alice_johnson_2026
```

**Step-by-step:** Trim -> `Alice Johnson`; lowercase -> `alice johnson`; split -> `["alice", "johnson"]`; join -> `alice_johnson`; append suffix -> `alice_johnson_2026`.

**Challenge solution:**

```javascript
let fullName = "   Alice    Johnson   ";
let username = fullName.trim().toLowerCase().split(/\s+/).join("_") + "_2026";
console.log(username); // alice_johnson_2026
```

`/\s+/` matches one or more whitespace characters, preventing extra underscores when there are repeated spaces. For completely empty input, validate before generating a username.

## Lesson Summary

| Concept | Key takeaway |
|---|---|
| String | Primitive textual value |
| Literals | Single quotes, double quotes, or backticks |
| `length` | Number of UTF-16 code units |
| Indexing | Starts at zero |
| `charAt()` | Character at index; empty string if out of range |
| `at()` | Supports negative indexes |
| Immutability | Methods return new strings |
| `slice()` | Extracts with excluded end; supports negative indexes |
| `substring()` | Swaps reversed indexes; negative treated as zero |
| `substr()` | Legacy method; avoid in new code |
| `includes()` | Tests whether substring exists |
| `indexOf()` / `lastIndexOf()` | First/last matching index |
| `startsWith()` / `endsWith()` | Test string boundaries |
| `toUpperCase()` / `toLowerCase()` | Change case in returned string |
| `trim()` | Remove surrounding whitespace |
| `replace()` / `replaceAll()` | Replace first/all string matches |
| `split()` | String -> array |
| `join()` | Array -> string |
| Template literals | Interpolate expressions and span lines |
| Chaining | Apply methods to previous results |
| Unicode | Code units may differ from visible characters |

**Remember:** Indexes start at zero, `slice()` excludes its end, searches are case-sensitive, strings cannot be changed in place, and Unicode text sometimes needs more advanced handling.

## Lesson Navigation

**Previous:** [Unit 1 - Lesson 3: Type Conversion and Type Coercion](03-type-conversion-and-type-coercion.md)

**Current:** Unit 1 - Lesson 4: JavaScript Strings and String Methods

**Next:** [Unit 1 - Lesson 5: JavaScript Numbers and Math Methods](05-numbers-and-math-methods.md)

**Repository path:** `javascript-course/unit-01-fundamentals/04-strings-and-string-methods.md`

When ready, submit your exercises for feedback or continue to Lesson 5.
