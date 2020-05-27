# Number of Islands

## Interviewer Prompt

Given a binary matrix (2D array) of `1`s (land) and `0`s (water), implement a function `numIslands` to count the number of islands. An island is a block of land surrounded by water. It can be formed by connecting adjacent lands horizontally or vertically, but NOT diagonally. You may assume all four edges of the grid are all surrounded by water.

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

- If your partner is stuck, ask them what they know about Depth-First Search (DFS). The key to this question is understanding that once they find a "land" `1` in the grid, they need to mark the adjacent `1`s recursively, or with a stack. The can be done with a helper function that uses a stack, or a helper function that is recursive. (This just goes to show that recursive solutions are ultimately still stack-based solutions - they use the call stack itself as a stack!)
- If you have time after the solution is fully fleshed out, you can ask your partner how they might approach this problem the other way.
- Make sure that your partner's code properly handles out-of-bound indices when trying to traverse adjacent cells in `grid` (either by avoiding them altogether as recursive inputs, or returning before attempting to access them from the matrix). JavaScript is relatively forgiving, but other languages may crash if you go out of bounds.
- NOTE: Did your partner attempt to solve this with Breadth-First Search or a queue? This is still a valid approach, but think about why this might not be desirable. Does BFS actually offer any advantages here over DFS?

### RE

At this point the interviewee should be asking you questions to clarify the problem statement. If they are not, prompt them: _"Do you have any questions before we get started?"_ Some questions you may get:
  - _Any strange inputs?_ You can assume that `grid` is a proper 2D array with elements that are either `0` or `1`. You can assume that all subarrays (rows) have equal length. However, what if the grid is `1 x m`? `1 x 1`? `0 x 0`? Will your interviewee's solution break?
  - _Should I be mutating the input?_ For our purposes, that's fine. If for some reason you wanted to preserve the original input grid, you could tweak your algorithm by making a deep copy of the input and performing all operations on the deep copy. Either way, space complexity will still be proportional to the area of the grid, because even if you mutate the input, you will either build up the call stack (with a recursive solution) or occupy space in your iterative stack.

### AC

- The key to this problem is to __write a good helper function__. __The main function should traverse every position in the matrix__, and if it happens to find a land, it should invoke the helper function at that position. __By passing the reference to `grid` to your helper function, any mutations made by the helper function updates the `grid` object in real time__.
- __The purpose of the helper function is to "process" the entire island by traversing it and marking it as "visited" in some way so as not to be double counted as a new island by the main function__. (That way, when the main function finds an unvisited land, it will always be a new, distinct island.) Make sure your partner's code does not have duplicate island counting!

### TO

- Time complexity: `O(n⋅m)` (the dimensions of the grid) because every cell in the grid will need to be traversed once by the main function, and even in the extreme case that the majority of the cells will be re-examined by the helper function after having been visited already, there will be at most 4 (i.e. a constant number) of re-examinations of that particular cell triggered by the initial visit to the cell's 4 neighbors.
- Space complexity: `O(n⋅m)` (i.e. the same as the time complexity, which follows the traversal of the entire grid) because as land is traversed, neighboring lands will either occupy the call stack or occupy space in the iterative stack depending on your implementation.

## Solution and Explanation

### Recursive Solution Code

In the recursive solution, the helper function only inspects one cell at a time. If that cell is in bounds and is a land, it turns the land into water (you can turn the land into anything else too, as long as it does not remain land) and then runs a recursive call at each of the cell's 4 neighbors. (To save stack space, you may prefer to check if each neighbor is in bounds before recursing at all, rather than checking it at the beginning of the helper function.)

```javascript
function numIslands (grid) {
    let count = 0;
    for (let row = 0; row < grid.length; row++) {           // iterate through every row and col
        for (let col = 0; col < grid[0].length; col++) {
           if (grid[row][col] === 1) {                      // if land is found, it must be a new island
               count++                                      // so, increase the island count
               helper(grid, row, col);                      // and turn that entire island into water
           }
        }
    }
    return count;
};

function helper (grid, row, col) {
    if (                                          // if:
        !(row >= 0 && row < grid.length) ||       // row coords are out of bounds...
        !(col >= 0 && col < grid[0].length) ||    // ...OR col coords are out of bounds...
        grid[row][col] !== 1                      // ... OR [row][col] does not point to land...
    ) return;                                     // then do not continue
    grid[row][col] = 0;           // turn land at current coords into water (or whatever else)
    helper(grid, row - 1, col);   // recurse up
    helper(grid, row + 1, col);   // recurse down
    helper(grid, row, col - 1);   // recurse left
    helper(grid, row, col + 1);   // recurse right
}
```

