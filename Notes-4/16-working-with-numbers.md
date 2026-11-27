# Lesson 16 - Working With Numbers

Previous: [Nested Loops](15-nested-loops.md) | Next: Working With Arrays

## 1. Processing numeric ranges

For small integers, divisibility by a nonzero integer is tested with `n % d === 0`. Filter a range or generate multiples directly. The sum of multiples of three from 1 through 20 is 63.

```javascript
let total = 0;
for (let n = 1; n <= 20; n++) {
  if (n % 3 === 0) total += n;
}
console.log(total); // 63
```

Direct generation uses `for (let n = 3; n <= 20; n += 3)`. AND requires both divisibility tests; OR requires either. Zero is divisible by any nonzero integer. Negative remainders do not invalidate the zero-remainder test.

Practice: sum multiples of five through 50 (275); collect multiples of four from -10 through 10 ([-8,-4,0,4,8]).

## 2. Divisibility and factors

A positive factor divides a positive integer exactly. Search candidates from 1 through the number, excluding zero. Proper factors exclude the number itself; a perfect number equals the sum of its proper factors.

```javascript
const n = 12;
const factors = [];
for (let d = 1; d <= n; d++) {
  if (n % d === 0) factors.push(d);
}
console.log(factors); // [1,2,3,4,6,12]
```

Factors occur in pairs d and n/d. At least one member is at most the square root. Stop when d*d exceeds n. Count equal partners only once.

```javascript
const n = 36;
let count = 0;
for (let d = 1; d*d <= n; d++) {
  if (n % d === 0) {
    count++;
    if (n/d !== d) count++;
  }
}
console.log(count); // 9
```

Practice: list factors of 24; count and sum factors of 18 (6,39); test whether 28 is perfect (true); count factors of 49 (3).

## 3. Prime numbers

A prime is an integer greater than one with exactly two positive factors. Search for a nontrivial factor and stop immediately when one is found. The square-root boundary is inclusive, otherwise perfect squares may be incorrectly classified.

```javascript
const primes = [];
for (let n = 2; n <= 30; n++) {
  let prime = true;
  for (let d = 2; d*d <= n; d++) {
    if (n % d === 0) {
      prime = false;
      break;
    }
  }
  if (prime) primes.push(n);
}
console.log(primes); // [2,3,5,7,11,13,17,19,23,29]
```

Reset the flag for each number. To reduce work further, test two separately and then examine odd candidates only. Remember that two itself is prime.

Practice: classify 0,1,2,9,25,29 (false,false,true,false,false,true); find the first prime greater than 50 (53).

## 4. Repeated arithmetic

Multiplication is repeated addition for integer multipliers. Initialize the accumulator to zero; use the absolute multiplier and negate the result for a negative multiplier.

```javascript
let product = 0;
for (let i = 0; i < 4; i++) product += 7;
console.log(product); // 28
```

Integer division of nonnegative integers by positive integers can be modeled by subtracting complete groups. Check the proposed subtraction before performing it.

```javascript
let remaining = 17;
let quotient = 0;
while (remaining >= 5) {
  remaining -= 5;
  quotient++;
}
console.log(quotient, remaining); // 3 2
```

Exponentiation repeats multiplication. Initialize to one, the multiplicative identity. A zero exponent requires no iterations.

```javascript
let result = 1;
for (let i = 0; i < 4; i++) result *= 3;
console.log(result); // 81
```

Factorial multiplies integers from one through n; zero factorial is one. Successive factorials reuse the preceding result.

```javascript
let factorial = 1;
for (let n = 1; n <= 5; n++) {
  factorial *= n;
  console.log(n, factorial);
}
```

Practice: multiply -8 by -3 (24); divide 29 by 6 (4 remainder 5); compute 2 to power 10 (1024); generate factorials 0 through 6 (1,1,2,6,24,120,720).

## 5. Processing digits

For a nonnegative integer, `n % 10` extracts the units digit. `Math.trunc(n / 10)` removes it. Math.trunc returns the integer portion, discarding the fraction toward zero. Use Math.abs first for negative integers if the sign is irrelevant.

```javascript
let remaining = 4728;
let total = 0;
let count = 0;
let reversed = 0;
do {
  const digit = remaining % 10;
  total += digit;
  count++;
  reversed = reversed * 10 + digit;
  remaining = Math.trunc(remaining / 10);
} while (remaining > 0);
console.log(total, count, reversed); // 21 4 8274
```

