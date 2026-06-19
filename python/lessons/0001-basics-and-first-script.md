# Lesson 1: Python Basics & Your First Script

**Lesson 1** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Python is the Swiss Army knife of programming — automation, scripts, APIs, data processing. This lesson gets you writing real code from the first line.

## What You'll Learn

- Run Python code
- Variables, data types, and basic I/O
- Conditional logic and loops
- Write and execute a Python script

## Setup

Check if Python is installed:

```bash
python --version
python3 --version
```

If not, download from [python.org](https://www.python.org/downloads/). On Windows, check "Add Python to PATH" during install.

Open an interactive Python shell:

```bash
python
>>> print("hello world")
hello world
>>> exit()
```

Or run your first script — create `hello.py`:

```python
print("hello world")
name = input("What is your name? ")
print(f"Nice to meet you, {name}!")
```

Run it:

```bash
python hello.py
```

## Variables & Data Types

```python
# Numbers
age = 30
price = 19.99
count = -5

# Strings
name = "Alice"
greeting = 'Hello'
multi_line = """This spans
multiple lines"""

# Booleans
is_active = True
is_admin = False

# None (null)
result = None

# Check types
print(type(age))       # <class 'int'>
print(type(price))     # <class 'float'>
print(type(name))      # <class 'str'>
```

> [!TIP] Python is dynamically typed — you don't need to declare types. But `type()` is useful for debugging.

## Basic Operations

```python
# Arithmetic
sum = 10 + 5
diff = 10 - 5
product = 10 * 5
quotient = 10 / 5        # 2.0 (float)
integer_div = 10 // 3    # 3 (integer division)
remainder = 10 % 3       # 1
power = 2 ** 3           # 8

# String operations
full = "Hello" + " " + "World"   # Concatenation
repeat = "Ha" * 3                # "HaHaHa"
length = len("hello")            # 5

# Comparisons
print(10 > 5)    # True
print(10 == 5)   # False
print(10 != 5)   # True
```

## Lists & Dictionaries

```python
# Lists (ordered, mutable)
fruits = ["apple", "banana", "cherry"]
fruits.append("date")
fruits[0]          # "apple"
fruits[-1]         # "date"
fruits[1:3]        # ["banana", "cherry"]
len(fruits)        # 4

for fruit in fruits:
    print(fruit)

# Dictionaries (key-value pairs)
person = {
    "name": "Alice",
    "age": 30,
    "skills": ["Python", "SQL"]
}
print(person["name"])           # "Alice"
person["city"] = "New York"     # Add key
for key, value in person.items():
    print(f"{key}: {value}")
```

## Conditional Logic

```python
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"

print(f"Grade: {grade}")
```

## Loops

```python
# For loop over range
for i in range(5):
    print(i)          # 0, 1, 2, 3, 4

# While loop
count = 0
while count < 3:
    print(count)
    count += 1

# Loop with enumerate (get index + value)
for idx, fruit in enumerate(fruits):
    print(f"{idx}: {fruit}")
```

## Functions

```python
def greet(name, greeting="Hello"):
    """Return a greeting string."""
    return f"{greeting}, {name}!"

def add_all(*numbers):
    """Sum any number of arguments."""
    return sum(numbers)

print(greet("Alice"))                 # "Hello, Alice!"
print(greet("Bob", "Hi"))             # "Hi, Bob!"
print(add_all(1, 2, 3, 4))           # 10
```

## Hands-On: Build a Todo Script

Create `todo.py`:

```python
#!/usr/bin/env python3

tasks = []

while True:
    print("\n--- TODO ---")
    for i, task in enumerate(tasks, 1):
        print(f"{i}. {task}")
    print("(a)dd  (r)emove  (q)uit")

    choice = input("> ").lower()

    if choice == "a":
        task = input("Task: ")
        tasks.append(task)
    elif choice == "r":
        num = int(input("Number: ")) - 1
        if 0 <= num < len(tasks):
            removed = tasks.pop(num)
            print(f"Removed: {removed}")
    elif choice == "q":
        break
```

Run it: `python todo.py`

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the difference between a list and a dictionary?</summary>

A list stores ordered items by index (`[0, 1, 2]`). A dictionary stores key-value pairs (`{"name": "Alice"}`). Use lists for sequences, dicts for labeled data.
</details>

<details>
<summary><strong>2.</strong> How do you write a function in Python?</summary>

`def function_name(param1, param2):` — the body is indented. Use `return` to send back a value. Functions without `return` return `None`.
</details>

<details>
<summary><strong>3.</strong> What does <code>range(5)</code> produce?</summary>

`range(0, 5)` — numbers 0 through 4 (5 items total). Often used in `for` loops.
</details>

## Next Steps

- Modify the todo script to save tasks to a file
- Try the exercises from [Python Tutorial](https://docs.python.org/3/tutorial/)
- Move to Lesson 2: File I/O & Error Handling (coming next)

> [!NOTE] Ask followup questions!
> Code not running? Confused about a concept? Ask your teacher.

---

*Reference: [Python Official Tutorial](https://docs.python.org/3/tutorial/)*
