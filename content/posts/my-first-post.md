+++
date = '2026-01-01T23:49:20-03:00'
draft = false
title = 'Getting Started with Python List Comprehensions'
description = 'A quick introduction to Python list comprehensions and how they can make your code more concise'
+++

List comprehensions are one of Python's most elegant features. They provide a concise way to create lists based on existing lists or other iterables.

## Basic Syntax

The basic syntax of a list comprehension is:

```python
[expression for item in iterable]
```

Here's a simple example that creates a list of squares:

```python
numbers = [1, 2, 3, 4, 5]
squares = [n**2 for n in numbers]
print(squares)  # Output: [1, 4, 9, 16, 25]
```

## Adding Conditions

You can also add conditional logic to filter items:

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_squares = [n**2 for n in numbers if n % 2 == 0]
print(even_squares)  # Output: [4, 16, 36, 64, 100]
```

## Nested List Comprehensions

For more complex scenarios, you can nest list comprehensions:

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [num for row in matrix for num in row]
print(flattened)  # Output: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## When to Use Them

List comprehensions are great for:

- Simple transformations
- Filtering data
- Creating lists from other iterables

However, if the logic gets too complex, it's better to use a regular for loop for readability.

Happy coding!
