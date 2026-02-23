# Phase 1: CLI File Manager

> **Phase 1 of 4** | Duration: 18 days (~54 hours) | Prerequisites: None

Over the next 18 days you will build a fully functional command-line note manager in Python. You will go from zero programming knowledge to writing tested, version-controlled, well-structured code. By the end, Nexus will create, search, tag, and export notes with data persisted to disk, a proper test suite, and clean architecture.

Each day is a 3-hour session with three sections: Reading (concept theory with inline examples), Build (apply the concept to Nexus), and Review (checklist + quiz). Do not skip the quizzes — if you get a question wrong, reread the section before moving on.

---

# Day 1: Your Development Environment

By the end of today you will have Python installed, a code editor ready, and your first program running.

## Reading: What Is Programming?

A program is a set of instructions written in a language a computer can execute. Python is an interpreted language — the Python interpreter reads your .py file line by line, top to bottom, and executes each instruction. If it hits an error, it stops and tells you what went wrong. This is different from compiled languages (C, Rust) where you must convert the entire file to machine code before running it.

Python was created by Guido van Rossum in 1991. Its syntax reads almost like English, making it an excellent first language. But do not be fooled — Python powers Instagram, Spotify, Dropbox, and most modern AI research.

## Reading: The Terminal

The terminal is a text-based interface to your operating system. Instead of clicking icons, you type commands. Every professional developer uses the terminal daily because it is faster for repetitive tasks, can be automated, and most servers only have a terminal.

On Windows, open PowerShell or install Windows Terminal. On macOS, open Terminal (Applications > Utilities). On Linux, use your default terminal emulator.

| Command                    | What It Does                    | Example         |
| -------------------------- | ------------------------------- | --------------- |
| cd <folder>                | Change directory                | cd Documents    |
| cd ..                      | Go up one level                 | cd ..           |
| ls (Mac/Linux) / dir (Win) | List files in current directory | ls              |
| mkdir <name>               | Make a new directory            | mkdir nexus     |
| pwd                        | Print working directory         | pwd             |
| python <file>              | Run a Python file               | python nexus.py |
| clear / cls                | Clear the terminal screen       | clear           |

## Reading: Installing Python

Go to python.org/downloads and download Python 3.12+. During Windows installation, CHECK the box “Add python.exe to PATH” — this is critical. After installing, open a NEW terminal and type:

```bash
python --version
# Should show: Python 3.12.x
```

> **💡 Mac Users**
>
> macOS ships with Python 2. After installing Python 3, you may need to type python3 instead of python. Try both and use whichever works.

## Reading: Installing VS Code

Download VS Code from code.visualstudio.com. After installing, open the Extensions panel (square icon in sidebar), search for "Python" by Microsoft, and install it. This gives you syntax highlighting, error detection, and a built-in terminal.

## Build: Your First Program

```bash
mkdir nexus
cd nexus
```

Open this folder in VS Code, create nexus.py, and type:

```python
# nexus.py - The beginning of something big
print("Welcome to Nexus!")
print("Version: 0.0.1")

name = input("What is your name? ")
print(f"Hello, {name}! Nexus is ready.")
```

Run it: python nexus.py

> **💡 Line-by-Line Breakdown**
>
> `#` starts a comment (ignored by Python). print() displays text. input() pauses for user input and returns a string. The f before a string creates an f-string where {name} is replaced by the variable’s value.

## Reading: Variables and Data Types

A variable is a name that refers to a value in memory. Python is dynamically typed — the type is inferred from the value.

```python
age = 25              # int - whole number
price = 19.99         # float - decimal number
name = "Nexus"        # str - text (string)
is_active = True      # bool - True or False
nothing = None        # NoneType - absence of value

# Check type:
print(type(age))      # <class 'int'>
```

### Arithmetic Operations

| Operator | Meaning                 | Example | Result   |
| -------- | ----------------------- | ------- | -------- |
| +        | Addition                | 10 + 3  | 13       |
| -        | Subtraction             | 10 - 3  | 7        |
| *        | Multiplication          | 10 * 3  | 30       |
| /        | Division (always float) | 10 / 3  | 3.333... |
| //       | Floor division (int)    | 10 // 3 | 3        |
| %        | Modulo (remainder)      | 10 % 3  | 1        |
| **       | Exponentiation          | 2 ** 10 | 1024     |

### String Operations

```python
"Hello" + " " + "World"     # "Hello World" (concatenation)
"ha" * 3                     # "hahaha" (repetition)
len("Nexus")                 # 5 (length)
"Python"[0]                  # "P" (first char, zero-indexed)
"Python"[-1]                 # "n" (last char)
"hello".upper()              # "HELLO"
"  spaced  ".strip()         # "spaced"
"a,b,c".split(",")           # ["a", "b", "c"]
", ".join(["a", "b", "c"])   # "a, b, c"
```

### Type Conversion

input() ALWAYS returns a string. To do math with it, you must convert:

```python
user_input = input("Number: ")  # e.g. "42" (a string!)
number = int(user_input)         # integer 42
decimal = float(user_input)      # float 42.0
back = str(number)               # string "42"
```

> **⚠️ Crash Alert**
>
> int("hello") will crash with ValueError. We will learn to handle this gracefully on Day 7.

## Build: Enhanced Nexus

```python
# nexus.py
VERSION = "0.0.1"    # ALL_CAPS = constant (convention)
APP_NAME = "Nexus"   # snake_case = variable (convention)

print(f"{APP_NAME} v{VERSION}")
print("=" * 40)
name = input("What is your name? ")
print(f"Hello, {name.strip().title()}!")

import sys
print(f"Python: {sys.version}")
```

## Review: Day 1

**Daily Checklist**

- [ ] I can navigate the terminal with cd, ls/dir, mkdir, pwd
- [ ] Python 3.12+ installed and verified
- [ ] VS Code installed with Python extension
- [ ] Created nexus/ folder and nexus.py
- [ ] I understand int, float, str, bool, None
- [ ] I know arithmetic and string operations
- [ ] I understand input() returns str and how to cast types
- [ ] I know f-strings, comments (#), naming conventions

### Quiz

**Q1: What does 'pwd' do?**

- A) Print working directory
- B) Prompt for password
- C) Power down
- D) Create file

<details><summary>Answer</summary>

**A** — Print Working Directory.

</details>

**Q2: What type does input() return?**

- A) int
- B) float
- C) str
- D) Depends on input

<details><summary>Answer</summary>

**C** — Always a string.

</details>

**Q3: What is 10 // 3?**

- A) 3.33
- B) 3
- C) 4
- D) 1

<details><summary>Answer</summary>

**B** — Floor division rounds down.

</details>

**Q4: Why use ALL_CAPS for VERSION?**

- A) Python requires it
- B) Convention: value should not change
- C) Makes it faster
- D) Required for f-strings

<details><summary>Answer</summary>

**B** — Convention for constants.

</details>


## Exercises

1. **Predict the output.** What does this snippet print?

```python
x = 10
y = 3
print(x / y)
print(x // y)
print("ha" * y)
print("Nexus"[1] + "Nexus"[-1])
```

<details><summary>Solution</summary>

```python
x = 10
y = 3
print(x / y)                         # 3.3333333333333335 — / always returns float
print(x // y)                        # 3 — floor division truncates decimal
print("ha" * y)                      # hahaha — string repetition
print("Nexus"[1] + "Nexus"[-1])      # "e" + "s" = "es" — indexing
```

</details>

2. **Input and f-strings.** Write a program that asks for the user's name and birth year, then prints their approximate age using an f-string. Use `int()` to convert the year.

```
Name: Alice
Birth year: 1995
Alice, you are approximately 31 years old.
```

<details><summary>Solution</summary>

```python
name = input("Name: ")
birth_year = int(input("Birth year: "))
age = 2026 - birth_year
print(f"{name}, you are approximately {age} years old.")
```

</details>

3. **Fix the bug.** The following code crashes. Identify why, then fix it so it prints a helpful message instead.

```python
user_input = "hello"
number = int(user_input)
print(f"Double: {number * 2}")
```

<details><summary>Solution</summary>

`int("hello")` raises a `ValueError` — "hello" is not a valid integer. Before Day 7 teaches `try/except`, use `.isdigit()` to guard the conversion:

```python
user_input = "hello"
if user_input.isdigit():
    number = int(user_input)
    print(f"Double: {number * 2}")
else:
    print(f"'{user_input}' is not a valid number.")
```

Output: `'hello' is not a valid number.`

</details>

## Further Reading

