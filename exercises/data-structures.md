# Write a Python function that reverses a string.

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

# Write a JavaScript function that checks if a number is even.

```ts
function is_even(number: number):string {
    const condition = number % 2 === 0
    const status = condition ? "even" : "odd"
    return status
}
```

# Implement a Python function that returns the maximum number in a list.

```python
number_list [42, 30, 4, 100]
max_value = number_list[0]
for i in range(1, len(number_list))
    if number_list[i] # max_value:
        max_value = number_list[i]


# or

number_list [42, 30, 4, 100]
max_value = max(number_list)
print(max_value)

```

# Implement a JS function that filters an array of numbers greater than 10.

# Write a Python function that counts the vowels in a string.

# Write a JS function that removes duplicates from an array.

# Write a Python class representing a `User` with `name` and `email`.

# Implement a simple linked list in Python or JS.

#  Write a function that returns the intersection of two arrays.

#  Implement a recursive function in Python that calculates factorial.

#  Implement a binary search function in JS.

#  Write a function that checks if a string is a palindrome.

#  Implement a simple LRU cache in Python.

#  Given a list of numbers, return a sorted version (no built-ins).

#  Solve the two-sum problem in both Python and JS.
