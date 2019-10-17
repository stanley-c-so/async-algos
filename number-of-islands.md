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
- As your peer works on the helper function (see __AC__ section), your peer may come up with a *recursive* algorithm to conduct a Breadth-First Search (BFS) or a Depth-First Search (DFS). If this happens, only after the solution is fully fleshed out, you can nudge your peer toward an iterative (i.e. non-recursive) solution so as not to burden the call stack.
- Make sure that your peer’s code properly handles out-of-bound indices when trying to traverse adjacent cells in `grid` (either by avoiding them altogether as recursive inputs, or returning before attempting to access them from the matrix).
- Any solution with time complexity greater than `O(n⋅m)` (where `n` and `m` are the dimensions of `grid`) is not optimal.

### RE

At this point the interviewee should be asking you questions to clarify the problem statement. If they are not, prompt them: _"Do you have any questions before we get started?"_ Some questions you may get:
  - _Any strange inputs?_ You can assume that `grid` is a proper 2D array with elements that are either `0` or `1`. You can assume that all subarrays (rows) have equal length. However, what if the grid is `1 x m`? `1 x 1`? `0 x 0`? Will your interviewee's solution break?
  - _Should I be mutating the input?_ For our purposes, that's fine. If for some reason you wanted to preserve the original input grid, you could tweak your algorithm by making a deep copy of the input and performing all operations on the deep copy. Either way, space complexity will still be proportional to the area of the grid, because even if you mutate the input, you will either build up the call stack (with a recursive solution) or occupy space in your iterative queue.

### AC

- The key to this problem is to __write a good helper function__. __The main function should traverse every position in the matrix__, and if it happens to find a land, it should invoke the helper function at that position. __By passing the reference to `grid` to your helper function, any mutations made by the helper function updates the `grid` object in real time__.
- __The purpose of the helper function is to "process" the entire island by traversing it and marking it as "visited" in some way so as not to be double counted as a new island by the main function__. (That way, when the main function finds an unvisited land, it will always be a new, distinct island.) Make sure your peer's code does not have duplicate island counting!

### TO

- Time complexity: `O(n⋅m)` because every cell in the grid will need to be traversed once by the main function, and even in the extreme case that the majority of the cells will be re-examined by the helper function after having been visited already, there will be at most 4 (i.e. a constant number) of re-examinations of that particular cell triggered by the initial visit to the cell's 4 neighbors.
- Space complexity: `O(n⋅m)` (i.e. the same as the time complexity, which follows the traversal of the entire grid) because as land is traversed, neighboring lands will either occupy the call stack or occupy space in the queue depending on your implementation.

## Solution and Explanation

In the recursive solution, the helper function only inspects one cell at a time. If that cell is in bounds and is a land, it turns the land into water (you can turn the land into anything else too, as long as it does not remain land) and then runs a recursive call at each of the cell's 4 neighbors. (To save stack space, you may prefer to check if each neighbor is in bounds before recursing at all, rather than checking it at the beginning of the helper function.)

### Recursive Solution Code

```javascript
var numIslands = function(grid) {
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

function helper(grid, row, col) {
    if (                                            // if:
        !(row >= 0 && row < grid.length)            // row coords are out of bounds...
        || !(col >= 0 && col < grid[0].length)      // ...OR col coords are out of bounds...
        || grid[row][col] !== 1                     // ... OR [row][col] does not point to land...
    ) return;                                       // then do not continue
    grid[row][col] = 0;           // turn land at current coords into water (or whatever else)
    helper(grid, row - 1, col);   // recurse up
    helper(grid, row + 1, col);   // recurse down
    helper(grid, row, col - 1);   // recurse left
    helper(grid, row, col + 1);   // recurse right
}
```

In the iterative solution, the helper function again inspects only one cell at a time, beginning with a queue pre-loaded with the initial cell. While the queue has elements, if the current element is a land, the function turns that element into water (or whatever else) and then pushes the cell's 4 neighbors into the queue. (To save queue space, you may prefer to check if each neighbor is in bounds before pushing it into the queue at all, rather than checking it as you pop it out of the queue.)

### Iterative Solution Code

```javascript
var numIslands = function(grid) {
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

function helper(grid, row, col) {
    const queue = [[row, col]];
    while (queue.length) {
      [currentRow, currentCol] = queue.shift();
      if (                                                    // if:
        (currentRow >= 0 && currentRow < grid.length)         // currentRow coords are in bounds...
        && (currentCol >= 0 && currentCol < grid[0].length)   // ...AND currentCol coords are in bounds...
        && grid[currentRow][currentCol] === 1                 // ...AND [currentRow][currentCol] points to land 
      ) {
        grid[currentRow][currentCol] = 0;           // turn land at current coords into water (or whatever else)
        queue.push([currentRow - 1, currentCol]);   // next, check up
        queue.push([currentRow + 1, currentCol]);   // next, check down
        queue.push([currentRow, currentCol - 1]);   // next, check left
        queue.push([currentRow, currentCol + 1]);   // next, check right
      }
    }
}
```

Notice that in both methods you can either check if a neighbor is in bounds before or after recursing or pushing it into your queue. Arguably this is a tradeoff between readability and efficiency. Some programmers may even prefer to write another helper function for testing validity, allowing them to shorten the clunky `if` condition inside the helper and taking up less space (see below). However, it is better to check if the current element is a land _after_ you dequeue it as blocks of land will result in the same cells being placed in queue multiple times, and therefore the cell will no longer be land after the first time it is processed (and its neighbors should not be analyzed).

### Variations

```javascript
function isInBounds (grid, row, col) {
  return (                                  // a given (row, col) is in bounds if:
    (row >= 0 && row < grid.length)         // row coords are in bounds...
    && (col >= 0 && col < grid[0].length)   // ...AND col coords are in bounds
  );
}

function helper(grid, row, col) {
    if (grid[row][col] !== 1) return;
    grid[row][col] = 0;
    if (isInBounds(grid, row - 1, col)) helper(grid, row - 1, col);
    if (isInBounds(grid, row + 1, col)) helper(grid, row + 1, col);
    if (isInBounds(grid, row, col - 1)) helper(grid, row, col - 1);
    if (isInBounds(grid, row, col + 1)) helper(grid, row, col + 1);
}
```

```javascript
function pushIfInBounds (grid, row, col, queue) {
  if (row >= 0 && row < grid.length && col >= 0 && col < grid[0].length) {
    queue.push([row, col]);
  }
}

function helper(grid, row, col) {
    const queue = [[row, col]];
    while (queue.length) {
      [currentRow, currentCol] = queue.shift();
      if (grid[currentRow][currentCol] === 1) {
        grid[currentRow][currentCol] = 0;
        pushIfInBounds(grid, currentRow - 1, currentCol, queue);
        pushIfInBounds(grid, currentRow + 1, currentCol, queue);
        pushIfInBounds(grid, currentRow, currentCol - 1, queue);
        pushIfInBounds(grid, currentRow, currentCol + 1, queue);
      }
    }
}
```

## Summary

Key concepts in this problem:
- matrix traversal
- helper function
- recursion vs. queue
- micro-optimization by checking validity (being in-bounds)
- potential edge cases arising from cells being enqueued multiple times, depending on implementation

## Credits

- This .md was a collaboration between Jing Cao (github.com/peoplemakeculture) and Stanley So (github.com/stanley-c-so)

## References

- Problem adapted from LeetCode #200 (medium): https://leetcode.com/problems/number-of-islands/
- Note that in the LeetCode problem, the matrix values are `'0'` and `'1'` instead of `0` and `1`.