- [The Python Tutorial (official)](https://docs.python.org/3/tutorial/) — the definitive starting point, written by Python's creators.
- [Built-in Types Reference](https://docs.python.org/3/library/stdtypes.html) — complete documentation for `str`, `int`, `float`, `list`, `dict`, and more.

---

# Day 2: Control Flow — Making Decisions

Today your programs learn to think. You will master boolean logic, conditionals, and loops. Nexus gets an interactive menu.

## Reading: Boolean Expressions

| Operator | Meaning          | Example | Result |
| -------- | ---------------- | ------- | ------ |
| ==       | Equal to         | 5 == 5  | True   |
| !=       | Not equal to     | 5 != 3  | True   |
| >        | Greater than     | 10 > 5  | True   |
| <        | Less than        | 3 < 1   | False  |
| >=       | Greater or equal | 5 >= 5  | True   |
| <=       | Less or equal    | 7 <= 3  | False  |

> **⚠️ == vs =**
>
> Single = is assignment (name = "Alice"). Double == is comparison (name == "Alice"). Mixing these up is the #1 beginner mistake.

### Logical Operators

```python
age = 25; has_id = True
print(age >= 18 and has_id)   # True (BOTH must be True)
print(age < 18 or has_id)     # True (at least ONE True)
print(not has_id)              # False (flips the value)
```

## Reading: if / elif / else

Python uses indentation (4 spaces) to define code blocks. Other languages use curly braces {}. Inconsistent indentation causes IndentationError.

```python
temperature = 35

if temperature > 30:
    print("Hot!")
elif temperature > 20:
    print("Nice.")
else:
    print("Cold!")
```

Python checks conditions top-to-bottom. The FIRST True condition’s block runs; all others are skipped. If none are True, else runs.

## Reading: Loops

### while Loop

Repeats as long as a condition is True. If the condition never becomes False, you have an infinite loop (Ctrl+C to stop).

```python
count = 0
while count < 5:
    print(f"Count: {count}")
    count += 1    # Same as count = count + 1
```

### for Loop

Iterates over a sequence. range(n) generates 0 through n-1.

```python
for i in range(5):         # 0, 1, 2, 3, 4
    print(f"Step {i}")

for char in "Nexus":       # iterates each character
    print(char)

for i in range(2, 10, 3):  # start=2, stop=10, step=3 => 2, 5, 8
    print(i)
```

### break and continue

```python
for i in range(10):
    if i == 3: continue    # skip 3, go to next iteration
    if i == 7: break       # exit the loop entirely
    print(i)               # prints 0, 1, 2, 4, 5, 6
```

## Build: Nexus Gets a Menu

```python
# nexus.py
VERSION = "0.0.2"

def show_menu():
    print("\n--- Nexus Menu ---")
    print("1. Show version")
    print("2. Greet me")
    print("3. Count to N")
    print("4. Exit")

print(f"Welcome to Nexus v{VERSION}")

while True:
    show_menu()
    choice = input("\nChoice: ")

    if choice == "1":
        print(f"Version: {VERSION}")
    elif choice == "2":
        name = input("Name: ")
        print(f"Hello, {name}!")
    elif choice == "3":
        n = int(input("Count to: "))
        for i in range(1, n + 1):
            print(i, end=" ")
        print()
    elif choice == "4":
        print("Goodbye!")
        break
    else:
        print("Invalid choice.")
```

> **💡 What Is def?**
>
> def show_menu(): defines a function — a reusable block of code with a name. We cover functions deeply on Day 4. For now, know that def creates a named block and you call it with parentheses: show_menu().

## Review: Day 2

**Daily Checklist**

- [ ] I understand comparison operators (==, !=, >, <, >=, <=)
- [ ] I can combine conditions with and, or, not
- [ ] I understand if/elif/else and indentation-based blocks
- [ ] I can write while loops and for loops with range()
- [ ] I know what break and continue do
- [ ] Nexus has an interactive menu that loops until exit

### Quiz

**Q1: What is: not (5 > 3 and 2 < 1)?**

- A) True
- B) False
- C) Error
- D) None

<details><summary>Answer</summary>

**A** — 5>3=True, 2<1=False. True and False = False. not False = True.

</details>

**Q2: What does range(2, 8, 2) produce?**

- A) 2, 4, 6
- B) 2, 4, 6, 8
- C) 2, 3, 4, 5, 6, 7
- D) 8, 6, 4, 2

<details><summary>Answer</summary>

**A** — Start=2, stop before 8, step=2.

</details>

**Q3: What happens with: while True: break?**

- A) Infinite loop
- B) Runs once, then exits
- C) Error
- D) Never enters the loop

<details><summary>Answer</summary>

**B** — Enters loop, hits break immediately, exits.

</details>

**Q4: What is the output of `print(bool(0))`?**

- A) True
- B) False
- C) Error
- D) None

<details><summary>Answer</summary>

**B** — 0 is falsy. Other falsy values: `""`, `[]`, `{}`, `None`.

</details>

**Q5: Which keyword exits a loop immediately?**

- A) exit
- B) stop
- C) break
- D) return

<details><summary>Answer</summary>

**C** — `break` exits the nearest enclosing loop. `return` exits the entire function.

</details>


## Exercises

1. **Predict the output.** What does this print when `score = 75`?

```python
score = 75
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"
print(f"Score: {score}, Grade: {grade}")
```

<details><summary>Solution</summary>

`Score: 75, Grade: C` — Python checks top-to-bottom. `75 >= 70` is the first True condition; all remaining branches are skipped.

</details>

2. **Countdown.** Write a `while` loop that counts down from 10 to 1, then prints `"Blastoff!"`.

<details><summary>Solution</summary>

```python
count = 10
while count >= 1:
    print(count)
    count -= 1
print("Blastoff!")
```

</details>

3. **Even numbers only.** Write a `for` loop over `range(1, 21)` that prints only even numbers. Use `continue` to skip odd ones.

<details><summary>Solution</summary>

```python
for i in range(1, 21):
    if i % 2 != 0:
        continue
    print(i)
```

`i % 2 != 0` is True for odd numbers. `continue` jumps to the next iteration, so only even numbers reach `print()`.

</details>

## Further Reading

- [Python Control Flow (official docs)](https://docs.python.org/3/tutorial/controlflow.html) — covers if/elif/else, for loops, break, continue, and range() with examples from Python's creators.
- [Truthy and Falsy Values (Real Python)](https://realpython.com/python-boolean/) — explains why `bool(0)`, `bool([])`, `bool(None)`, and `bool("")` are all `False`, and when this matters in conditions.

---

# Day 3: Collections — Lists, Dicts, and Tuples

Today you learn to store multiple values. Nexus starts managing a list of notes.

## Reading: Lists

A list is an ordered, mutable (changeable) collection. Created with square brackets:

```python
fruits = ["apple", "banana", "cherry"]
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]  # Can hold different types
empty = []
```

Items are accessed by index, starting from 0 (zero-based indexing):

```python
fruits[0]      # "apple" (first)
fruits[-1]     # "cherry" (last)
fruits[1:3]    # ["banana", "cherry"] (slice: start inclusive, end exclusive)
```

| Operation     | What It Does         | Example                   |
| ------------- | -------------------- | ------------------------- |
| .append(x)    | Add x to end         | fruits.append("date")     |
| .insert(i, x) | Insert at index i    | fruits.insert(0, "fig")   |
| .remove(x)    | Remove first x       | fruits.remove("banana")   |
| .pop(i)       | Remove & return at i | last = fruits.pop()       |
| len(list)     | Count of items       | len(fruits)               |
| x in list     | Check membership     | "apple" in fruits  # True |
| .sort()       | Sort in place        | numbers.sort()            |

## Reading: Dictionaries

A dict stores key-value pairs. Keys must be unique and immutable (strings, numbers).

```python
note = {
    "title": "My first note",
    "content": "Hello world",
    "tags": ["personal", "important"],
}

print(note["title"])              # "My first note"
print(note.get("author", "N/A")) # "N/A" (safe access with default)
note["author"] = "Alice"         # Add new key
del note["tags"]                  # Delete key

for key, value in note.items():  # Iterate
    print(f"{key}: {value}")
```

> **💡 [] vs .get()**
>
> note["missing_key"] crashes with KeyError. note.get("missing_key", default) returns the default value safely. Use .get() when a key might not exist.

## Reading: Tuples

A tuple is like a list but immutable — once created, it cannot be changed. Use parentheses:

```python
coordinates = (42.36, -71.06)
coordinates[0]     # 42.36
# coordinates[0] = 50  # ERROR! Cannot modify tuples
```

Use tuples for data that should never change: coordinates, database rows, function return values.

## Reading: List Comprehensions

A list comprehension creates a new list by transforming and/or filtering an existing iterable — all in one expression.

**Syntax:**

```python
[expression for item in iterable if condition]
```

This is equivalent to:

```python
result = []
for item in iterable:
    if condition:
        result.append(expression)
```

**Example 1 — Square numbers:**

```python
squares = [x ** 2 for x in range(6)]
print(squares)  # [0, 1, 4, 9, 16, 25]
```

**Example 2 — Filter notes by tag:**

```python
notes = [
    {"title": "Meeting",  "tags": ["work"]},
    {"title": "Groceries","tags": ["personal"]},
    {"title": "Deadline", "tags": ["work", "urgent"]},
]
work_titles = [n["title"] for n in notes if "work" in n["tags"]]
print(work_titles)  # ['Meeting', 'Deadline']
```

> **💡 One-Liner Power**
>
> List comprehensions are powerful but readability comes first. If the expression or condition doesn't fit comfortably on one line, use a regular `for` loop instead. Compact is only good when it's still obvious.

## Build: Nexus Manages Notes

```python
# Add to nexus.py
from datetime import datetime
VERSION = "0.0.3"
notes = []  # In-memory storage

def add_note():
    title = input("Title: ")
    content = input("Content: ")
    note = {
        "id": len(notes) + 1,
        "title": title,
        "content": content,
        "created_at": datetime.now().isoformat(),
        "tags": []
    }
    notes.append(note)
    print(f"Note #{note['id']} created!")

def list_notes():
    if not notes:
        print("No notes yet.")
        return
    for note in notes:
        print(f"  [{note['id']}] {note['title']}")
```

## Review: Day 3

**Daily Checklist**

- [ ] I can create, access, and modify lists
- [ ] I understand zero-based indexing and slicing [start:stop]
- [ ] I can create dicts, access by key, iterate with .items()
- [ ] I know [] vs .get() for dict access
- [ ] I understand tuples and when to use them
- [ ] Nexus stores notes as dicts in a list

### Quiz

**Q1: What is [1,2,3][1:]?**

- A) [1, 2]
- B) [2, 3]
- C) [1]
- D) [2]