### Iterative Solution Code

In the iterative solution, the helper function again inspects only one cell at a time, beginning with a stack pre-loaded with the initial cell. While the stack has elements, if the current element is a land, the function turns that element into water (or whatever else) and then pushes the cell's 4 neighbors into the stack. (To save stack space, you may prefer to check if each neighbor is in bounds before pushing it into the stack at all, rather than checking it as you pop it out of the stack.)

```javascript
function numIslands (grid) {
    let count = 0;
    for (let row = 0; row < grid.length; row++) {           // iterate through every row and col
        for (let col = 0; col < grid[0].length; col++) {
           if (grid[row][col] === 1) {                      // if land is found, it must be a new island
               count++                                      // so, increase the island count
               helper(grid, row, col);                      // and turn that entire island into water
           }
        }
    }
    return count;
};

function helper (grid, row, col) {
    const stack = [[row, col]];
    while (stack.length) {
      [currentRow, currentCol] = stack.pop();
      if (                                                    // if:
        (currentRow >= 0 && currentRow < grid.length) &&      // currentRow coords are in bounds...
        (currentCol >= 0 && currentCol < grid[0].length) &&   // ...AND currentCol coords are in bounds...
        grid[currentRow][currentCol] === 1                    // ...AND [currentRow][currentCol] points to land 
      ) {
        grid[currentRow][currentCol] = 0;           // turn land at current coords into water (or whatever else)
        stack.push([currentRow - 1, currentCol]);   // next, check up
        stack.push([currentRow + 1, currentCol]);   // next, check down
        stack.push([currentRow, currentCol - 1]);   // next, check left
        stack.push([currentRow, currentCol + 1]);   // next, check right
      }
    }
}
```

### Variations

Notice that in both methods you can either check if a neighbor is in bounds and contains land before or after recursing or pushing it into your stack. Arguably this is a tradeoff between readability and efficiency. Some programmers may even prefer to write another helper function for testing validity, allowing them to shorten the clunky `if` condition inside the helper and taking up less space (see below).

```javascript
function isInBoundsAndContainsLand (grid, row, col) {     // for checking if a given position is in bounds and contains land
  return (
    (row >= 0 && row < grid.length) &&
    (col >= 0 && col < grid[0].length) &&
    grid[row][col] === 1
  );
}

function helper (grid, row, col) {
  grid[row][col] = 0;
  if (isInBoundsAndContainsLand(grid, row - 1, col)) helper(grid, row - 1, col);    // only recurse if in bounds and contains land
  if (isInBoundsAndContainsLand(grid, row + 1, col)) helper(grid, row + 1, col);
  if (isInBoundsAndContainsLand(grid, row, col - 1)) helper(grid, row, col - 1);
  if (isInBoundsAndContainsLand(grid, row, col + 1)) helper(grid, row, col + 1);
}
```

```javascript
function pushIfInBoundsAndContainsLand (grid, row, col, stack) {        // for checking if a given position is in bounds and contains land
  if (
    (row >= 0 && row < grid.length) && 
    (col >= 0 && col < grid[0].length) &&
    grid[row][col] === 1
  ) {
    stack.push([row, col]);
  }
}

function helper (grid, row, col) {
  const stack = [[row, col]];
  while (stack.length) {
    [currentRow, currentCol] = stack.pop();
    grid[currentRow][currentCol] = 0;
    pushIfInBoundsAndContainsLand(grid, currentRow - 1, currentCol, stack);     // only push into stack if in bounds and contains land
    pushIfInBoundsAndContainsLand(grid, currentRow + 1, currentCol, stack);
    pushIfInBoundsAndContainsLand(grid, currentRow, currentCol - 1, stack);
    pushIfInBoundsAndContainsLand(grid, currentRow, currentCol + 1, stack);
  }
}
```

## Summary

Key concepts in this problem:
- matrix traversal
- helper function
- recursion vs. stack
- micro-optimization by checking validity (being in-bounds)

## References

- Problem adapted from LeetCode #200 (medium): https://leetcode.com/problems/number-of-islands/
- Note that in the LeetCode problem, the matrix values are `'0'` and `'1'` instead of `0` and `1`.
- The algorithm used by the helper function is known as 'Flood fill': https://en.wikipedia.org/wiki/Flood_fill

## Credits

- This .md was a collaboration between Jing Cao (github.com/peoplemakeculture) of GH and Stanley So (github.com/stanley-c-so) of FSA!