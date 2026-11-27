# Lesson 18 - Working With Strings

*JavaScript Plain Loops*

Previous: [Working With Arrays](17-working-with-arrays.md) | Next: Frequency Counting and Grouping (planned)

In the previous lesson, we developed techniques for processing ordered collections of values. Strings share indexed access and length with arrays, but differ in representation and mutation. This chapter uses traditional loops rather than iterator loops or regular expressions.

## Table of contents

1. [String basics for loops](#1-string-basics-for-loops)
2. [Iterating over strings](#2-iterating-over-strings)
3. [Unicode and UTF-16](#3-unicode-and-utf-16)
4. [Classifying characters](#4-classifying-characters)
5. [Building and transforming strings](#5-building-and-transforming-strings)
6. [Searching strings](#6-searching-strings)
7. [Comparing strings](#7-comparing-strings)
8. [Splitting and processing text](#8-splitting-and-processing-text)
9. [Summary](#9-summary)
10. [Integrated exercises](#10-integrated-exercises)

## 1. String basics for loops

A string is a primitive value representing text. Quotes delimit a string literal; they are not part of its contents. An empty string contains no code units. For now, use ASCII text, where each ordinary character occupies one position. Section 3 explains why this assumption does not hold for all text.

```javascript
const text = "hello";
console.log(text.length); // 5
console.log(text[0]); // h
console.log(text[text.length - 1]); // o
console.log(text[5]); // undefined
```

Indexes start at zero. The last valid index is length minus one, provided the string is not empty. An indexed value is itself a string, not a separate character type.

### Strings cannot be modified in place

An array assignment can replace an element. A string assignment cannot replace an indexed character. In non-strict code such an assignment is ignored; in strict code it throws a TypeError. Strict mode is a JavaScript execution mode that turns certain otherwise silent mistakes into errors.

Instead of changing the existing string, construct another string and assign it to a variable declared with let.

```javascript
let text = "cat";
let result = "";
for (let i = 0; i < text.length; i++) {
  if (i === 0) {
    result += "b";
  } else {
    result += text[i];
  }
}
text = result;
console.log(text); // bat
```

The original string value was never modified. The variable now holds a different value. The result starts empty because concatenating an empty string adds no text. The += operator appends text when the accumulator is a string.

### Escape sequences

A backslash introduces an escape sequence in a string literal. The source spelling is not necessarily the stored text. For example, \n represents a newline, \t a tab, and \\ a literal backslash. These matter when processing whitespace or counting positions.

```javascript
const text = "a\nb";
console.log(text.length); // 3
console.log(text[1] === "\n"); // true
```

### Practice

- Predict the length and last indexed value of "", "a", and "a\tb". Expected lengths: 0, 1, 3; last values: undefined, "a", "b".
- Construct "dog" from "dig" by replacing only index 1 using a loop. Explain why direct indexed assignment is unsuitable.

## 2. Iterating over strings

### Choosing the traversal order

To display every position, generate indexes from zero through length minus one. To display the same text backward, start at the last index and decrease the index. Neither traversal modifies the string.

```javascript
const text = "CODE";
for (let i = 0; i < text.length; i++) {
  console.log(i, text[i]);
}
// 0 C, 1 O, 2 D, 3 E (one pair per line)
```

```javascript
const text = "CODE";
let result = "";
for (let i = text.length - 1; i >= 0; i--) {
  result += text[i];
}
console.log(result); // EDOC
```

An empty string starts backward traversal at -1, so no iteration occurs. A single-character string is visited once. These boundaries require no special cases.

### Neighboring characters and runs

Suppose a machine records consecutive states as "AAABBCA". Count changes of state, not the number of states recorded. A change exists only when a position has a predecessor and differs from it. Start at index one.

```javascript
const text = "AAABBCA";
let changes = 0;
for (let i = 1; i < text.length; i++) {
  if (text[i] !== text[i - 1]) changes++;
}
console.log(changes); // 3
```

A run is a maximal consecutive group of equal characters. The example contains four runs: AAA, BB, C, A. A nonempty string has one more run than changes; an empty string has zero runs.

To find the longest run, keep the current run length and the largest length observed. Reset the current length to one when the character changes, not zero: the new character already belongs to a run.

```javascript
const text = "AABBBBCC";
let current = 0;
let best = 0;
for (let i = 0; i < text.length; i++) {
  if (i > 0 && text[i] === text[i - 1]) {
    current++;
  } else {
    current = 1;
  }
  if (current > best) best = current;
}
console.log(best); // 4
```

| Index | Character | Current | Best |
|---:|---|---:|---:|
| 0 | A | 1 | 1 |
| 1 | A | 2 | 2 |
| 2 | B | 1 | 2 |
| 3 | B | 2 | 2 |
| 4 | B | 3 | 3 |
| 5 | B | 4 | 4 |
| 6 | C | 1 | 4 |
| 7 | C | 2 | 4 |

The best value is updated during every iteration, so a longest run at the end is not forgotten.

### Practice

- Collect alternate positions of "ABCDEFG". Expected: "ACEG".
- Trace the longest-run algorithm for "ABCCC". Expected: 3. Also test "" and "X" (0 and 1).
- Modify the algorithm to record the starting index of the first longest run. For "AABBCCC" expect 4.

## 3. Unicode and UTF-16

The word character can mean several things. Unicode assigns numerical identifiers called code points to text symbols. JavaScript stores strings as sequences of 16-bit code units (UTF-16). Many code points occupy one unit, while supplementary code points occupy two. A user-perceived character, called a grapheme cluster, may contain multiple code points.

To keep this file ASCII-only, examples use Unicode escapes. The literal "\u0041" represents A. A four-digit hexadecimal escape specifies a code unit. A brace escape such as "\u{1F600}" specifies a code point; hexadecimal digits A through F represent ten through fifteen.

```javascript
const text = "A\u{1F600}B";
console.log(text.length); // 4
console.log(text[0]); // A
console.log(text[3]); // B
```

The middle symbol occupies two code units, called a surrogate pair.

```
Index:       0       1       2       3
Code unit:  0041    D83D    DE00    0042
Code point:   A     [ 1F600  ]       B
```

Indexing at position 1 retrieves only the first half of the pair. Consequently, blindly reversing code units can break supplementary symbols.

### Reading numerical code units and code points

The string method charCodeAt(index) returns the numerical code unit at an index. codePointAt(index) returns the combined code point when the index begins a valid surrogate pair; otherwise it returns the single unit value. At an absent index, charCodeAt returns NaN and codePointAt returns undefined. Neither changes the string.

```javascript
const text = "A\u{1F600}B";
console.log(text.charCodeAt(0)); // 65
console.log(text.charCodeAt(1)); // 55357
console.log(text.charCodeAt(2)); // 56832
console.log(text.codePointAt(1)); // 128512
```

A supplementary code point is greater than 65535. This gives us a variable step size: advance two positions for such a code point, otherwise one.

```javascript
const text = "A\u{1F600}B";
let count = 0;
let i = 0;
while (i < text.length) {
  const point = text.codePointAt(i);
  count++;
  if (point > 65535) {
    i += 2;
  } else {
    i++;
  }
}
console.log(count); // 3
```

### Extracting a complete code point

The slice(start, end) string method returns the substring beginning at start and ending before end. It does not modify the original. Omitting end extracts the remaining text. For nonnegative indexes, an end beyond length is clipped to length; a start at or beyond length returns an empty string.

```javascript
const text = "ABCDE";
console.log(text.slice(1, 3)); // BC
console.log(text.slice(3)); // DE
console.log(text.slice(4, 10)); // E
```

Combine slice with the step size to extract a complete code point. Prepending each extracted substring reverses code-point order without splitting valid surrogate pairs.

```javascript
const text = "A\u{1F600}B";
let result = "";
let i = 0;
while (i < text.length) {
  let width = 1;
  if (text.codePointAt(i) > 65535) width = 2;
  result = text.slice(i, i + width) + result;
  i += width;
}
console.log(result === "B\u{1F600}A"); // true
```

This is not a complete human-character reversal. For example, "e\u0301" consists of e and a combining accent. Reversing its two code points separates the accent from its intended base. Emoji sequences can also contain several code points. Grapheme segmentation requires additional Unicode-aware facilities outside this chapter. Unless stated otherwise, subsequent examples deliberately use ASCII.

### Practice

- Predict code-unit and code-point counts for "A\u{1F600}B\u{1F600}". Expected: 6 and 4.
- Explain why increasing the index by one after codePointAt can count a surrogate pair twice.
- Reverse "X\u{1F600}Y" by code point. Expected: "Y\u{1F600}X".

## 4. Classifying characters

Classification assigns characters to categories. Consider an identifier containing letters and digits. We want separate counts for uppercase letters, lowercase letters, digits and other characters.

ASCII letters and digits occupy contiguous ranges of code-unit values. For single ASCII characters, relational string comparisons follow the same order. Therefore ch >= "0" && ch <= "9" identifies a decimal digit. This is a test of a single character, not of an entire numeric string.

```javascript
const text = "Ab9-Z2!";
let upper = 0;
let lower = 0;
let digits = 0;
let other = 0;
for (let i = 0; i < text.length; i++) {
  const ch = text[i];
  if (ch >= "A" && ch <= "Z") upper++;
  else if (ch >= "a" && ch <= "z") lower++;
  else if (ch >= "0" && ch <= "9") digits++;
  else other++;
}
console.log(upper, lower, digits, other); // 2 1 2 2
```

The else-if chain assigns each position to exactly one category. The sum of the counters must equal the input length. These ranges do not recognize all Unicode letters or digits.

### Vowels and case conversion

The toLowerCase() and toUpperCase() methods return new strings with case conversion applied. They do not modify their receiver. For ASCII, each letter maps to one corresponding letter. Unicode case conversion may change length, so do not assume that property for arbitrary text.

```javascript
const text = "Apple";
console.log(text.toLowerCase()); // apple
console.log(text.toUpperCase()); // APPLE
console.log(text); // Apple
```

To count English vowels, normalize case and search a small set of allowed characters. A nested loop avoids introducing an unexplained membership method.

```javascript
const text = "Education".toLowerCase();
const vowels = "aeiou";
let count = 0;
for (let i = 0; i < text.length; i++) {
  for (let j = 0; j < vowels.length; j++) {
    if (text[i] === vowels[j]) {
      count++;
      break;
    }
  }
}
console.log(count); // 5
```

The inner break ends only the membership search. The outer loop continues to the next character. For five fixed vowels, a chain of OR comparisons is also reasonable.

### Validating a text identifier

Require a nonempty identifier consisting only of ASCII letters, digits and underscores, with a letter or underscore first. Separate the first-position rule from the general rule. A flag records whether every position examined so far is valid.

```javascript
const text = "item_42";
let valid = text.length > 0;
for (let i = 0; valid && i < text.length; i++) {
  const ch = text[i];
  const letter = (ch >= "A" && ch <= "Z") ||
                 (ch >= "a" && ch <= "z");
  const digit = ch >= "0" && ch <= "9";
  if (!(letter || ch === "_" || (i > 0 && digit))) {
    valid = false;
  }
}
console.log(valid); // true
```

This is a deliberately simplified identifier specification, not the complete JavaScript identifier grammar. Empty input must fail even though it contains no invalid character.

### Practice

- Classify "A1b2!?". Expected upper 1, lower 1, digits 2, other 2.
- Count vowels in "Rhythm" and "AEIOU". Expected 0 and 5 under the five-vowel rule.
- Test identifier validation with "", "9item", "_x", "a-b", "a9". Expected false, false, true, false, true.

## 5. Building and transforming strings

String construction combines a traversal with a result accumulator. Decide whether each input position produces zero, one or multiple output characters. Filtering produces zero or one; simple substitution produces one; escaping may produce several.

### Filtering and replacement

Remove all ASCII spaces from a string without modifying other characters.

```javascript
const text = "a b  c";
let result = "";
for (let i = 0; i < text.length; i++) {
  if (text[i] !== " ") result += text[i];
}
console.log(result); // abc
```

This removes only literal spaces, not tabs or newlines. If those should also be removed, add explicit conditions. If spaces should instead become hyphens, every input position must contribute an output.

```javascript
const text = "red blue green";
let result = "";
for (let i = 0; i < text.length; i++) {
  if (text[i] === " ") result += "-";
  else result += text[i];
}
console.log(result); // red-blue-green
```

### Collapsing whitespace runs

Suppose the requirement is to replace every run of ASCII spaces, tabs and newlines with one space, while removing leading and trailing whitespace. Merely replacing each whitespace character produces repeated spaces. Instead, delay emitting a separator until another non-whitespace character arrives.

```javascript
const text = "  red\t\tblue\n green  ";
let result = "";
let pendingSpace = false;
for (let i = 0; i < text.length; i++) {
  const ch = text[i];
  const whitespace = ch === " " || ch === "\t" || ch === "\n";
  if (whitespace) {
    if (result.length > 0) pendingSpace = true;
  } else {
    if (pendingSpace) result += " ";
    result += ch;
    pendingSpace = false;
  }
}
console.log(result); // red blue green
```

Leading whitespace cannot set pendingSpace because the result is empty. Trailing whitespace sets the flag but never causes an append. This is a small state machine: the flag represents a pending separator, not a character already written.

### Expanding characters

To escape quotation marks and backslashes for a simplified quoted-text format, prepend a backslash before either character. This is not a complete JSON encoder; it handles only the two specified characters.

```javascript
const text = 'a"b\\c';
let result = "";
for (let i = 0; i < text.length; i++) {
  const ch = text[i];
  if (ch === '"' || ch === "\\") result += "\\";
  result += ch;
}
console.log(result === 'a\\"b\\\\c'); // true
```

### Joining pieces with separators

Given words, construct a comma-separated string without a trailing comma. Add the separator before each element except the first. This avoids needing to remove an unwanted suffix afterward.

```javascript
const words = ["red", "blue", "green"];
let result = "";
for (let i = 0; i < words.length; i++) {
  if (i > 0) result += ", ";
  result += words[i];
}
console.log(result); // red, blue, green
```

For an empty array the result is empty. For one word no separator is emitted. If empty words are meaningful, preserve them rather than using result.length as the separator condition.

### Practice

- Replace every digit in "A12B3" with "*". Expected "A**B*".
- Normalize "\t a   b\n" using the whitespace rules above. Expected "a b". Test whitespace-only input (empty result).
- Join ["", "a", ""] with "|". Expected "|a|". Explain why checking result.length would fail.

## 6. Searching strings

### Finding individual characters

The familiar first-occurrence search works for a single ASCII character. Use -1 to represent absence, not zero, because zero is a valid index.

```javascript
const text = "banana";
let position = -1;
for (let i = 0; i < text.length; i++) {
  if (text[i] === "a") {
    position = i;
    break;
  }
}
console.log(position); // 1
```

Backward traversal finds the last occurrence; collecting indexes without breaking finds all occurrences. For banana and a, these results are 5 and [1, 3, 5].

### Searching for a substring

A substring is a contiguous portion of a string. Searching for "ana" in "banana" requires comparing several consecutive positions. Comparing text[i] to "ana" cannot succeed because text[i] contains only one code unit.

At each possible starting index, compare the pattern against the corresponding text positions. If the pattern has length m and text has length n, the last possible starting index is n - m. A later start would leave too few positions.

```javascript
const text = "banana";
const pattern = "ana";
let position = -1;
for (let i = 0; i <= text.length - pattern.length; i++) {
  let matches = true;
  for (let j = 0; j < pattern.length; j++) {
    if (text[i + j] !== pattern[j]) {
      matches = false;
      break;
    }
  }
  if (matches) {
    position = i;
    break;
  }
}
console.log(position); // 1
```

| Start | Compared portion | Result |
|---:|---|---|
| 0 | ban | mismatch at first position |
| 1 | ana | match |

The inner flag must reset for each candidate. The expression i + j translates a pattern-relative index into a text index. The inclusive outer boundary is essential: a pattern may match exactly at the end.

For this first-match algorithm, an empty pattern matches at index zero because the inner loop has no comparisons to fail. A pattern longer than the text produces -1 because the outer loop never starts.

### Overlapping and non-overlapping matches

In "aaaa", the pattern "aa" occurs at indexes 0, 1 and 2 if overlaps are allowed. If matches must not share positions, the result is 0 and 2. The difference is the update after a successful match.

```javascript
const text = "aaaa";
const pattern = "aa";
const positions = [];
let i = 0;
while (pattern.length > 0 && i <= text.length - pattern.length) {
  let matches = true;
  for (let j = 0; j < pattern.length; j++) {
    if (text[i + j] !== pattern[j]) {
      matches = false;
      break;
    }
  }
  if (matches) {
    positions.push(i);
    i += pattern.length;
  } else {
    i++;
  }
}
console.log(positions); // [0, 2]
```

Change the successful update to i++ to allow overlaps. This example defines an empty pattern as producing no collected matches; the explicit guard also prevents a zero-step infinite loop. Different search contracts can legitimately choose different empty-pattern behavior.

### Replacing a substring

Use the non-overlapping search strategy, but build a result as you scan. When a match begins at the current index, append the replacement and skip the matched input. Otherwise append one original character. Newly produced output is not searched again.

```javascript
const text = "banana";
const pattern = "ana";
const replacement = "X";
let result = "";
let i = 0;
while (i < text.length) {
  let matches = pattern.length > 0 && i + pattern.length <= text.length;
  for (let j = 0; matches && j < pattern.length; j++) {
    if (text[i + j] !== pattern[j]) matches = false;
  }
  if (matches) {
    result += replacement;
    i += pattern.length;
  } else {
    result += text[i];
    i++;
  }
}
console.log(result); // bXna
```

An empty replacement removes matched substrings. An empty pattern leaves the input unchanged under this contract. Searching the growing result instead of the original could repeatedly process replacement text and fail to terminate.

### Practice

- Find the first occurrence of "end" in "the end". Expected 4. Test "end" in "end" (0) and "end" in "en" (-1).
- Collect overlapping and non-overlapping occurrences of "aba" in "ababa". Expected [0, 2] and [0].
- Replace non-overlapping "aa" with "b" in "aaaaa". Expected "bba".
- Debug an outer search condition using i < text.length - pattern.length. Explain why it misses a final-position match.

## 7. Comparing strings

Unlike arrays, strings are primitive values. Strict equality compares their contents directly, so "abc" === "abc" is true. A manual loop is useful for learning comparison rules or implementing custom equality, but unnecessary for ordinary exact equality.

### Case-insensitive equality

For ASCII text, normalize both strings to lowercase before comparing. If comparing manually, check lengths and stop at the first mismatch.

```javascript
const first = "Code".toLowerCase();
const second = "CODE".toLowerCase();
let equal = first.length === second.length;
for (let i = 0; equal && i < first.length; i++) {
  if (first[i] !== second[i]) equal = false;
}
console.log(equal); // true
```

For arbitrary Unicode, case conversion alone is not a complete linguistic comparison. For instance, composed and decomposed accents can have different code-unit sequences. This chapter does not implement locale-sensitive equality.

### Lexicographic comparison

Lexicographic ordering compares corresponding positions until the first difference. If all shared positions match, the shorter string comes first. For ASCII, uppercase letters precede lowercase letters in code-unit order; this is not necessarily dictionary order.

```javascript
const first = "car";
const second = "cart";
let order = 0;
let i = 0;
while (i < first.length && i < second.length) {
  if (first[i] < second[i]) {
    order = -1;
    break;
  }
  if (first[i] > second[i]) {
    order = 1;
    break;
  }
  i++;
}
if (order === 0) {
  if (first.length < second.length) order = -1;
  else if (first.length > second.length) order = 1;
}
console.log(order); // -1
```

The result contract is -1 for earlier, 0 for equal and 1 for later. Do not compare lengths first: "z" comes after "apple" despite being shorter.

### Palindrome testing

A palindrome reads the same in both directions. Rather than constructing a reversed copy, compare opposite positions and move inward. Stop at the first mismatch.

```javascript
const text = "level";
let left = 0;
let right = text.length - 1;
let palindrome = true;
while (left < right) {
  if (text[left] !== text[right]) {
    palindrome = false;
    break;
  }
  left++;
  right--;
}
console.log(palindrome); // true
```

The middle character of an odd-length string need not be compared with itself. Empty and single-character strings are palindromes under this contract. To ignore spaces and ASCII case, first build a normalized string using the earlier filtering and case-conversion techniques, then apply the comparison.

### Practice

- Compare "apple" and "apricot", "z" and "apple", "cat" and "cat". Expected -1, 1, 0.
- Test palindrome logic on "", "a", "abba", "abc". Expected true, true, true, false.
- Determine whether "Never odd or even" is a palindrome ignoring ASCII spaces and case. Expected true.

## 8. Splitting and processing text

Splitting separates text into pieces. The central design decision is what counts as a boundary and whether empty pieces should be preserved.

### Splitting on a delimiter

A delimiter is a character or sequence separating fields. For "red,blue,green", a comma marks the end of the current field. Maintain a temporary string for the current field and an array for completed fields.

```javascript
const text = "red,blue,green";
const fields = [];
let field = "";
for (let i = 0; i < text.length; i++) {
  if (text[i] === ",") {
    fields.push(field);
    field = "";
  } else {
    field += text[i];
  }
}
fields.push(field);
console.log(fields); // ["red", "blue", "green"]
```

The final append is essential because the last field is not followed by a delimiter. This contract preserves empty fields: ",a,," becomes ["", "a", "", ""], and empty input becomes [""]. Each delimiter creates a boundary, even when no text precedes it.

### Splitting on a multi-character delimiter

For "a::b::::c", the delimiter is "::", not a single colon. At each position, compare the entire delimiter using slice, then advance by its length on a match. Otherwise consume one character.

```javascript
const text = "a::b::::c";
const delimiter = "::";
const fields = [];
let field = "";
let i = 0;
while (i < text.length) {
  if (delimiter.length > 0 && text.slice(i, i + delimiter.length) === delimiter) {
    fields.push(field);
    field = "";
    i += delimiter.length;
  } else {
    field += text[i];
    i++;
  }
}
fields.push(field);
console.log(fields); // ["a", "b", "", "c"]
```

An empty delimiter is treated as no splitting in this example. The guard ensures progress. A full CSV parser requires additional rules for quoted fields and escaped quotes; this simple delimiter algorithm does not implement those rules.

### Extracting words

For this exercise, define a word as a maximal nonempty sequence of characters separated by spaces, tabs or newlines. Unlike field splitting, repeated separators should not create empty words.

```javascript
const text = "  red\tblue\n green  ";
const words = [];
let word = "";
for (let i = 0; i < text.length; i++) {
  const ch = text[i];
  if (ch === " " || ch === "\t" || ch === "\n") {
    if (word.length > 0) {
      words.push(word);
      word = "";
    }
  } else {
    word += ch;
  }
}
if (word.length > 0) words.push(word);
console.log(words); // ["red", "blue", "green"]
```

Compare this with delimiter splitting: both use a temporary accumulator and a final flush, but the word algorithm flushes only nonempty accumulators. Punctuation is part of a word under this simplified specification.

### Parsing unsigned decimal integers

Parsing converts text into a structured value. To parse "472", multiply the accumulated number by ten and add the next digit. We learned this positional reconstruction in Lesson 16.

For ASCII digits, charCodeAt returns consecutive values from 48 for zero through 57 for nine. Subtracting 48 yields the digit's numerical value. Validate before subtracting.

```javascript
const text = "472";
let value = 0;
let valid = text.length > 0;
for (let i = 0; valid && i < text.length; i++) {
  const code = text.charCodeAt(i);
  if (code < 48 || code > 57) {
    valid = false;
  } else {
    value = value * 10 + code - 48;
  }
}
if (valid) console.log(value);
else console.log("Invalid");
// 472
```

This accepts leading zeros but rejects signs, spaces, decimal points and empty input. Use small integers: JavaScript Number cannot exactly represent every integer beyond 9007199254740991. A production parser must also check numeric range. Do not report a partially accumulated number when validation fails.

### Practice

- Split "a,,b," on commas. Expected ["a", "", "b", ""].
- Extract words from "\t one  two\nthree". Expected ["one", "two", "three"]. Test empty and whitespace-only inputs (empty arrays).
- Parse "007", "12x", "", "0". Expected 7, invalid, invalid, 0.
- Explain why the final flush is unconditional for the field contract but conditional for the word contract.

## 9. Summary

| Requirement | Technique |
|---|---|
| Visit positions | Indexed traversal |
| Preserve supplementary symbols | Variable-width code-point traversal |
| Categorize ASCII text | Range tests and conditions |
| Modify text | Construct a new string |
| Find a substring | Candidate starts and nested comparisons |
| Compare text | Equality or first differing position |
| Split fields | Boundary detection and final flush |
| Parse digits | Validation and positional accumulation |

Always establish whether the task operates on code units, code points or grapheme clusters. Specify empty-input behavior, matching rules and separator handling before implementing a loop.

## 10. Integrated exercises

1. **Text report:** For "Ab 12!", report uppercase, lowercase, digits, spaces and other characters. Expected 1, 1, 2, 1, 1.
2. **Run compression:** Convert "AAABBCCCCA" to "A3B2C4A1". Preserve run order. Empty input produces empty output. Use a current run counter and flush the last run.
3. **Search and mask:** Replace every non-overlapping "cat" in "cat dog cat" with "***". Expected "*** dog ***". Test "catcat" ("******").
4. **Word statistics:** For "  red blue green  ", report count 3, longest word "green", total non-separator characters 12. Preserve the first longest word on ties.
5. **Field validation:** Split "12,007,x,0," and parse each field under the unsigned decimal contract. Expected 12, 7, invalid, 0, invalid.
6. **Normalized palindrome:** Ignore ASCII spaces and case in "Never odd or even". Expected true. Test "hello" (false).
7. **Unicode report:** For "A\u{1F600}B", report four code units, three code points and a code-point reversed string "B\u{1F600}A". Explain why this does not solve grapheme reversal generally.
8. **Debugging:** A substring search uses i < text.length - pattern.length. Supply a failing input and correct it. Example: text "abc", pattern "bc", expected index 1.
9. **Independent design:** Given "red::blue::::green", split on "::", preserve empty fields, then join with "|". Expected "red|blue||green".
10. **Comparison:** Given "ABC" and "abc", report exact equality false and ASCII case-insensitive equality true. Explain why this differs from comparing two separate arrays.

Solutions are withheld for independent practice.

Previous: [Working With Arrays](17-working-with-arrays.md) | Next: Frequency Counting and Grouping (planned)