<details><summary>Answer</summary>

**B** — Slice from index 1 to end.

</details>

**Q2: What happens with dict['missing_key']?**

- A) Returns None
- B) Returns ''
- C) KeyError
- D) Creates the key

<details><summary>Answer</summary>

**C** — KeyError. Use .get() for safe access.

</details>

**Q3: Can you append to a tuple?**

- A) Yes
- B) No, tuples are immutable
- C) Only with .extend()
- D) Only numbers

<details><summary>Answer</summary>

**B** — Tuples cannot be changed after creation.

</details>

**Q4: What does `[x*2 for x in range(4)]` produce?**

- A) [0, 2, 4, 6]
- B) [2, 4, 6, 8]
- C) [1, 2, 3, 4]
- D) Error

<details><summary>Answer</summary>

**A** — `range(4)` gives 0, 1, 2, 3. Doubled: 0, 2, 4, 6.

</details>


## Exercises

1. **Comprehension filter.** Given `numbers = [3, 18, 7, 25, 2, 11, 9, 14]`, use a list comprehension to produce only the values greater than 10.

<details><summary>Solution</summary>

```python
numbers = [3, 18, 7, 25, 2, 11, 9, 14]
big = [n for n in numbers if n > 10]
print(big)  # [18, 25, 11, 14]
```

</details>

2. **Word lengths.** Given `"the quick brown fox"`, build a dict mapping each word to its length.

```
{'the': 3, 'quick': 5, 'brown': 5, 'fox': 3}
```

<details><summary>Solution</summary>

```python
sentence = "the quick brown fox"
word_lengths = {word: len(word) for word in sentence.split()}
print(word_lengths)
```

This is a **dict comprehension** — same idea, with `{key: value for ...}` syntax.

</details>

3. **Filter notes by tag.** Write `find_by_tag(notes, tag)` that returns a list of titles for notes containing the given tag.

```python
notes = [
    {"title": "Standup",      "tags": ["work", "daily"]},
    {"title": "Buy milk",     "tags": ["personal"]},
    {"title": "Sprint review","tags": ["work"]},
]
print(find_by_tag(notes, "work"))  # ['Standup', 'Sprint review']
```

<details><summary>Solution</summary>

```python
def find_by_tag(notes, tag):
    return [note["title"] for note in notes if tag in note["tags"]]
```

</details>

## Further Reading

- [Python Data Structures Tutorial](https://docs.python.org/3/tutorial/datastructures.html) — official guide to lists, list comprehensions, tuples, sets, and dicts.
- [Mapping Types — dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict) — complete reference for all dictionary methods.

---

# Day 4: Functions — Organizing Your Code

Today you learn to write reusable functions. Nexus gets refactored into clean, organized code.

## Reading: What Is a Function?

A function is a named block of code that performs a specific task. Functions exist for reusability (write once, use many times), abstraction (hide complexity behind a name), and testability (test in isolation).

```python
def greet(name):
    """Greet a user by name."""
    return f"Hello, {name}!"

message = greet("Alice")
print(message)  # "Hello, Alice!"
```

Anatomy: def starts the definition. The name uses snake_case. Parameters are variables in parentheses that receive values. The docstring (triple-quoted first line) describes the function. return sends a value back. No return means the function returns None.

### Parameters vs Arguments

Parameters are variable names in the definition. Arguments are actual values passed when calling.

```python
def add(a, b):        # a, b are PARAMETERS
    return a + b
result = add(3, 5)    # 3, 5 are ARGUMENTS
```

### Default Parameters

```python
def create_note(title, content="", tags=None):
    if tags is None:
        tags = []     # NEVER use tags=[] as default!
    return {"title": title, "content": content, "tags": tags}

note1 = create_note("Quick thought")
note2 = create_note("Meeting", content="Notes here")
note3 = create_note("Todo", tags=["urgent"])
```

> **⚠️ Mutable Default Gotcha**
>
> Never use a list or dict as a default parameter (e.g., def f(items=[])). Python creates the default ONCE at function definition. Every call that uses the default shares the SAME list. Use None and create a new list inside the function.

### Scope

Variables inside a function are local — they exist only within that function. Variables outside are global. A function can READ globals but cannot MODIFY them without the global keyword (avoid this).

```python
count = 0           # global
def increment():
    local_var = 10   # only exists here
    return count + 1 # can READ global
# print(local_var)  # ERROR! Does not exist outside
```

### *args and **kwargs

Sometimes you need a function that accepts a flexible number of arguments.

`*args` collects extra positional arguments into a **tuple**:

```python
def add_tags(note_title, *tags):
    print(f"Note: {note_title}, Tags: {tags}")

add_tags("Meeting", "work", "urgent", "q1")
# Note: Meeting, Tags: ('work', 'urgent', 'q1')
```

`**kwargs` collects extra keyword arguments into a **dict**:

```python
def create_record(title, **metadata):
    print(f"Title: {title}, Meta: {metadata}")

create_record("Sprint Notes", author="Alice", priority="high")
# Title: Sprint Notes, Meta: {'author': 'Alice', 'priority': 'high'}
```

You can use both together. Order must be: regular params → `*args` → `**kwargs`:

```python
def flexible(required, *args, **kwargs):
    print(required, args, kwargs)

flexible("hello", 1, 2, debug=True)
# hello (1, 2) {'debug': True}
```

> **💡 When Do You See These?**
>
> You'll encounter `*args` and `**kwargs` most often in library code and decorators (Day 15). The `wrapper(*args, **kwargs)` pattern inside a decorator means "accept whatever the original function accepts and pass it through unchanged."

## Build: Refactored Nexus

```python
# nexus.py - Refactored
from datetime import datetime
VERSION = "0.0.4"

def create_note_store():
    """Create an empty note store."""
    return []

def add_note(store, title, content, tags=None):
    """Add a note to the store."""
    if tags is None: tags = []
    note = {
        "id": len(store) + 1, "title": title,
        "content": content, "tags": tags,
        "created_at": datetime.now().isoformat(),
    }
    store.append(note)
    return note

def find_note(store, note_id):
    """Find a note by ID. Returns None if not found."""
    for note in store:
        if note["id"] == note_id: return note
    return None

def delete_note(store, note_id):
    """Delete a note by ID."""
    note = find_note(store, note_id)
    if note:
        store.remove(note)
        return True
    return False

def main():
    store = create_note_store()
    print(f"Nexus v{VERSION}")
    while True:
        print("\n1.Add  2.List  3.View  4.Delete  5.Exit")
        ch = input("Choice: ")
        if ch == "1":
            t = input("Title: "); c = input("Content: ")
            tags_in = input("Tags (comma-sep): ")
            tags = [t.strip() for t in tags_in.split(",") if t.strip()]
            note = add_note(store, t, c, tags)
            print(f"Created #{note['id']}")
        elif ch == "5":
            print("Goodbye!"); break

if __name__ == "__main__":
    main()
```

> **💡 if __name__ == "__main__"**
>
> When Python runs a file directly, __name__ is set to "__main__". When imported by another file, __name__ is the module name. This guard ensures main() only runs when executed directly, not when imported. Essential for testable code.

## Review: Day 4

**Daily Checklist**

- [ ] I can define functions with def, parameters, return
- [ ] I know parameters vs arguments
- [ ] I understand default parameters and the mutable default trap
- [ ] I understand variable scope (local vs global)
- [ ] I know docstrings and the __name__ guard
- [ ] Nexus is refactored into clean functions

### Quiz

**Q1: What returns from a function with no return statement?**

- A) 0
- B) ""
- C) None
- D) Error

