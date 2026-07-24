# Python Functional Programming — Teaching Guide

---

## Table of Contents

1. [Core Python Basics](#1-core-python-basics)
   - 1.1 String Formatting with `%`
   - 1.2 `sorted()` for Sorting Lists
   - 1.3 Variadic Arguments with `*args`
   - 1.4 The `if __name__ == "__main__"` Pattern
2. [Lambda Expressions / Anonymous Functions](#2-lambda-expressions--anonymous-functions)
3. [Higher-Order Functions](#3-higher-order-functions)
   - 3.1 Passing Functions as Arguments
   - 3.2 Returning Functions
4. [Built-in Functional Tools](#4-built-in-functional-tools)
   - 4.1 `map()` — Applying a Function to a Sequence
   - 4.2 `filter()` — Selecting Elements by a Predicate
   - 4.3 `reduce()` — Folding a Sequence into a Single Value
   - 4.4 Combining `map`, `filter`, and `sorted`
5. [Closures and the `nonlocal` Keyword](#5-closures-and-the-nonlocal-keyword)
6. [Partial Functions](#6-partial-functions)
7. [Recursion](#7-recursion)
   - 7.1 Standard Recursion
   - 7.2 Tail Recursion with an Accumulator

---

## 1. Core Python Basics

Before we dive into functional programming, let's make sure we have a solid grip on a few Python fundamentals that appear throughout the examples.

---

### 1.1 String Formatting with `%`

The `%` operator is one of Python's classic ways to embed values inside strings. Think of it like a fill-in-the-blanks template.

**Format specifiers:**
| Specifier | Meaning |
|-----------|---------|
| `%d` | Integer |
| `%f` | Float |
| `%s` | String |

**Syntax:**
```python
"template string %d and %s" % (value1, value2)
```

**Examples:**
```python
name = "Alice"
age = 30

# Single value — no tuple needed
print("Hello, %s!" % name)
# Output: Hello, Alice!

# Multiple values — use a tuple
print("Name: %s, Age: %d" % (name, age))
# Output: Name: Alice, Age: 30

# Formatting numbers
score = 95.678
print("Your score is %.2f" % score)
# Output: Your score is 95.68

# From our files:
print("You passed %d, %d" % (10, 20))
# Output: You passed 10, 20
```

> 💡 **Note:** Modern Python also uses f-strings (`f"Hello {name}"`) and `.format()`, but `%` formatting is still widely used and important to understand.

---

### 1.2 `sorted()` for Sorting Lists

`sorted()` returns a **new sorted list** without modifying the original. This is in contrast to `.sort()` which sorts in-place.

**Syntax:**
```python
sorted(iterable, key=None, reverse=False)
```

**Examples:**
```python
names = ["Zak", "Tim", "Ben", "Joe", "Kim", "Bud", "Ted", "Baz"]

# Basic alphabetical sort
sorted_names = sorted(names)
print(sorted_names)
# Output: ['Baz', 'Ben', 'Bud', 'Joe', 'Kim', 'Ted', 'Tim', 'Zak']

# Original list is unchanged
print(names)
# Output: ["Zak", "Tim", "Ben", "Joe", "Kim", "Bud", "Ted", "Baz"]

# Sort in reverse
print(sorted(names, reverse=True))
# Output: ['Zak', 'Tim', 'Ted', 'Kim', 'Joe', 'Bud', 'Ben', 'Baz']

# Sort by string length
print(sorted(names, key=len))
# Output: ['Zak', 'Tim', 'Ben', 'Joe', 'Kim', 'Bud', 'Ted', 'Baz']

# Sort numbers
numbers = [5, 2, 8, 1, 9, 3]
print(sorted(numbers))
# Output: [1, 2, 3, 5, 8, 9]
```

---

### 1.3 Variadic Arguments with `*args`

Sometimes you want a function that can accept **any number of arguments**. Python uses `*args` for this.

- `*args` collects all extra positional arguments into a **tuple**.
- The name `args` is a convention — what matters is the `*`.

**Examples:**
```python
# Basic *args usage
def greet(*names):
    for name in names:
        print("Hello,", name)

greet("Alice")
# Output: Hello, Alice

greet("Alice", "Bob", "Charlie")
# Output:
# Hello, Alice
# Hello, Bob
# Hello, Charlie

# *args collects into a tuple
def show_args(*args):
    print(type(args))   # <class 'tuple'>
    print(args)

show_args(1, 2, 3)
# Output: <class 'tuple'>
#         (1, 2, 3)

# Mixing regular and variadic args
def fixed_and_variable(a, b, *rest):
    print("Fixed:", a, b)
    print("Variable:", rest)

fixed_and_variable(1, 2, 3, 4, 5)
# Output:
# Fixed: 1 2
# Variable: (3, 4, 5)
```

**Unpacking with `*`:**

The `*` can also be used to **unpack** a tuple into individual arguments when calling a function:

```python
def multiply(a, b, c, d):
    return a * b * c * d

fixed = (2, 3)
extra = (4, 5)

# Unpack both tuples into the function call
result = multiply(*fixed, *extra)
print(result)
# Output: 120   (2 * 3 * 4 * 5)
```

> 💡 This unpacking trick is used inside `PartialFunctionsHowTheyWork.py` — we'll revisit it in the Partial Functions section.

---

### 1.4 The `if __name__ == "__main__"` Pattern

You'll see this at the bottom of many Python files:

```python
def main():
    # your code here
    pass

if __name__ == "__main__":
    main()
```

**Why does this exist?**

Every Python file has a built-in variable called `__name__`.

- When you **run** a file directly, `__name__` is set to `"__main__"`.
- When a file is **imported** by another file, `__name__` is set to the file's module name instead.

```python
# greetings.py
print("__name__ is:", __name__)

def say_hello():
    print("Hello!")

if __name__ == "__main__":
    say_hello()
```

```bash
# Running directly:
$ python greetings.py
__name__ is: __main__
Hello!

# Importing from another file:
# import greetings
# __name__ is: greetings
# (say_hello is NOT called automatically)
```

**Why is this good practice?**
- Keeps your code **reusable** — other files can import your functions without triggering side effects.
- Makes it clear where the program **entry point** is.
- Enables **testing** — you can import functions without running the whole script.

---

## 2. Lambda Expressions / Anonymous Functions

### What is a Lambda?

A **lambda** is a small, anonymous (nameless) function defined in a single line. Unlike regular functions defined with `def`, lambdas are written inline and are often used for short, throwaway operations.

**Syntax:**
```
lambda parameters: expression
```

- Can have **zero, one, or many** parameters.
- Has exactly **one expression** — which is automatically returned.
- No `return` keyword needed.
- No multi-line statements.

---

### Defining and Calling Lambdas

```python
# A regular function
def square(x):
    return x * x

# The same thing as a lambda
square_lam = lambda x: x * x

print(square(5))      # Output: 25
print(square_lam(5))  # Output: 25
```

```python
# Zero parameters
greet = lambda: print("Hello!")
greet()
# Output: Hello!

# One parameter
double = lambda x: x * 2
print(double(7))
# Output: 14

# Two parameters
multiply = lambda x, y: x * y
print(multiply(4, 5))
# Output: 20

# Three parameters
msg = lambda x, y, z: print("lambda arguments are %d, %d and %d" % (x, y, z))
msg(3, 4, 5)
# Output: lambda arguments are 3, 4 and 5
```

---

### Lambdas vs Regular Functions

```python
# These two are equivalent:

# Option 1 — regular function
def add(a, b):
    return a + b

# Option 2 — lambda
add_lam = lambda a, b: a + b

print(add(10, 20))      # Output: 30
print(add_lam(10, 20))  # Output: 30
```

**When to use a lambda:**
- For **short**, simple expressions.
- When you need a function **only once** (inline use).
- As arguments to other functions (we'll see this with `map`, `filter`, and `sorted`).

**When to use `def`:**
- For **complex** logic with multiple steps.
- When the function needs a **docstring** or clear name.
- When the function will be used **many times**.

---

### Lambdas in Action

```python
# Using lambda with sorted()
names = ["Charlie", "Alice", "Bob"]
sorted_by_length = sorted(names, key=lambda name: len(name))
print(sorted_by_length)
# Output: ['Bob', 'Alice', 'Charlie']

# Lambda doing arithmetic
cube = lambda x: x * x * x
print(cube(3))   # Output: 27
print(cube(4))   # Output: 64
```

> 💡 **Key Insight:** Lambdas are just functions. The word "anonymous" simply means they don't require a name like `def` functions do. When you assign them to a variable (`sq = lambda x: x*x`), they behave exactly like a normal function.

---

## 3. Higher-Order Functions

### What is a Higher-Order Function?

A **Higher-Order Function (HOF)** is a function that either:
1. **Takes a function as an argument**, or
2. **Returns a function as its result** (or both).

This is one of the most powerful ideas in functional programming. Functions in Python are **first-class citizens** — they can be stored in variables, passed around, and returned just like any other value (integers, strings, lists, etc.).

---

### 3.1 Passing Functions as Arguments

```python
# A higher-order function that applies any operation to two numbers
def apply(x, y, op):
    return op(x, y)

# Pass different lambdas to perform different operations
result1 = apply(10, 20, lambda x, y: x + y)
print(result1)   # Output: 30

result2 = apply(10, 20, lambda x, y: x * y)
print(result2)   # Output: 200

result3 = apply(10, 20, lambda x, y: x - y)
print(result3)   # Output: -10

result4 = apply(10, 20, lambda x, y: x / y)
print(result4)   # Output: 0.5
```

Notice that `apply` doesn't care **what** the operation does — it just calls it. This makes `apply` extremely flexible and reusable.

You can also pass **named functions**, not just lambdas:

```python
def add(x, y):
    return x + y

def subtract(x, y):
    return x - y

result = apply(100, 40, add)
print(result)   # Output: 140

result = apply(100, 40, subtract)
print(result)   # Output: 60
```

---

### 3.2 Returning Functions

A function can also **create and return** a new function. This is incredibly useful for building customized functions on the fly.

```python
def concat(str1, str2):
    return str1 + str2

print(concat("Hello", "World"))
# Output: HelloWorld

# flip() takes a binary function and returns a new function
# with its two arguments REVERSED
def flip(binaryOp):
    return lambda x, y: binaryOp(y, x)

flipConcat = flip(concat)

result = flipConcat("Hello", "World")
print(result)
# Output: WorldHello
# Because flip swapped the arguments: concat("World", "Hello")
```

**Visualizing `flip`:**

```
Normal:    concat("Hello", "World")  →  "HelloWorld"
Flipped:  flipConcat("Hello", "World")  →  concat("World", "Hello")  →  "WorldHello"
```

Another example — flip with subtraction:

```python
subtract = lambda x, y: x - y

flipped_subtract = flip(subtract)

print(subtract(10, 3))          # Output: 7   (10 - 3)
print(flipped_subtract(10, 3))  # Output: -7  (3 - 10, arguments are swapped)
```

---

### Why Higher-Order Functions Matter

Higher-order functions let you write **generic, reusable code**. Instead of writing a separate function for every operation, you write one flexible function and plug in different behaviours:

```python
def apply_twice(f, x):
    return f(f(x))

double = lambda x: x * 2
add_ten = lambda x: x + 10

print(apply_twice(double, 3))   # Output: 12   (double(double(3)) = double(6) = 12)
print(apply_twice(add_ten, 5))  # Output: 25   (add_ten(add_ten(5)) = add_ten(15) = 25)
```

---

## 4. Built-in Functional Tools

Python provides three powerful built-in functions — `map()`, `filter()`, and `reduce()` — that are the backbone of functional-style data processing. They all operate on sequences (like lists) and accept functions as arguments, making them Higher-Order Functions.

---

### 4.1 `map()` — Applying a Function to a Sequence

`map()` applies a function to **every element** in a sequence and returns the transformed results.

**Syntax:**
```python
map(function, iterable)
```

**Think of it as:** "Do this to every item."

```python
# Using a named function
def square(x):
    return x * x

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

result = list(map(square, numbers))
print(result)
# Output: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# Using a lambda (more common in practice)
result = list(map(lambda x: x * x, numbers))
print(result)
# Output: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

> 💡 `map()` returns a **map object** (lazy iterator), so we wrap it in `list()` to see the results.

More examples:

```python
words = ["hello", "world", "python"]

# Convert all to uppercase
upper = list(map(lambda w: w.upper(), words))
print(upper)
# Output: ['HELLO', 'WORLD', 'PYTHON']

# Get the length of each word
lengths = list(map(len, words))
print(lengths)
# Output: [5, 5, 6]

# Add decoration around each name
def top_and_tail(element):
    return "***" + element + "***"

names = ["Ben", "Baz", "Bud"]
decorated = list(map(top_and_tail, names))
print(decorated)
# Output: ['***Ben***', '***Baz***', '***Bud***']
```

---

### 4.2 `filter()` — Selecting Elements by a Predicate

`filter()` keeps only the elements for which a **predicate function** returns `True`.

**Syntax:**
```python
filter(function, iterable)
```

**Think of it as:** "Keep only items that pass this test."

A **predicate** is simply a function that returns `True` or `False`.

```python
# Using a named predicate
def is_even(x):
    return (x % 2) == 0

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

evens = list(filter(is_even, numbers))
print(evens)
# Output: [2, 4, 6, 8, 10]

# Using a lambda
odds = list(filter(lambda x: x % 2 != 0, numbers))
print(odds)
# Output: [1, 3, 5, 7, 9]
```

More examples:

```python
# Filter names starting with 'B'
def starts_with_b(element):
    return len(element) > 0 and element[0] == 'B'

names = ["Zak", "Tim", "Ben", "Joe", "Kim", "Bud", "Ted", "Baz"]
b_names = list(filter(starts_with_b, names))
print(b_names)
# Output: ['Ben', 'Bud', 'Baz']

# Filter numbers greater than 5
numbers = [1, 8, 3, 9, 2, 7, 4, 6, 5]
big = list(filter(lambda x: x > 5, numbers))
print(big)
# Output: [8, 9, 7, 6]
```

---

### 4.3 `reduce()` — Folding a Sequence into a Single Value

`reduce()` applies a function **cumulatively** to the elements of a sequence, reducing them to a **single result**.

It must be imported from `functools`:

```python
from functools import reduce
```

**Syntax:**
```python
reduce(function, iterable)
```

**Think of it as:** "Combine all items step by step into one value."

**How it works — step by step:**

```python
from functools import reduce

numbers = [3, 12, 19, 1, 2, 7]

result = reduce(lambda x, y: x + y, numbers)
print(result)
# Output: 44
```

The computation unfolds like this:
```
Step 1:  3  + 12 = 15
Step 2: 15  + 19 = 34
Step 3: 34  +  1 = 35
Step 4: 35  +  2 = 37
Step 5: 37  +  7 = 44  ← final result
```

More examples:

```python
from functools import reduce

# Find the maximum value
nums = [3, 7, 2, 9, 4]
maximum = reduce(lambda a, b: a if a > b else b, nums)
print(maximum)
# Output: 9

# Multiply all values together
product = reduce(lambda x, y: x * y, [1, 2, 3, 4, 5])
print(product)
# Output: 120   (1 * 2 * 3 * 4 * 5)

# Concatenate strings
words = ["Hello", " ", "World", "!"]
sentence = reduce(lambda a, b: a + b, words)
print(sentence)
# Output: Hello World!
```

---

### 4.4 Combining `map`, `filter`, `reduce`, and `sorted`

The real power comes from **chaining** these tools together to build data pipelines.

**Example 1 — Filter, then sort, then map:**

```python
def starts_with_b(element):
    return len(element) > 0 and element[0] == 'B'

def top_and_tail(element):
    return "***" + element + "***"

names = ["Zak", "Tim", "Ben", "Joe", "Kim", "Bud", "Ted", "Baz"]

# Step 1: Keep only names starting with B
b_names = list(filter(starts_with_b, names))
print(b_names)
# Output: ['Ben', 'Bud', 'Baz']

# Step 2: Sort them alphabetically
sorted_b_names = sorted(b_names)
print(sorted_b_names)
# Output: ['Baz', 'Ben', 'Bud']

# Step 3: Decorate each name
final = list(map(top_and_tail, sorted_b_names))
print(final)
# Output: ['***Baz***', '***Ben***', '***Bud***']
```

**Example 2 — Map then reduce (squares and sum):**

```python
from functools import reduce

items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Square all numbers
squares = list(map(lambda x: x * x, items))
print(squares)
# Output: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# Sum all squared numbers
total = reduce(lambda x, y: x + y, squares)
print(total)
# Output: 385

# One-liner version
total = reduce(lambda x, y: x + y, map(lambda x: x * x, items))
print(total)
# Output: 385
```

**Example 3 — Filter and map with lambdas only:**

```python
items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Get even numbers only
evens = list(filter(lambda x: (x % 2) == 0, items))
print(evens)
# Output: [2, 4, 6, 8, 10]

# Square each even number
squared_evens = list(map(lambda x: x * x, evens))
print(squared_evens)
# Output: [4, 16, 36, 64, 100]
```

> 💡 **Pipeline Thinking:** Read these chains left to right — data flows in, gets filtered, transformed, and reduced. This style of writing code is clean, readable, and very common in data processing.

---

## 5. Closures and the `nonlocal` Keyword

### What is a Closure?

A **closure** is a function that "remembers" variables from the scope in which it was created — even after that outer scope has finished executing.

In simpler terms: **a closure carries its environment with it.**

This is a fundamental concept in functional programming and powers many patterns like decorators, callbacks, and stateful functions.

---

### Understanding Scope First

```python
def outer():
    message = "Hello from outer"

    def inner():
        print(message)   # inner can see outer's variable

    inner()

outer()
# Output: Hello from outer
```

`inner` can read `message` from `outer`. That's **normal scope**. A closure takes it a step further — `inner` can be **returned** and still remember `message`.

---

### A Simple Closure

```python
def banner(start, end):
    # Returns a lambda that "closes over" start and end
    return lambda msg: print("%s %s %s" % (start, msg, end))

# Create a customised banner function
bannerMsg = banner("[---", "---]")

# Even though banner() has finished executing,
# bannerMsg still remembers start and end
bannerMsg("Hello")
# Output: [--- Hello ---]

bannerMsg("World")
# Output: [--- World ---]
```

**What happened?**
- `banner("[---", "---]")` runs and returns a lambda.
- That lambda "closes over" the values `start = "[---"` and `end = "---]"`.
- When we later call `bannerMsg("Hello")`, it still has access to those captured values.

**You can create multiple independent closures:**

```python
stars  = banner("***", "***")
arrows = banner(">>>", "<<<")
dots   = banner("...", "...")

stars("Python")    # Output: *** Python ***
arrows("is fun")   # Output: >>> is fun <<<
dots("closures")   # Output: ... closures ...
```

---

### The `nonlocal` Keyword

By default, a nested function can **read** variables from its enclosing scope, but it **cannot reassign** them. The `nonlocal` keyword grants permission to modify an outer variable.

```python
def counter():
    count = 0

    def increment():
        nonlocal count     # Tell Python: use the 'count' from the enclosing scope
        count += 1
        return count

    return increment

c = counter()
print(c())  # Output: 1
print(c())  # Output: 2
print(c())  # Output: 3

# A completely separate counter — independent state!
c2 = counter()
print(c2()) # Output: 1
print(c())  # Output: 4  (c still has its own count)
```

Without `nonlocal`, `count += 1` would raise an `UnboundLocalError` because Python would treat `count` as a new local variable.

---

### Closures with State — The Fibonacci Example

Here's a beautiful use of closures: a stateful Fibonacci generator.

```python
def fib():
    tup = (1, -1)   # Initial tuple: (current, previous)

    def retFunc():
        nonlocal tup
        tup = (tup[0] + tup[1], tup[0])  # new = current + previous, new_prev = current
        return tup[0]

    return retFunc

f = fib()
print(f())  # Output: 0   (1 + -1)
print(f())  # Output: 1   (0 + 1)
print(f())  # Output: 1   (1 + 0)
print(f())  # Output: 2   (1 + 1)
print(f())  # Output: 3   (2 + 1)
print(f())  # Output: 5   (3 + 2)
print(f())  # Output: 8   (5 + 3)
```

**Step-by-step trace:**

| Call | `tup` before | Calculation | `tup` after | Returned |
|------|-------------|-------------|------------|---------|
| 1st  | (1, -1)     | 1 + (-1) = 0 | (0, 1)    | 0       |
| 2nd  | (0, 1)      | 0 + 1 = 1   | (1, 0)    | 1       |
| 3rd  | (1, 0)      | 1 + 0 = 1   | (1, 1)    | 1       |
| 4th  | (1, 1)      | 1 + 1 = 2   | (2, 1)    | 2       |
| 5th  | (2, 1)      | 2 + 1 = 3   | (3, 2)    | 3       |
| 6th  | (3, 2)      | 3 + 2 = 5   | (5, 3)    | 5       |

Each call to `f()` updates and remembers the state stored in `tup` — this is closure at work.

> 💡 **Key Insight:** Closures give functions **memory**. Each closure instance maintains its own independent copy of the closed-over variables, making them perfect for creating **stateful** behaviours without needing classes or global variables.

---

## 6. Partial Functions

### What is a Partial Function?

A **partial function** is a new function created by **fixing some arguments** of an existing function. The result is a function that needs fewer arguments to be called.

Think of it as: "I know some of the inputs ahead of time — let me bake those in and create a simpler function."

---

### Using `functools.partial`

```python
from functools import partial

multiply = lambda x, y: x * y

# Fix the first argument to 2 — creates a "times 2" function
times2 = partial(multiply, 2)

# Fix the first argument to 5 — creates a "times 5" function
times5 = partial(multiply, 5)

# Fix the first argument to 8 — creates a "times 8" function
times8 = partial(multiply, 8)

print("10 times 2 is %d" % times2(10))  # Output: 10 times 2 is 20
print("10 times 5 is %d" % times5(10))  # Output: 10 times 5 is 50
print("10 times 8 is %d" % times8(10))  # Output: 10 times 8 is 80
```

`times2` is now a function that only needs **one argument** — the second one. The first (`2`) is already fixed.

---

### Building Partial from Scratch

To understand how `partial` works internally, let's build our own version:

```python
def mypartial(op, *fixedArgs):
    print("Fixed args:", fixedArgs)
    funcToCall = lambda *otherArgs: op(*fixedArgs, *otherArgs)
    return funcToCall
```

**How it works:**
1. `mypartial` takes a function `op` and some fixed arguments `*fixedArgs`.
2. It returns a new lambda that accepts additional arguments `*otherArgs`.
3. When called, it combines the fixed and new arguments and calls the original function.

```python
multiply = lambda a, b, c, d: a * b * c * d

# Fix just one argument
times2 = mypartial(multiply, 2)
# Fixed args: (2,)

print(times2(3, 4, 5))   # Output: 120  →  2 * 3 * 4 * 5
print(times2(30, 40, 50)) # Output: 120000  →  2 * 30 * 40 * 50

# Fix two arguments
times2times3 = mypartial(multiply, 2, 3)
# Fixed args: (2, 3)

print(times2times3(4, 5))   # Output: 120  →  2 * 3 * 4 * 5
print(times2times3(40, 50)) # Output: 12000  →  2 * 3 * 40 * 50

# Fix three arguments
times2times3times4 = mypartial(multiply, 2, 3, 4)
# Fixed args: (2, 3, 4)

print(times2times3times4(5))  # Output: 120  →  2 * 3 * 4 * 5
print(times2times3times4(50)) # Output: 1200  →  2 * 3 * 4 * 50
```

---

### Real-World Use Case

Partial functions shine when you have a generic function and need **specialised versions** of it:

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

# Create specialised power functions
square = partial(power, exponent=2)
cube   = partial(power, exponent=3)

print(square(4))  # Output: 16
print(cube(3))    # Output: 27

# Another example — logging with a fixed prefix
def log(level, message):
    print("[%s] %s" % (level, message))

info  = partial(log, "INFO")
error = partial(log, "ERROR")
warn  = partial(log, "WARN")

info("Server started")    # Output: [INFO] Server started
error("File not found")   # Output: [ERROR] File not found
warn("Low memory")        # Output: [WARN] Low memory
```

> 💡 **Key Insight:** Partial functions promote **code reuse**. Instead of writing `times2 = lambda x: x * 2` and `times5 = lambda x: x * 5` separately, you define multiplication once and specialise it. As logic grows more complex, this becomes increasingly valuable.

---

## 7. Recursion

### What is Recursion?

**Recursion** is when a function calls **itself** to solve a problem. Each recursive call works on a smaller version of the same problem, until it reaches a **base case** — a condition where it stops calling itself and returns a direct answer.

Every recursive function needs:
1. A **base case** — to stop the recursion.
2. A **recursive case** — a call to itself with a smaller/simpler input.

---

### 7.1 Standard Recursion

**Classic example — Factorial:**

Factorial of n (written `n!`) is: `n × (n-1) × (n-2) × ... × 2 × 1`

```
4! = 4 × 3 × 2 × 1 = 24
```

```python
def factorial(n):
    if n == 0:           # Base case: 0! = 1
        return 1
    else:
        return n * factorial(n - 1)  # Recursive case

result = factorial(4)
print("4 factorial is %d" % result)
# Output: 4 factorial is 24
```

**Call stack visualised:**

```
factorial(4)
  └─ 4 * factorial(3)
           └─ 3 * factorial(2)
                    └─ 2 * factorial(1)
                             └─ 1 * factorial(0)
                                      └─ returns 1     ← base case hit

Now the calls return upwards:
1 * 1 = 1
2 * 1 = 2
3 * 2 = 6
4 * 6 = 24  ← final answer
```

More examples of recursion:

```python
# Sum of a list
def sum_list(lst):
    if len(lst) == 0:        # Base case: empty list sums to 0
        return 0
    else:
        return lst[0] + sum_list(lst[1:])   # head + sum of rest

print(sum_list([1, 2, 3, 4, 5]))
# Output: 15

# Countdown
def countdown(n):
    if n <= 0:
        print("Go!")
    else:
        print(n)
        countdown(n - 1)

countdown(5)
# Output: 5, 4, 3, 2, 1, Go!
```

---

### 7.2 Tail Recursion with an Accumulator

**Standard recursion** builds up a chain of operations that must be evaluated on the way **back up** the call stack. This can be a problem for very large inputs — it uses a lot of memory because every call is held in the stack waiting to complete.

**Tail recursion** solves this by doing the calculation **on the way down** using an **accumulator** — a variable that carries the running result.

```python
# Standard recursion — result built on the way BACK UP
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)   # must wait for recursive call to finish, then multiply


# Tail recursive — result built on the way DOWN via accumulator
def tailRecursiveFactorial(accumulator, n):
    if n == 0:
        return accumulator          # base case: return the accumulated result
    else:
        return tailRecursiveFactorial(n * accumulator, n - 1)

result = tailRecursiveFactorial(1, 4)   # Start with accumulator = 1
print("4 factorial is %d" % result)
# Output: 4 factorial is 24
```

**Call stack trace — tail recursive:**

```
tailRecursiveFactorial(1, 4)
  └─ tailRecursiveFactorial(4*1=4, 3)
           └─ tailRecursiveFactorial(3*4=12, 2)
                    └─ tailRecursiveFactorial(2*12=24, 1)
                             └─ tailRecursiveFactorial(1*24=24, 0)
                                      └─ returns 24  ← base case, answer is already there!
```

**Side-by-side comparison:**

| | Standard Recursion | Tail Recursion |
|---|---|---|
| Work done | On the way **back up** | On the way **down** |
| Accumulator | No | Yes (carries running result) |
| Stack frames | All held simultaneously | Only current one needed |
| Risk | Stack overflow on large inputs | More memory efficient |
| Initial call | `factorial(4)` | `tailRecursiveFactorial(1, 4)` |

**Another tail recursive example — sum:**

```python
# Standard
def sum_standard(lst):
    if not lst:
        return 0
    return lst[0] + sum_standard(lst[1:])

# Tail recursive
def sum_tail(lst, acc=0):
    if not lst:
        return acc
    return sum_tail(lst[1:], acc + lst[0])

print(sum_standard([1, 2, 3, 4, 5]))  # Output: 15
print(sum_tail([1, 2, 3, 4, 5]))      # Output: 15
```

> 💡 **Note for Python:** Unlike functional languages such as Haskell or Scala, Python does **not** automatically optimise tail calls. Python has a default recursion limit of 1000 calls. However, understanding tail recursion is still valuable because it teaches the **accumulator pattern** — a key concept in functional thinking.

---

## Summary — Concepts at a Glance

| Concept | What it does | Key Syntax / Tool |
|---|---|---|
| String formatting | Embed values in strings | `"Hello %s" % name` |
| `sorted()` | Sort without modifying original | `sorted(list, key=fn)` |
| `*args` | Accept variable arguments | `def f(*args)` |
| `__name__ == "__main__"` | Safe entry point | `if __name__ == "__main__"` |
| Lambda | Anonymous inline function | `lambda x: x * x` |
| Higher-Order Functions | Functions that take/return functions | `def apply(x, fn): return fn(x)` |
| `map()` | Transform every element | `map(fn, list)` |
| `filter()` | Keep elements passing a test | `filter(predicate, list)` |
| `reduce()` | Combine elements into one value | `reduce(fn, list)` |
| Closures | Functions that remember their environment | `def outer(): return inner` |
| `nonlocal` | Modify enclosing scope variable | `nonlocal var` |
| Partial Functions | Fix some args, create simpler function | `partial(fn, arg1)` |
| Recursion | Function calls itself | Base case + recursive case |
| Tail Recursion | Recursion with accumulator | `def f(acc, n)` |

---

*End of Document*
