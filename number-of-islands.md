# Number of Islands

## Interviewer Prompt

Given a matrix (2D array) of `1`s (land) and `0`s (water), implement a function `numIslands` count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

For example, in the image below, there are 6 different islands highlighted in different colors to distinguish them:

![](./number-of-islands_pic.png)

## Example Output

Example 1:
```javascript
const grid = [
  [1, 1, 1, 1, 0],
  [1, 1, 0, 1, 0],
  [1, 1, 0, 0, 0],
  [0, 0, 0, 0, 0],
];

console.log(numIslands(grid));    // 1
```

Example 2:
```javascript
const grid = [
  [1, 1, 0, 0, 0],
  [1, 1, 0, 0, 0],
  [0, 0, 1, 0, 0],
  [0, 0, 0, 1, 1],
];

console.log(numIslands(grid));    // 3
```

## Interviewer Guide

- If your peer is stuck, ask them if they know how to traverse an undirected graph - that may kickstart some ideas.
- The key to this problem is to __write a good helper function__. __The main function should traverse every position in the matrix__, and if it happens to find a land, it should invoke the helper function at that position.
- __The purpose of the helper function is to "process" the entire island by traversing it and marking it as "visited" in some way so as not to be double counted as a new island by the main function__. (That way, when the main function finds an unvisited land, it will always be a new, distinct island.) Make sure your peer's code does not have duplicate island counting!
- As your peer works on the helper function, your peer may come up with a recursive algorithm to conduct a Breadth-First Search (BFS) or a Depth-First Search (DFS). Only after the solution is fully fleshed out, you can nudge your peer toward an iterative (i.e. non-recursive) solution so as not to burden the call stack.
- Make sure that your peer’s code properly handles out-of-bound indices when trying to traverse adjacent cells in `grid` (either by avoiding them altogether as recursive inputs, or returning before attempting to access them from the matrix).
- Any solution with time complexity greater than `O(n⋅m)` (where `n` and `m` are the dimensions of `grid`) is not optimal.

### RE

<!-- At this point the interviewee should be asking you questions to clarify the problem statement. If they are not, prompt them: _"Do you have any questions before we get started?"_ Some questions you may get:
  - _What are my base inputs?_ 1 and 2.
  - _How big could n be?_ Potentially very large - that's why optimization matters!
  - _Can my input ever be non-positive or non-integer?_ No, do not worry about this case.

The interviewee should also be writing out the first few Fibonacci numbers, along with their `n` values. Check this against the Example Output above to make sure you're on the same page. -->

### AC

<!-- As stated above, most interviewees will probably go straight to the naive solution. Even if your interviewee does not, make sure to ask about it to ensure that he/she understands it. Try to get through this within the first 10 minutes, so there is time for you to walk your interviewee through memoization. -->

### TO

<!-- With the naive solution having been explored, make sure your interviewee understands why this implementation would be slow for large numbers of `n`. For example, if you try `getNthFib(100)` it will probably get your Chrome dev tools to stop responding... -->

### Answers to Common Questions

<!-- N/A -->

## Solution and Explanation

<!-- This problem is often used as an introduction to recursion. By our base definitions, `getNthFib(1)` = 0, `getNthFib(2)` = 1, and for any other value of `n`, `getNthFib(n)` = `getNthFib(n - 1)` + `getNthFib(n - 2)`.

Therefore, the naive implementation is simple: -->

### Solution Code

<!-- ```javascript
function getNthFib(n) {
  if (n === 1) return 0;
  if (n === 2) return 1;
  return getNthFib(n - 1) + getNthFib(n - 2);
}
``` -->

## Summary

<!-- - Optimization is important. While it will not necessarily be the largest focus in REACTO going forward, I chose the classic Fibonacci problem to showcase the variety of ways to approach it, to introduce the concept of memoization, and to demonstrate that sometimes iteration can be more efficient than recursion (to avoid blowing up the call stack)!

- Trivia: Did you know that this problem can technically be solved in constant time and constant space using Binet's formula? (See https://en.wikipedia.org/wiki/Fibonacci_number#Binet's_formula)

```
F(n) = (phi^n - psi^n) / sqrt(5)

where phi = (1 + sqrt(5))/2 and psi = -1/phi.
``` -->

## Credits

- This .md was a collaboration between Jing Cao (github.com/peoplemakeculture) and Stanley So (github.com/stanley-c-so)

## References

- Problem adapted from LeetCode #200 (medium): https://leetcode.com/problems/number-of-islands/