1. Write a Python function that reverses a string.

```python
my_string = "hello"
my_array = list(my_string)
array_length = len(my_array)
inverted_array = []

for i in range(my_array):
    inverted_array.append(my_array[array_length - 1 -i])

inverted_string = ''.join(inverted_array)


## ou

my_string="hello"
inverted_string = my_string[::-1]

```

2. Write a JavaScript function that checks if a number is even.

```ts
function is_even(number: number):string {
    const condition = number % 2 === 0
    const status = condition ? "even" : "odd"
    return status
}
```

3. Implement a Python function that returns the maximum number in a list.

```python
number_list [42, 30, 4, 100]
max_value = number_list[0]
for i in range(1, len(number_list))
    if number_list[i] > max_value:
        max_value = number_list[i]


# or

number_list [42, 30, 4, 100]
max_value = max(number_list)
print(max_value)

```

4. Implement a JS function that filters an array of numbers greater than 10.
5. Write a Python function that counts the vowels in a string.
6. Write a JS function that removes duplicates from an array.
7. Write a Python class representing a `User` with `name` and `email`.
8. Implement a simple linked list in Python or JS.
9.  Write a function that returns the intersection of two arrays.
10. Implement a recursive function in Python that calculates factorial.
11. Implement a binary search function in JS.
12. Write a function that checks if a string is a palindrome.
13. Implement a simple LRU cache in Python.
14. Given a list of numbers, return a sorted version (no built-ins).
15. Solve the two-sum problem in both Python and JS.