<details><summary>Answer</summary>

**C** — Implicit return is None.

</details>

**Q2: Why is def f(items=[]) dangerous?**

- A) Lists can't be params
- B) Same list shared across calls
- C) Syntax error
- D) Slower

<details><summary>Answer</summary>

**B** — Default list created once, shared across all calls.

</details>

**Q3: What does `*args` give you inside a function?**

- A) A list
- B) A tuple
- C) A dict
- D) A set

<details><summary>Answer</summary>

**B** — `*args` collects extra positional arguments into a tuple.

</details>

**Q4: What is the scope of a variable defined inside a function?**

- A) Global
- B) Local only
- C) Module
- D) Session

<details><summary>Answer</summary>

**B** — Local only. It ceases to exist when the function returns.

</details>


## Exercises

1. **Word frequency counter.** Write `word_count(text)` that returns a dict mapping each lowercased word to how many times it appears.

```python
print(word_count("the cat sat on the mat"))
# {'the': 2, 'cat': 1, 'sat': 1, 'on': 1, 'mat': 1}
```

<details><summary>Solution</summary>

```python
def word_count(text):
    counts = {}
    for word in text.lower().split():
        counts[word] = counts.get(word, 0) + 1
    return counts
```

`counts.get(word, 0)` returns 0 if the word hasn't been seen yet, avoiding a `KeyError`.

</details>

2. **Tag truncator.** Write `truncate_tags(tags, max_tags=5)` that returns only the first `max_tags` items.

```python
print(truncate_tags(["a","b","c","d","e","f","g"]))       # ['a','b','c','d','e']
print(truncate_tags(["a","b","c","d","e","f","g"], max_tags=3)) # ['a','b','c']
print(truncate_tags(["a","b"]))                            # ['a','b']
```

<details><summary>Solution</summary>

```python
def truncate_tags(tags, max_tags=5):
    return tags[:max_tags]
```

Slicing beyond the list length is safe — Python returns whatever is there.

</details>

3. **Mutable default bug.** The function below has the mutable default bug. Show what goes wrong, then write the fixed version.

```python
def add_item(item, items=[]):
    items.append(item)
    return items
```

<details><summary>Solution</summary>

```python
# The bug:
print(add_item("a"))  # ['a']
print(add_item("b"))  # ['a', 'b'] — expected ['b']!
# Python creates [] once at definition. Every default call shares it.

# The fix:
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item("a"))  # ['a']
print(add_item("b"))  # ['b'] — fresh list each call
```

</details>

## Further Reading

- [Defining Functions (Python Docs)](https://docs.python.org/3/tutorial/controlflow.html#defining-functions) — official coverage of parameters, default values, keyword arguments, and `*args`/`**kwargs`.
- [Python Scope and the LEGB Rule (Real Python)](https://realpython.com/python-scope-legb-rule/) — deep dive into local, enclosing, global, and built-in scope with clear diagrams.

---

# Day 5: Git — Your Code's Time Machine

Today you learn Git. Your Nexus project becomes a version-controlled repository.

## Reading: Version Control

Git tracks every change to every file, letting you revert to any previous state and enabling collaboration. A Git repository (repo) is a folder whose history Git tracks. A commit is a snapshot of all files at a point in time, with a unique hash (like a1b2c3d), a message, and a pointer to the previous commit.

The model: Working Directory (your files) → Staging Area (changes prepared for commit) → Repository (committed history).

| Command             | What It Does                         |
| ------------------- | ------------------------------------ |
| git init            | Initialize a new repository          |
| git status          | Show modified/staged/untracked files |
| git add <file>      | Stage a file for next commit         |
| git add .           | Stage ALL changes                    |
| git commit -m "msg" | Create a commit with message         |
| git log --oneline   | Compact commit history               |
| git diff            | Show uncommitted changes             |
| git push            | Upload commits to remote (GitHub)    |

### Good Commit Messages

Use imperative mood, under 72 chars. Good: "Add note deletion feature". Bad: "fixed stuff", "WIP", "asdf".

### The .gitignore File

```text
# .gitignore
__pycache__/
*.pyc
.env
.vscode/
venv/
.DS_Store
```

## Build: Initialize Nexus Repo

```bash
git init
git add .gitignore
git commit -m "Add .gitignore"
git add nexus.py
git commit -m "Add Nexus CLI with note management"
git log --oneline
```

Create a GitHub repo called "nexus" (do NOT initialize with README), then:

```bash
git remote add origin https://github.com/YOUR_USERNAME/nexus.git
git branch -M main
git push -u origin main
```

From now on: commit after every meaningful change, push regularly.

## Review: Day 5

**Daily Checklist**

- [ ] I understand working directory / staging / repository model
- [ ] I can init a repo, add, commit, push
- [ ] I write good commit messages in imperative mood
- [ ] I created .gitignore and pushed to GitHub

### Quiz

**Q1: What does 'git add' do?**

- A) Commits changes
- B) Stages files
- C) Pushes to GitHub
- D) Creates branch

<details><summary>Answer</summary>

**B** — Stages files for the next commit.

</details>

**Q2: What should NEVER be committed?**

- A) Source code
- B) .gitignore
- C) API keys and passwords
- D) README

<details><summary>Answer</summary>

**C** — Secrets never go in version control.

</details>

**Q3: What does `git diff` show?**

- A) Committed changes
- B) Unstaged changes between working directory and last commit
- C) Differences between two branches
- D) Remote vs local differences

<details><summary>Answer</summary>

**B** — `git diff` shows changes not yet staged. Use `git diff --staged` to see staged changes.

</details>


## Exercises

1. **Two logical commits.** You changed these 5 files: `models.py` (added delete function), `cli.py` (added delete menu option), `storage.py` (fixed a save bug), `test_models.py` (added delete tests), `README.md` (updated feature list). Write git commands to commit them as two logical commits — one for the bug fix, one for the delete feature.

<details><summary>Solution</summary>

```bash
# Commit 1: the bug fix (isolated change)
git add storage.py
git commit -m "Fix save bug in storage module"

# Commit 2: the delete feature (related changes together)
git add models.py cli.py test_models.py README.md
git commit -m "Add note deletion feature with tests"
```

Group related changes into one commit. The bug fix is independent, so it gets its own.

</details>

2. **Good commit messages.** A developer wrote: `"I added the ability to delete notes and fixed a crash when title is empty"`. Rewrite this as two properly-formed commits.

<details><summary>Solution</summary>

```bash
git commit -m "Fix crash when note title is empty"
git commit -m "Add note deletion feature"
```

Rules applied: imperative mood, under 72 chars, one logical change per commit.

</details>

3. **Last 3 commits.** Write the command to show only the 3 most recent commits in oneline format.

<details><summary>Solution</summary>

```bash
git log --oneline -3
```

The `-3` flag limits output to the 3 most recent commits. Output looks like:
```
a1b2c3d Add note deletion feature
e4f5g6h Fix save bug in storage module
i7j8k9l Add persistent JSON storage for notes
```

</details>

## Further Reading

