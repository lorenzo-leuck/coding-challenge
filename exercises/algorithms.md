1. If `p = x/4` and `q = x/5` and `p + q = 18`, find the value of `x`.

40

2. Write pseudocode that takes three numbers and outputs them in ascending order using only comparisons and swaps.

```py
# Fixed 3-number sort
def sortThree(a, b, c):
    if a > b:
        temp = a
        a = b
        b = temp
    if b > c:
        temp = b
        b = c
        c = temp
    if a > b:
        temp = a
        a = b
        b = temp
    return [a, b, c]

# General bubble sort for n numbers
def sortNumbers(n):
    numbers = [int(input()) for _ in range(n)]
    for i in range(n):
        for j in range(n-i-1):
            if numbers[j] > numbers[j+1]:
                temp = numbers[j]
                numbers[j] = numbers[j+1]
                numbers[j+1] = temp
    return numbers

sortThree(3, 1, 2)
sortNumbers(3)
```

1. Explain step-by-step how to find the greatest common divisor (GCD) of two numbers using the Euclidean algorithm.

2. Write pseudocode that counts the number of vowels in a given string.

3. Given two arrays of integers, write pseudocode that returns a new array with only the elements found in both arrays.

4. Write pseudocode to find the largest and smallest number in an array in one pass.

5. Show two methods to swap `x` and `y` without using a temporary variable: arithmetic and bitwise XOR.

6. Consider this pseudocode:
```
for i = 1 to n:
    for j = 1 to i:
        do O(1) work
```
What is the time complexity in Big-O notation? Explain your reasoning.

1.  Describe the preconditions for binary search and write pseudocode for it. What is its time complexity?

2.  Write pseudocode to reverse the words in a sentence while keeping the words themselves unchanged.

3.  Given a list of numbers, write pseudocode to return a new list containing only the numbers that appear exactly once.

4.  Write pseudocode to generate the first 10 numbers in the Fibonacci sequence.

5.  Given an array of test scores, write pseudocode to calculate the average and print all scores above the average.

6.  Write pseudocode to check if a string is a palindrome.

7.  Explain the difference between linear search and binary search in terms of efficiency.

8.  Given two sorted arrays, write pseudocode to merge them into a single sorted array.

9.  Write pseudocode for a simple sorting algorithm (e.g., selection sort) and describe its time complexity.

10. Given a list of students and their grades, write pseudocode to sort the students from highest to lowest grade and display the results.

11. You are designing a basic route-finding program for a map. Describe in simple steps how you would find the shortest route between two points using a method like Dijkstra’s algorithm, but in plain language and pseudocode without advanced notation.
