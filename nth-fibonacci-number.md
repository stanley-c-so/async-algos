# Multiplicative Persistence

---

## Interviewer Prompt

Given a non-negative integer, write a function that returns its multiplicative persistence--the number of times you must multiply the digits in a number together until you reach a single digit product. 

```
Ex: 39 returns 3

3 x 9 = 27
2 x 7 = 14
1 x 4 = 4
```
---

## Example Output

```javascript
persistence(39);  // 3 
persistence(111); // 1
persistence(1);   // 0
```

---

## Interviewer Guide

This problem can be approached iteratively or recursively. Make sure you understand both solutions so that you can guide the interviewee no matter which option they take.

---

### RE

At this point the interviewer should be asking you questions to clarify the problem statement. If they are not, prompt them: _"Do you have any questions before we get started?"_ Some questions you may get.
  - _How many digits can my input have?_ It can have any number of digits
  - _What do I return if I input a single digit number?_ 0
  - _Can my input ever be negative?_ No, do not worry about this case.
  - _What base is my input in?_ Hm... you're getting a little ahead of yourself there. But it's base 10.

---

### AC

Some people's thoughts might race to doing division and modulo math to find the individual components of the input, but there might be an easier way... If they do bring up math though, let them talk through that approach before suggesting string and number coercion (thank you JS for making this possible).

---

### TO

---

### Answers to Common Questions


---

## Solution and Explanation (a)

You're given a number, you need to break it down, multiply the parts, increment a count, rinse, and repeat. If you're thinking recursion, you're not wrong!

What would be your base case? If your number is a single digit, you're done. You've had to do 0 rounds of multiplication.

Otherwise, you'll want to break apart your digits and start multiplying. Once you have a product, you'll want to feed it to your function again. Your result should be 1 + the number of times the resulting product needs to go through this process again.

---

### Solution Code

```javascript
function persistence(num) {
  const stringNum = String(num);
  if (String(num).length === 1) return 0;
  
  // We can split the strings into an array and then multiply the values to return a product using a reduce function
  // This works in JS because the engine sees two numbers of string data type and coerces them back to being numbers when you try to multiple them
  const newNum = stringNum.split('').reduce((product, factor) => product * factor);
  return 1 + persistence(newNum);
}
```

If you're not a fan of the `Array.reduce()` method, here's a `for` loop with the same result.
```javascript
function persistence(num) {
  const stringNum = String(num);
  if (String(num).length === 1) return 0;
  
  let newNum = 1;
  
  for (let i = 0; i < stringNum.length; i++) {
    newNum *= stringNum[i];
  }
  
  return 1 + persistence(newNum);
}
```

---

## Solution and Explanation (b)

You can also implement an iterative solution. The approach here is to maintain a count that you increment every time you go through a cycle of multiplication and checks.

---

### Solution Code

```javascript
function persistence(num) {
  let result = 0;
  let stringNum = String(num);
  
  while (stringNum.length > 1) {
    result++;
    const product = stringNum.split('').reduce((total, factor) => total * factor);
    
    // Set stringNum to the new product. Cast the product back into a string again before you execute the while loop condition
    stringNum = String(product);
  }
  
  return result;
}
```

---

## Summary

- While we took advantage of a lot of JS quirks to get concise solutions, note that these shortcuts might not be available in other languages. You may have to more actively cast values to different types in order to get working solutions as you move between different data types to get our answer.

---