- [Pro Git Book (free online)](https://git-scm.com/book) — the comprehensive guide to Git, from basics to advanced branching.
- [GitHub Docs: Creating a Repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) — step-by-step guide to setting up a GitHub repo.

---

# Day 6: File I/O — Persistence

Nexus loses all notes when closed. Today you add file persistence with JSON.

## Reading: File Operations

Always use the with statement (context manager) which auto-closes the file even if an error occurs:

```python
# CORRECT - auto-closes
with open("data.txt", "r") as f:
    content = f.read()
# File closed here automatically
```

| Mode | Description                            | Creates File? |
| ---- | -------------------------------------- | ------------- |
| "r"  | Read only (default). Error if missing. | No            |
| "w"  | Write. Creates or OVERWRITES.          | Yes           |
| "a"  | Append to end.                         | Yes           |
| "r+" | Read and write.                        | No            |

> **⚠️ Write Mode Danger**
>
> Mode "w" ERASES the entire file before writing. Opening an existing file with "w" and writing one line destroys everything else. Always double-check your mode.

## Reading: JSON

JSON (JavaScript Object Notation) is a universal text format for structured data. It maps naturally to Python dicts and lists.

```python
import json

# Python -> JSON string
data = {"name": "Nexus", "version": "0.0.5"}
json_string = json.dumps(data, indent=2)

# JSON string -> Python
parsed = json.loads(json_string)

# Write to file
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# Read from file
with open("data.json", "r") as f:
    loaded = json.load(f)
```

> **💡 dump vs dumps**
>
> json.dump() writes to a FILE. json.dumps() returns a STRING. The 's' stands for 'string'. Same for load/loads.

## Build: Persistent Notes

Create storage.py:

```python
# storage.py
import json, os
DEFAULT_PATH = "nexus_data.json"

def save_notes(notes, path=DEFAULT_PATH):
    with open(path, "w") as f:
        json.dump(notes, f, indent=2)

def load_notes(path=DEFAULT_PATH):
    if not os.path.exists(path): return []
    with open(path, "r") as f:
        return json.load(f)
```

Update nexus.py: store = load_notes() at start, save_notes(store) after changes.

```python
git add storage.py nexus.py
git commit -m "Add persistent JSON storage for notes"
```

## Review: Day 6

**Daily Checklist**

- [ ] I use 'with' for all file operations
- [ ] I understand file modes and the danger of 'w'
- [ ] I can read/write JSON with json.dump/load
- [ ] Nexus persists notes to nexus_data.json
- [ ] I split code into modules and used import

### Quiz

**Q1: json.dump() vs json.dumps()?**

- A) dump writes to file, dumps returns string
- B) They're identical
- C) dumps is safer
- D) dump is for lists only

<details><summary>Answer</summary>

A

</details>

**Q2: Why use 'with open()' instead of plain open()?**

- A) Faster
- B) Auto-closes even on errors
- C) Only way in Python 3
- D) Encrypts data

<details><summary>Answer</summary>

B

</details>

**Q3: What is the difference between `json.load()` and `json.loads()`?**

- A) They're identical
- B) `load()` reads from a file object, `loads()` parses a string
- C) `loads()` is faster
- D) `load()` only works with lists

<details><summary>Answer</summary>

**B** — The `s` in `loads` stands for "string". Same pattern applies to `dump` vs `dumps`.

</details>


## Exercises

1. **Read and count.** Create a text file with 5 lines, then write a Python script that reads it and prints the number of lines and the longest line.

<details><summary>Solution</summary>

```python
# First, create the file:
with open("sample.txt", "w") as f:
    f.write("Hello world\nPython is great\nNexus\nFile I/O is easy\nDone\n")

# Then read and analyse:
with open("sample.txt", "r") as f:
    lines = f.readlines()

print(f"Lines: {len(lines)}")
longest = max(lines, key=lambda l: len(l.strip()))
print(f"Longest: {longest.strip()!r}")
```

</details>

2. **Round-trip JSON.** Write a script that saves a list of two note dicts to `notes.json`, then reads it back and prints the title of each note.

<details><summary>Solution</summary>

```python
import json

notes = [
    {"id": 1, "title": "Meeting", "tags": ["work"]},
    {"id": 2, "title": "Groceries", "tags": ["personal"]},
]

with open("notes.json", "w") as f:
    json.dump(notes, f, indent=2)

with open("notes.json", "r") as f:
    loaded = json.load(f)

for note in loaded:
    print(note["title"])
```

</details>

3. **Safe load.** Modify `load_notes()` from the Build section so that if the file contains invalid JSON (corrupted data), it prints a warning and returns an empty list instead of crashing.

<details><summary>Solution</summary>

```python
import json, os

def load_notes(path="nexus_data.json"):
    if not os.path.exists(path):
        return []
    try:
        with open(path, "r") as f:
            return json.load(f)
    except json.JSONDecodeError:
        print("Warning: notes file is corrupted. Starting fresh.")
        return []
```

`json.JSONDecodeError` is raised when the file exists but its contents aren't valid JSON.

</details>

## Further Reading

- [Reading and Writing Files (Python Docs)](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files) — official guide to file modes, `read()`, `readlines()`, and `with` context managers.
- [json — JSON encoder and decoder (Python Docs)](https://docs.python.org/3/library/json.html) — complete reference for `dump`, `load`, `dumps`, `loads`, and all serialization options including `indent` and `default`.

---

# Days 7–8: Error Handling and Modules

Learn to handle errors gracefully and organize code into proper Python packages. (6 hours total)

## Reading: Exceptions

When something goes wrong, Python raises an exception. Unhandled exceptions crash your program. try/except catches them:

```python
try:
    number = int(input("Enter a number: "))
    result = 100 / number
except ValueError:
    print("Not a valid number.")
except ZeroDivisionError:
    print("Cannot divide by zero.")
except Exception as e:
    print(f"Unexpected error: {e}")
finally:
    print("This always runs.")
```

Common exceptions: ValueError (bad conversion), TypeError (wrong type), KeyError (missing dict key), FileNotFoundError, IndexError (list index out of range), ImportError.

### Raising Exceptions

```python
def add_note(store, title, content):
    if not title.strip():
        raise ValueError("Title cannot be empty")
    if len(title) > 200:
        raise ValueError("Title too long")
```

## Reading: Modules and Packages

A module is a .py file. A package is a folder with __init__.py (can be empty). Structure Nexus as a package:

```python
nexus/
    nexus/
        __init__.py      # Makes folder a package
        cli.py           # Menu / user interaction
        storage.py       # File I/O
        models.py        # Note data structures
    main.py              # Entry point
    .gitignore
```

## Build: Robust Nexus Package

Restructure into the package layout above. Here is what each file should contain:

**`nexus/__init__.py`**

```python
# Makes the nexus/ folder a Python package. Can be empty.
```

**`nexus/models.py`**

```python
from datetime import datetime

def create_note(title, content="", tags=None):
    """Create a note dict. Raises ValueError on invalid input."""
    if not title or not title.strip():
        raise ValueError("Title cannot be empty")
    if len(title) > 200:
        raise ValueError("Title cannot exceed 200 characters")
    if tags is None:
        tags = []
    return {
        "id": None,  # assigned by storage
        "title": title.strip(),
        "content": content,
        "tags": tags,
        "created_at": datetime.now().isoformat(),
    }
```

**`nexus/storage.py`**

```python
import json

DEFAULT_PATH = "nexus_data.json"

def save_notes(notes, path=DEFAULT_PATH):
    try:
        with open(path, "w") as f:
            json.dump(notes, f, indent=2)
    except OSError as e:
        print(f"Error saving notes: {e}")

def load_notes(path=DEFAULT_PATH):
    try:
        with open(path, "r") as f:
            return json.load(f)
    except FileNotFoundError:
        return []          # first run — no file yet
    except json.JSONDecodeError:
        print("Warning: notes file is corrupted. Starting fresh.")
        return []
```

**`main.py`** (lives outside the `nexus/` package)

```python
from nexus.models import create_note
from nexus.storage import load_notes, save_notes

def main():
    store = load_notes()
    while True:
        print("\n1.Add  2.List  3.Exit")
        choice = input("Choice: ")
        if choice == "1":
            try:
                title = input("Title: ")
                note = create_note(title, input("Content: "))
                note["id"] = len(store) + 1
                store.append(note)
                save_notes(store)
                print(f"Created #{note['id']}")
            except ValueError as e:
                print(f"Invalid input: {e}")
        elif choice == "2":
            for n in store:
                print(f"  [{n['id']}] {n['title']}")
            if not store:
                print("No notes yet.")
        elif choice == "3":
            break

if __name__ == "__main__":
    main()
```

> **💡 Commit Strategy**
>
> Make two commits: first the restructure (`git add nexus/ main.py && git commit -m "Restructure Nexus into a Python package"`), then the validation and error handling (`git commit -m "Add input validation and error handling"`). Keeping structural changes separate from behavioral ones makes the history easier to review.

## Review: Days 7–8

**Daily Checklist**

- [ ] I understand try/except/finally
- [ ] I can catch specific exceptions and raise my own
- [ ] I know modules (.py files) vs packages (folders + __init__.py)
- [ ] Nexus is a proper package with validated inputs

### Quiz

**Q1: In what order are except blocks checked?**

- A) Top to bottom (most specific first)
- B) Alphabetical
- C) Least specific first
- D) Random

