# nth Fibonacci Number

---

## Interviewer Prompt

The Fibonacci Sequence is the series of numbers:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
```

The next number is found by adding up the two numbers before it.

Write a function, `getNthFib`, that takes in a value, `n`, and returns the `n`th Fibonacci number.

## Example Output

```javascript
getNthFib(1);  // 0
getNthFib(2);  // 1
getNthFib(3);  // 1 
getNthFib(5);  // 3 
getNthFib(10);  // 34 
getNthFib(100);  // 218922995834555200000 
```

---

## Interviewer Guide

This guide will walk you through three approaches. The interviewee will probably get the naive method very quickly, which makes two recursive calls. Here, focus on the 'O' of REACTO by explaining that optimization is important for this problem, since for large values of `n`, the naive implementation may run very slowly (and/or the stack may overflow). Point out that a lot of work is being repeated (for example, `getNthFib(10)` will call `getNthFib(9)` and `getNthFib(8)` - while it is calculating `getNthFib(9)`, the engine will need to calculate `getNthFib(8)` and `getNthFib(7)`... so already you can see some overlaps). Ask the interviewee if he/she is familiar with memoization: https://en.wikipedia.org/wiki/Memoization.

Note that different people can have a different understanding of where the Fibonacci begins. (Is the 'first' Fibonacci number 0 or 1? Does n start at 0 or 1?) For our purposes, `getNthFib(1)` = 0, and `getNthFib(2)` = 1. All remaining values can be derived from those two. The user should not be feeding in values of `n` that are less than 1, such as 0.

---

### RE

At this point the interviewee should be asking you questions to clarify the problem statement. If they are not, prompt them: _"Do you have any questions before we get started?"_ Some questions you may get:
  - _What are my base inputs?_ 1 and 2.
  - _How big could n be?_ Potentially very large - that's why optimization matters!
  - _Can my input ever be non-positive or non-integer?_ No, do not worry about this case.

The interviewee should also be writing out the first few Fibonacci numbers, along with their `n` values. Check this against the Example Output above to make sure you're on the same page.

---

### AC

As stated above, most interviewees will probably go straight to the naive solution. Even if your interviewee does not, make sure to ask about it to ensure that he/she understands it. Try to get through this within the first 10 minutes, so there is time for you to walk your interviewee through memoization.

---

### TO

With the naive solution having been explored, make sure your interviewee understands why this implementation would be slow for large numbers of `n`. For example, if you try `getNthFib(100)` it will probably get your Chrome dev tools to stop responding...

---

### Answers to Common Questions

N/A

---

## Solution and Explanation (a)

This problem is often used as an introduction to recursion. By our base definitions, `getNthFib(1)` = 0, `getNthFib(2)` = 1, and for any other value of `n`, `getNthFib(n)` = `getNthFib(n - 1)` + `getNthFib(n - 2)`.

Therefore, the naive implementation is simple:

### Naive Solution Code

```javascript
function getNthFib(n) {
  if (n === 1) return 0;
  if (n === 2) return 1;
  return getNthFib(n - 1) + getNthFib(n - 2);
}
```

---

## Solution and Explanation (b)

Your interviewee may not be familiar with memoization. Dedicate the most time to explaining to your interviewee how it works. The idea is to 'cache' any pre-calculated values into some sort of store (such as a hash table) for quick reference to avoid having your engine rerun your algorithm for the same input twice.

### Memoization Solution Code

```javascript
function getNthFib(n, memo = {1: 0, 2: 1}) {
  if (memo[n] !== undefined) return memo[n];
  memo[n] = getNthFib(n - 1, memo) + getNthFib(n - 2, memo);
  return memo[n];
}
```

Note that when a user calls `getNthFib`, the user normally only expects to input `n`. However, based on how this method works, we will need to pass the reference to `memo` back and forth among the recursive calls, so it needs to be an argument as well. That's why here, we make `memo` an optional argument with a default value of `{1: 0, 2: 1}` which is an object that stores our base values. (Another option instead of a default value in the parameters is to include a line that says `memo = memo || {1: 0, 2: 1}`. This way, if a `memo` argument was supplied, it remains untouched, but if it was not, then `memo` gets initialized.)

The first line of the code checks whether our `memo` object already knows the answer for the given `n` key. If it does (because `memo[n]` is NOT undefined), then simply return the value stored at `memo[n]`. Otherwise, update the `memo` object by calculating and storing the sum of the two prior Fibonacci numbers into the `memo`, and then simply return the value you just stored at `memo[n]`. When referencing the two prior Fibonacci numbers using recursion, be sure to pass in the current state of your `memo` object as an argument.

No matter where the engine is in the call stack, all references to `memo` are pointing to the same place in memory. The recursed calls to `getNthFib` are NOT storing their own individual `memo`s!

---

## Solution and Explanation (c)

There is a simple way to tackle this problem without using a memo (which would take up O(n) space)! This is a neat trick that uses recursion in a way that seems 'iterative':

### 'Iterative' Solution Code

```javascript
function getNthFib(n, f = 0, F = 1) {
  return n === 1
    ? f
    : getNthFib(n - 1, F, f + F)
}
```

With this method, `n` effectively works like a counter. The optional arguments `f` and `F` work as placeholders for the current and next Fibonacci numbers, and are 'pre-loaded' with values of 0 and 1, respectively, by default. The final result, stored in `f`, is returned when `n` is equal to 1. Therefore, if the user invokes `getNthFib(1)`, the function is ready to return the default value of 0. If `n` is not 1, then the function is recursed, `n` is decremented, `F` takes the place of `f`, and `f + F` takes the place of `F`. Ultimately, the `getNthFib` function is run a total of `n` times, where the next Fibonacci number is calculated at every iteration.

Of course, the same thing could be accomplished without any recursion at all:

### True Iterative Solution Code

```javascript
function getNthFib(n) {
  let f = 0;                      // base value
  let F = 1;                      // base value
  for (let i = 1; i < n; i++) {
    [f, F] = [F, f + F];          // update values of f and F in one line of code using an array
  }
  return f;
}
```

---

## Summary

- Optimization is important. While it will not necessarily be the largest focus in REACTO going forward, I chose the classic Fibonacci problem to showcase the variety of ways to approach it, to introduce the concept of memoization, and to demonstrate that sometimes iteration can be more efficient than recursion (to avoid blowing up the call stack)!

- Trivia: Did you know that this problem can technically be solved in constant time and constant space using Binet's formula? (See https://en.wikipedia.org/wiki/Fibonacci_number#Binet's_formula)

```
F(n) = (phi^n - psi^n) / sqrt(5)

where phi = (1 + sqrt(5))/2 and psi = -1/phi.
```

---