The extracted digits are 8,2,7,4. A do...while handles zero as one digit. Numeric reversal discards leading zeros. Preserve the original number to test whether it equals its reversal (a palindrome).

For a largest digit, initialize a maximum to zero and update it when a larger digit appears. For frequencies, initialize ten array counters to zero and increment counts[digit] on each extraction.

Practice: count digits of 58320,0,-72 (5,1,2); sum and multiply digits of 234 (9,24); reverse 5832 and 1200 (2385,21); test 12321,12345,0 for palindrome (true,false,true); count even and odd digits of 572804 (4,2).

## 6. Greatest common divisor

The GCD of two positive integers is their largest shared positive factor. Search downward from the smaller number and stop at the first common divisor.

```javascript
const a = 48;
const b = 18;
let candidate = a;
if (b < candidate) candidate = b;
while (a % candidate !== 0 || b % candidate !== 0) candidate--;
console.log(candidate); // 6
```

Euclid's algorithm uses the fact that a and b have the same common divisors as b and a%b. Save the remainder before replacing either operand. The nonnegative remainder is smaller than the positive divisor, ensuring termination.

```javascript
let a = 48;
let b = 18;
while (b !== 0) {
  const remainder = a % b;
  a = b;
  b = remainder;
}
console.log(a); // 6
```

Trace: (48,18), (18,12), (12,6), (6,0). For nonnegative inputs, gcd(a,0)=a; use zero for gcd(0,0) by convention in this chapter. Normalize negative inputs with Math.abs.

Practice: gcd(84,30)=6; gcd(17,13)=1; gcd(0,12)=12.

## 7. Least common multiple

The LCM is the smallest positive common multiple of two positive integers. Generate multiples of one number until divisible by the other.

```javascript
const a = 12;
const b = 18;
let multiple = a;
while (multiple % b !== 0) multiple += a;
console.log(multiple); // 36
```

For positive integers, lcm(a,b) = (a/gcd(a,b))*b. Divide before multiplying to reduce intermediate magnitude. Define LCM as zero if either input is zero.

Practice: lcm(8,12)=24; lcm(7,5)=35; lcm(6,6)=6.

## 8. Number base conversion

A base determines the number of available digit symbols and the place-value multiplier. Binary uses 0 and 1; decimal uses 0 through 9. Convert decimal to base 2 by repeatedly extracting remainder modulo 2 and truncating division by 2. Prepend each digit to a string because extraction is right to left.

```javascript
let remaining = 13;
let binary = "";
do {
  const digit = remaining % 2;
  binary = digit + binary;
  remaining = Math.trunc(remaining / 2);
} while (remaining > 0);
console.log(binary); // 1101
```

To decode, multiply the accumulated value by the base and add the next digit. The string index retrieves a character. Explicit comparisons distinguish binary digits and reject invalid characters.

```javascript
const binary = "1101";
let decimal = 0;
let valid = binary.length > 0;
for (let i = 0; i < binary.length; i++) {
  if (binary[i] !== "0" && binary[i] !== "1") {
    valid = false;
    break;
  }
  let digit = 0;
  if (binary[i] === "1") digit = 1;
  decimal = decimal * 2 + digit;
}
if (valid) console.log(decimal); // 13
else console.log("Invalid binary");
```

Practice: convert 25 to binary (11001); convert 83 to octal (123); decode 10110 (22); reject 102 and empty input.

## 9. Summary

Numeric loops generate candidates, test divisibility, accumulate results, extract digits or reduce a problem to a smaller one. Establish valid input domains, correct initial values and reachable stopping conditions. These examples assume small integers to avoid Number precision limitations.

## 10. Integrated exercises

1. Find all perfect numbers from 1 through 100 (6,28).
2. Find prime pairs whose sum is 20, excluding reversed pairs (3+17,7+13).
3. Collect palindromes from 10 through 50 (11,22,33,44).
4. Calculate GCD and LCM of 24 and 36 (12,72).
5. Convert 42 to binary (101010) and decode it back (42).
6. Count the digits of 10020 and report their sum and largest digit (5,3,2).

Previous: [Nested Loops](15-nested-loops.md) | Next: Working With Arrays (planned)