<details><summary>Answer</summary>

**A** — Put specific exceptions before general ones.

</details>

**Q2: What makes a folder a Python package?**

- A) Having 3+ files
- B) __init__.py file
- C) Being in Python directory
- D) setup.py file

<details><summary>Answer</summary>

B

</details>

**Q3: What does the `finally` block do?**

- A) Runs only on error
- B) Runs only on success
- C) Always runs
- D) Skips on KeyboardInterrupt

<details><summary>Answer</summary>

**C** — Always runs, even if an exception was raised or a `return` was hit inside `try/except`.

</details>

**Q4: What is the difference between `Exception` and `BaseException`?**

- A) They're identical
- B) `BaseException` includes `SystemExit` and `KeyboardInterrupt`
- C) `Exception` is for syntax errors
- D) `BaseException` is deprecated

<details><summary>Answer</summary>

**B** — `BaseException` is the root of all exceptions. Catching `Exception` is safer because it won't accidentally swallow Ctrl+C.

</details>


## Exercises

1. **Safe input with retry.** Write a loop that asks the user for an integer. If the input is invalid, print an error and ask again. Give up after 3 attempts.

<details><summary>Solution</summary>

```python
attempts = 3
while attempts > 0:
    try:
        value = int(input("Enter a number: "))
        print(f"You entered: {value}")
        break
    except ValueError:
        attempts -= 1
        if attempts > 0:
            print(f"Invalid. Try again. ({attempts} attempt(s) left)")
        else:
            print("Too many invalid attempts.")
```

</details>

2. **Custom exception.** Write a `NexusError` class that inherits from `Exception`, then a `validate_title(title)` function that raises it for empty input.

<details><summary>Solution</summary>

```python
class NexusError(Exception):
    pass

def validate_title(title):
    if not title or not title.strip():
        raise NexusError("Title cannot be empty")
    return title.strip()

try:
    validate_title("")
except NexusError as e:
    print(f"Caught: {e}")  # Caught: Title cannot be empty

print(validate_title("  Hello  "))  # Hello
```

</details>

3. **Fix the import.** You have `nexus/__init__.py` (empty) and `nexus/models.py` containing `def create_note(title): ...`. A user tries `from nexus.models import create_note` and gets an error. What are the three most likely causes?

<details><summary>Solution</summary>

1. **Wrong working directory** — you must run Python from the folder *containing* `nexus/`, not from inside it.
2. **`__init__.py` is missing** — its presence is what makes `nexus/` a package.
3. **Syntax error in `models.py`** — Python can't load the module if it has a syntax error.

An empty `__init__.py` is sufficient for `from nexus.models import create_note` to work. No content needed unless you want to re-export at the package level (`from nexus import create_note`).

</details>

## Further Reading

- [Errors and Exceptions (Python Docs)](https://docs.python.org/3/tutorial/errors.html) — covers built-in exceptions, try/except/else/finally, and how to define and raise custom exception classes.
- [Modules (Python Docs)](https://docs.python.org/3/tutorial/modules.html) — the definitive guide to `import`, `__init__.py`, the module search path, and package namespaces.

---

# Days 9–10: Testing — Proving Your Code Works

Learn pytest to write automated tests. Build a test suite for Nexus. (6 hours total)

## Reading: Why Test?

Without tests, every change could break something. Tests are automated checks that run in seconds. Professional practice: write code, write tests, run tests before every commit.

Unit tests verify a single function. Integration tests verify multiple components together. Install pytest: pip install pytest

## Reading: pytest

A test is a function named test_* in a file named test_*. pytest discovers and runs them.

```python
# test_models.py
import pytest
from nexus.models import create_note

def test_create_note_basic():
    note = create_note("Test", "Body")
    assert note["title"] == "Test"
    assert note["tags"] == []
    assert "created_at" in note

def test_empty_title_raises():
    with pytest.raises(ValueError):
        create_note("")

def test_strips_whitespace():
    note = create_note("  spaced  ")
    assert note["title"] == "spaced"
# Run tests:
pytest           # all tests
pytest -v        # verbose (shows each test name)
pytest -k "empty"  # tests matching keyword
```

## Reading: pytest Fixtures

A **fixture** is a function that runs setup code before a test and provides its return value to the test function automatically.

```python
import pytest

@pytest.fixture
def note_store():
    """Provide a fresh list with 2 notes for each test."""
    return [
        {"id": 1, "title": "First", "tags": ["work"]},
        {"id": 2, "title": "Second", "tags": ["personal"]},
    ]

def test_store_has_two_notes(note_store):
    assert len(note_store) == 2

def test_first_note_title(note_store):
    assert note_store[0]["title"] == "First"
```

Each test gets its own fresh copy — modifying `note_store` in one test does not affect another.

### The `tmp_path` Built-in Fixture

For file I/O tests, use `tmp_path` — a built-in pytest fixture that provides a temporary directory unique to each test run:

```python
import json

def test_save_and_load(tmp_path):
    filepath = tmp_path / "notes.json"
    data = [{"title": "Test note"}]

    with open(filepath, "w") as f:
        json.dump(data, f)

    with open(filepath, "r") as f:
        loaded = json.load(f)

    assert loaded == data
```

This never pollutes your real filesystem.

## Build: Nexus Test Suite

Write tests for models.py (creation, validation, edge cases) and storage.py (save/load round-trip, missing file handling). Use `tmp_path` for storage tests. Aim for at least 10 test functions.

## Review: Days 9–10

**Daily Checklist**

- [ ] I can install and run pytest
- [ ] I write test functions with assert
- [ ] I test exceptions with pytest.raises
- [ ] Nexus has tests for models and storage
- [ ] I run tests before every commit

### Quiz

**Q1: What happens when assert evaluates to False?**

- A) Nothing
- B) Test marked as failed
- C) All tests stop
- D) Warning printed

<details><summary>Answer</summary>

**B** — That test fails, others still run.

</details>

**Q2: What naming convention does pytest use to discover test functions?**

- A) `test_` prefix
- B) `_test` suffix
- C) Test class only
- D) Any name

<details><summary>Answer</summary>

**A** — Functions must start with `test_` (or classes with `Test`) for pytest to find them.

</details>

**Q3: What does `pytest -k "empty"` do?**

- A) Skips empty tests
- B) Runs only tests whose name contains "empty"
- C) Runs tests in empty files
- D) Deletes empty test files

<details><summary>Answer</summary>

**B** — The `-k` flag filters tests by keyword matched against their names.

</details>

**Q4: What is a pytest fixture?**

- A) Randomly generated test data
- B) Reusable setup code injected into test functions via parameters
- C) A replacement for assert statements
- D) A way to mock network calls only

<details><summary>Answer</summary>

**B** — Fixtures set up reusable state. pytest injects them automatically when a test function has a matching parameter name.

</details>


## Exercises

1. **Three tests for `word_count`.** Write test functions for: (a) a sentence with repeated words, (b) an empty string, (c) a string with punctuation.

<details><summary>Solution</summary>

```python
def word_count(text):
    counts = {}
    for word in text.lower().split():
        counts[word] = counts.get(word, 0) + 1
    return counts

def test_word_count_basic():
    assert word_count("the cat sat on the mat") == {
        "the": 2, "cat": 1, "sat": 1, "on": 1, "mat": 1
    }

def test_word_count_empty():
    assert word_count("") == {}

def test_word_count_punctuation():
    # split() doesn't strip punctuation, so "hello," ≠ "hello"
    result = word_count("hello, hello")
    assert result == {"hello,": 1, "hello": 1}
```

</details>

2. **Test a ValueError.** Write a test that verifies `create_note("")` raises `ValueError`. Use `pytest.raises`.

<details><summary>Solution</summary>

```python
import pytest

def create_note(title):
    if not title or not title.strip():
        raise ValueError("Title cannot be empty")
    return {"title": title.strip()}

def test_empty_title_raises():
    with pytest.raises(ValueError):
        create_note("")

def test_whitespace_title_raises():
    with pytest.raises(ValueError):
        create_note("   ")
```

</details>

3. **Fixture practice.** Write a `sample_store` fixture returning 2 note dicts, then write 2 tests that use it.

<details><summary>Solution</summary>

```python
import pytest

@pytest.fixture
def sample_store():
    return [
        {"id": 1, "title": "Meeting Notes", "tags": ["work"]},
        {"id": 2, "title": "Shopping List", "tags": ["personal"]},
    ]

def test_sample_store_count(sample_store):
    assert len(sample_store) == 2

def test_sample_store_has_title(sample_store):
    assert sample_store[0]["title"] == "Meeting Notes"
```

Each test receives an independent copy — pytest calls the fixture fresh for every test function.

</details>

## Further Reading

- [pytest documentation](https://docs.pytest.org/en/stable/) — official pytest docs with full fixture and assertion reference.
- [Python `unittest` docs](https://docs.python.org/3/library/unittest.html) — Python's built-in testing framework (pytest is preferred, but `unittest` is worth knowing).

---

# Days 11–14: OOP, Search, and CLI with argparse

Major milestone: classes, OOP, search functionality, and a professional CLI. (12 hours total)

## Reading: Classes and Objects

OOP models programs around objects — bundles of data (attributes) and behavior (methods). A class is the blueprint; an object is an instance.

```python
class Note:
    """Represents a note in Nexus."""
    def __init__(self, title, content="", tags=None):
        if not title.strip():
            raise ValueError("Title cannot be empty")
        self.title = title.strip()
        self.content = content
        self.tags = tags if tags is not None else []
        self.created_at = datetime.now().isoformat()

    def matches(self, query):
        """Check if note matches search query."""
        q = query.lower()
        return (q in self.title.lower() or
                q in self.content.lower() or
                any(q in t.lower() for t in self.tags))

    def to_dict(self):
        return {"title": self.title, "content": self.content,
                "tags": self.tags, "created_at": self.created_at}

    @classmethod
    def from_dict(cls, data):
        """Create Note from dictionary."""
        note = cls(data["title"], data.get("content", ""))
        note.tags = data.get("tags", [])
        note.created_at = data.get("created_at", "")
        return note

    def __repr__(self):
        return f"Note('{self.title}')"
```

Key concepts: __init__ is the constructor. self is the specific instance. @classmethod receives the class itself (cls). __repr__ defines how the object prints. Methods starting with _ are conventionally private.

## Reading: argparse

Real CLI tools use command-line arguments, not interactive menus. argparse builds these:

```python
import argparse
parser = argparse.ArgumentParser(description="Nexus")
sub = parser.add_subparsers(dest="command")

add_p = sub.add_parser("add", help="Add a note")
add_p.add_argument("title")
add_p.add_argument("--content", "-c", default="")
add_p.add_argument("--tags", "-t", default="")

sub.add_parser("list", help="List notes")

args = parser.parse_args()
# Usage: python main.py add "Meeting" --tags work,urgent
```

## Build: Nexus v1.0 — Full CLI

Wire the `NoteStore` class and `argparse` into a complete, runnable CLI. Update `main.py`:

```python
# main.py — Nexus v1.0
import argparse
from nexus.models import Note
from nexus.storage import load_notes, save_notes


class NoteStore:
    def __init__(self):
        self._notes = []
        self._next_id = 1

    def add(self, title, content="", tags=None):
        note = Note(self._next_id, title, content, tags)
        self._notes.append(note)
        self._next_id += 1
        return note

    def search(self, query):
        return [n for n in self._notes if n.matches(query)]

    def delete(self, note_id):
        note = next((n for n in self._notes if n.id == note_id), None)
        if note:
            self._notes.remove(note)
            return True
        return False


def build_parser():
    parser = argparse.ArgumentParser(description="Nexus — personal note manager")
    sub = parser.add_subparsers(dest="command", required=True)

    add_p = sub.add_parser("add", help="Add a note")
    add_p.add_argument("title")
    add_p.add_argument("--content", "-c", default="")
    add_p.add_argument("--tags", "-t", default="")

    sub.add_parser("list", help="List all notes")

    search_p = sub.add_parser("search", help="Search notes")
    search_p.add_argument("query")

    delete_p = sub.add_parser("delete", help="Delete a note")
    delete_p.add_argument("id", type=int)

    return parser


def main():
    args = build_parser().parse_args()
    data = load_notes()
    store = NoteStore()
    for d in data:
        note = Note(d["id"], d["title"], d.get("content", ""), d.get("tags", []))
        store._notes.append(note)
        store._next_id = max(store._next_id, d["id"] + 1)

    if args.command == "add":
        tags = [t.strip() for t in args.tags.split(",") if t.strip()]
        note = store.add(args.title, args.content, tags)
        save_notes([n.to_dict() for n in store._notes])
        print(f"Created #{note.id}: {note.title}")
    elif args.command == "list":
        for note in store._notes:
            tags = f" [{', '.join(note.tags)}]" if note.tags else ""
            print(f"  [{note.id}] {note.title}{tags}")
        if not store._notes:
            print("No notes yet.")
    elif args.command == "search":
        results = store.search(args.query)
        for note in results:
            print(f"  [{note.id}] {note.title}")
        if not results:
            print("No matches found.")
    elif args.command == "delete":
        if store.delete(args.id):
            save_notes([n.to_dict() for n in store._notes])
            print(f"Deleted #{args.id}")
        else:
            print(f"Note #{args.id} not found")


if __name__ == "__main__":
    main()
```

```bash
# Try it:
python main.py add "Sprint Notes" --content "Reviewed backlog" --tags work,urgent
python main.py list
python main.py search "sprint"
python main.py delete 1
```

```bash
git add nexus/ main.py
git commit -m "Add NoteStore class with OOP architecture"
git commit -m "Wire argparse CLI to NoteStore — Nexus v1.0"
```

## Review: Days 11–14

**Daily Checklist**

- [ ] I understand classes, __init__, self, methods
- [ ] I know @classmethod, __repr__, _ convention
- [ ] I can use list comprehensions for filtering
- [ ] I built a CLI with argparse subcommands
- [ ] Nexus v1.0: add, list, search, delete via CLI

### Quiz

**Q1: What does 'self' refer to?**

- A) The class
- B) The specific instance
- C) The parent class
- D) The module

<details><summary>Answer</summary>

**B** — The specific object the method is called on.

</details>

**Q2: What is a list comprehension?**

- A) Understanding lists
- B) Compact syntax to create lists from iterables
- C) A linked list type
- D) Sort method

<details><summary>Answer</summary>

**B** — e.g., [x for x in items if condition]

</details>

**Q3: What does `@classmethod` mean?**

- A) The method is private
- B) The method receives the class (`cls`) as first argument instead of an instance
- C) The method cannot be called on instances
- D) The method is static

<details><summary>Answer</summary>

**B** — `cls` is the class itself, not an instance. Used for alternative constructors like `from_dict()`.

</details>

**Q4: What does `__repr__` control?**

- A) How the object is saved to disk
- B) How the object appears when printed or in the REPL
- C) The object's string encoding
- D) The object's comparison behaviour

<details><summary>Answer</summary>

**B** — `__repr__` defines the developer-facing string representation, essential for debugging.

</details>


## Exercises

1. **`NoteStore` class.** Implement `NoteStore` with `add(title, content, tags)`, `find_by_id(note_id)`, `search(query)`, and `delete(note_id)` methods. Use the `Note` class from the reading.

<details><summary>Solution</summary>

```python
from datetime import datetime

class Note:
    def __init__(self, note_id, title, content="", tags=None):
        if not title.strip():
            raise ValueError("Title cannot be empty")
        self.id = note_id
        self.title = title.strip()
        self.content = content
        self.tags = tags if tags is not None else []
        self.created_at = datetime.now().isoformat()

    def matches(self, query):
        q = query.lower()
        return q in self.title.lower() or q in self.content.lower()

    def __repr__(self):
        return f"Note({self.id}, '{self.title}')"

class NoteStore:
    def __init__(self):
        self._notes = []
        self._next_id = 1

    def add(self, title, content="", tags=None):
        note = Note(self._next_id, title, content, tags)
        self._notes.append(note)
        self._next_id += 1
        return note

    def find_by_id(self, note_id):
        for note in self._notes:
            if note.id == note_id:
                return note
        return None

    def search(self, query):
        return [n for n in self._notes if n.matches(query)]

    def delete(self, note_id):
        note = self.find_by_id(note_id)
        if note:
            self._notes.remove(note)
            return True
        return False
```

</details>

2. **argparse parsing.** Write argparse code for `nexus add "Title" --content "..." --tags work,urgent` that prints the parsed title, content, and tags list.

<details><summary>Solution</summary>

```python
import argparse

parser = argparse.ArgumentParser(description="Nexus")
sub = parser.add_subparsers(dest="command")

add_p = sub.add_parser("add", help="Add a note")
add_p.add_argument("title")
add_p.add_argument("--content", "-c", default="")
add_p.add_argument("--tags", "-t", default="")

args = parser.parse_args()

if args.command == "add":
    tags = [t.strip() for t in args.tags.split(",") if t.strip()]
    print(f"Title:   {args.title}")
    print(f"Content: {args.content}")
    print(f"Tags:    {tags}")
```

Run: `python main.py add "Sprint Notes" --content "Review progress" --tags work,urgent`

</details>

3. **Filter objects by tag.** Given a list of `Note` objects, write a list comprehension returning only those tagged `"urgent"`.

<details><summary>Solution</summary>

```python
urgent_notes = [note for note in notes if "urgent" in note.tags]
```

</details>

## Further Reading

- [Classes (Python Docs)](https://docs.python.org/3/tutorial/classes.html) — the official guide to `__init__`, instance methods, class methods, inheritance, and `__repr__`.
- [argparse Tutorial (Python Docs)](https://docs.python.org/3/howto/argparse.html) — step-by-step guide to building CLIs with subcommands, positional arguments, and optional flags.

---

# Days 15–18: Decorators, Generators, and Virtual Environments

Intermediate Python: decorators, generators, venvs, dependency management. (12 hours total)

## Reading: Decorators

A decorator wraps a function to add behavior without modifying the original:

```python
import time
def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def search_notes(store, query):
    return store.search(query)
# Equivalent to: search_notes = timer(search_notes)
```

## Reading: Generators

A generator produces values one at a time with yield, not all at once. Memory-efficient for large data:

```python
def read_large_file(path):
    with open(path) as f:
        for line in f:
            yield line.strip()  # yields one line at a time

for line in read_large_file("big.txt"):
    process(line)  # only one line in memory
```

## Reading: *args and **kwargs in Decorators

You learned `*args` and `**kwargs` on Day 4. Here is why decorators depend on them.

Without `*args`/`**kwargs`, a decorator wrapper would only work for functions with one specific signature. With them, a single decorator works on **any** function:

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):       # accept whatever the original accepts
        start = time.time()
        result = func(*args, **kwargs)  # pass everything through unchanged
        print(f"{func.__name__} took {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def search(store, query, case_sensitive=False):
    return [n for n in store if query in n["title"]]

@timer
def save(path, data):
    pass  # same decorator works on any signature
```

The `wrapper(*args, **kwargs)` pattern is the standard way to write decorators that don't interfere with the wrapped function's arguments.

## Reading: Virtual Environments

A venv isolates your project’s dependencies from the system. Without it, projects share packages and version conflicts arise.

```bash
python -m venv venv           # Create
source venv/bin/activate      # Activate (Mac/Linux)
venv\Scripts\activate        # Activate (Windows)
pip install pytest             # Install into venv only
pip freeze > requirements.txt  # Save dependencies
pip install -r requirements.txt # Reproduce environment
deactivate                     # Exit venv
```

> **⚠️ Non-Negotiable**
>
> From today forward, EVERY Python project starts with a virtual environment. Add venv/ to .gitignore. Commit requirements.txt. This is professional practice.

## Build: Enhanced Nexus

Set up venv and requirements.txt. Add a @timer decorator to search. Add export commands (CSV, Markdown). Add logging with Python’s logging module.

## Review: Days 15–18

**Daily Checklist**

- [ ] I understand decorators and can write simple ones
- [ ] I understand generators and yield vs return
- [ ] I always use virtual environments
- [ ] I manage dependencies with pip freeze and requirements.txt
- [ ] I know *args and **kwargs
- [ ] Nexus has logging, export features, proper venv setup

### Quiz

**Q1: What does @timer above a function do?**

- A) Times the import
- B) Wraps the function to add timing behavior
- C) Limits execution time
- D) Delays execution

<details><summary>Answer</summary>

**B** — Decorator wraps the function.

</details>

**Q2: What does yield do vs return?**

- A) They're the same
- B) yield produces a value and pauses, return exits
- C) yield is faster
- D) yield only works in classes

<details><summary>Answer</summary>

**B** — yield pauses the function, saving state for next call.

</details>

**Q3: What does `pip freeze` output?**

- A) Installed package names only
- B) Package names with pinned versions
- C) Package source code
- D) pip version

<details><summary>Answer</summary>

**B** — Names with pinned versions (e.g. `pytest==8.2.2`). Used to reproduce environments exactly via `requirements.txt`.

</details>

**Q4: Which correctly accepts any keyword arguments?**

- A) `def f(*args)`
- B) `def f(**kwargs)`
- C) `def f(args={})`
- D) `def f(kwargs=[])`

<details><summary>Answer</summary>

**B** — `**kwargs` collects keyword arguments into a dict. `*args` collects positional arguments into a tuple.

</details>


## Exercises

1. **Retry decorator.** Write a `retry(times=3)` decorator factory that calls the decorated function up to `times` times if it raises an exception. Re-raise on the final failure.

<details><summary>Solution</summary>

```python
def retry(times=3):
    def decorator(func):
        def wrapper(*args, **kwargs):
            last_exc = None
            for attempt in range(1, times + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    last_exc = e
                    print(f"Attempt {attempt} failed: {e}")
            raise last_exc
        return wrapper
    return decorator

@retry(times=3)
def flaky():
    import random
    if random.random() < 0.7:
        raise ConnectionError("Timeout")
    return "OK"
```

`retry(times=3)` is a decorator *factory* — it returns the actual decorator. Three nested functions: factory → decorator → wrapper.

</details>

2. **Batch generator.** Write `note_chunks(notes, size)` that yields sublists of `size` items.

```python
for chunk in note_chunks(["n1","n2","n3","n4","n5"], 2):
    print(chunk)
# ['n1', 'n2']
# ['n3', 'n4']
# ['n5']
```

<details><summary>Solution</summary>

```python
def note_chunks(notes, size):
    for i in range(0, len(notes), size):
        yield notes[i:i + size]
```

`range(0, len(notes), size)` steps through in increments of `size`. The slice grabs each batch.

</details>

3. **Virtual environment setup.** Write the exact commands to create a venv, install `black` and `pytest`, and save `requirements.txt`.

<details><summary>Solution</summary>

```bash
python -m venv venv
source venv/bin/activate          # Mac/Linux
# venv\Scripts\activate           # Windows

pip install black pytest
pip freeze > requirements.txt
```

`pip freeze` outputs every installed package (including transitive dependencies) with pinned versions. To reproduce later: `pip install -r requirements.txt`.

</details>

## Further Reading

- [Python Decorators (Real Python)](https://realpython.com/primer-on-python-decorators/) — thorough walkthrough with practical examples.
- [Python Generators (Real Python)](https://realpython.com/introduction-to-python-generators/) — how `yield` works under the hood.
- [Python Virtual Environments Primer](https://realpython.com/python-virtual-environments-a-primer/) — why venvs matter and how to manage them.

---

# Phase 1 Complete: What You Have Built

Congratulations. You now have a working Python application with the following:

**Daily Checklist**

- [ ] A CLI note manager with add, list, search, view, delete, export
- [ ] Persistent JSON storage that survives restarts
- [ ] Clean OOP architecture with Note and NoteStore classes
- [ ] A proper Python package structure
- [ ] Comprehensive pytest test suite
- [ ] Git version control with meaningful commit history on GitHub
- [ ] Virtual environment with documented dependencies
- [ ] Professional CLI using argparse

Your GitHub repo should have 20–40 commits by now. Each one tells the story of your learning journey.

> **📚 Concepts to Deepen on Your Own**
>
> • Python data model and dunder methods (__eq__, __hash__, __len__)
> • Iterators and the iterator protocol (__iter__, __next__)
> • Context managers (__enter__, __exit__) and writing your own
> • Python’s garbage collector and reference counting
> • Closures and lexical scoping
> • Typing module and type hints (from typing import List, Optional)
> • Dataclasses (@dataclass) as an alternative to manual __init__
> • Pathlib for modern file path handling
> • Regular expressions (re module) for pattern matching
> • Comprehension variants: dict comprehensions, set comprehensions
> • The collections module: defaultdict, Counter, namedtuple
> • Recursion and when to use it vs iteration
> • Big-O notation and algorithmic complexity basics
> • PEP 8 style guide and linting with flake8 or ruff
