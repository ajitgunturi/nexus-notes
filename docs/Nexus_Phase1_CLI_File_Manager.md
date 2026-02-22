# Introduction to Phase 1

Over the next 18 days you will build a fully functional command-line note manager in Python. You will go from zero programming knowledge to writing tested, version-controlled, well-structured code. By the end, Nexus will create, search, tag, and export notes with data persisted to disk, a proper test suite, and clean architecture.

Each day is a 3-hour session with three sections: Reading (concept theory with inline examples), Build (apply the concept to Nexus), and Review (checklist + quiz). Do not skip the quizzes — if you get a question wrong, reread the section before moving on.

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
> # starts a comment (ignored by Python). print() displays text. input() pauses for user input and returns a string. The f before a string creates an f-string where {name} is replaced by the variable’s value.

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

```
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

```
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

> **🧠 Quiz**
>
> **Q1: What does 'pwd' do?**
> A) Print working directory
> B) Prompt for password
> C) Power down
> D) Create file
> ✅ Answer: A — Print Working Directory.
>
> **Q2: What type does input() return?**
> A) int
> B) float
> C) str
> D) Depends on input
> ✅ Answer: C — Always a string.
>
> **Q3: What is 10 // 3?**
> A) 3.33
> B) 3
> C) 4
> D) 1
> ✅ Answer: B — Floor division rounds down.
>
> **Q4: Why use ALL_CAPS for VERSION?**
> A) Python requires it
> B) Convention: value should not change
> C) Makes it faster
> D) Required for f-strings
> ✅ Answer: B — Convention for constants.
>

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

> **🧠 Quiz**
>
> **Q1: What is: not (5 > 3 and 2 < 1)?**
> A) True
> B) False
> C) Error
> D) None
> ✅ Answer: A — 5>3=True, 2<1=False. True and False = False. not False = True.
>
> **Q2: What does range(2, 8, 2) produce?**
> A) 2, 4, 6
> B) 2, 4, 6, 8
> C) 2, 3, 4, 5, 6, 7
> D) 8, 6, 4, 2
> ✅ Answer: A — Start=2, stop before 8, step=2.
>
> **Q3: What happens with: while True: break?**
> A) Infinite loop
> B) Runs once, then exits
> C) Error
> D) Never enters the loop
> ✅ Answer: B — Enters loop, hits break immediately, exits.
>

# Day 3: Collections — Lists, Dicts, and Tuples

Today you learn to store multiple values. Nexus starts managing a list of notes.

## Reading: Lists

A list is an ordered, mutable (changeable) collection. Created with square brackets:

```
fruits = ["apple", "banana", "cherry"]
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]  # Can hold different types
empty = []
```

Items are accessed by index, starting from 0 (zero-based indexing):

```
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

```
coordinates = (42.36, -71.06)
coordinates[0]     # 42.36
# coordinates[0] = 50  # ERROR! Cannot modify tuples
```

Use tuples for data that should never change: coordinates, database rows, function return values.

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

> **🧠 Quiz**
>
> **Q1: What is [1,2,3][1:]?**
> A) [1, 2]
> B) [2, 3]
> C) [1]
> D) [2]
> ✅ Answer: B — Slice from index 1 to end.
>
> **Q2: What happens with dict['missing_key']?**
> A) Returns None
> B) Returns ''
> C) KeyError
> D) Creates the key
> ✅ Answer: C — KeyError. Use .get() for safe access.
>
> **Q3: Can you append to a tuple?**
> A) Yes
> B) No, tuples are immutable
> C) Only with .extend()
> D) Only numbers
> ✅ Answer: B — Tuples cannot be changed after creation.
>

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

> **🧠 Quiz**
>
> **Q1: What returns from a function with no return statement?**
> A) 0
> B) ""
> C) None
> D) Error
> ✅ Answer: C — Implicit return is None.
>
> **Q2: Why is def f(items=[]) dangerous?**
> A) Lists can't be params
> B) Same list shared across calls
> C) Syntax error
> D) Slower
> ✅ Answer: B — Default list created once, shared across all calls.
>

# Day 5: Git — Your Code’s Time Machine

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

```python
# .gitignore
__pycache__/
*.pyc
.env
.vscode/
venv/
.DS_Store
```

## Build: Initialize Nexus Repo

```python
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

> **🧠 Quiz**
>
> **Q1: What does 'git add' do?**
> A) Commits changes
> B) Stages files
> C) Pushes to GitHub
> D) Creates branch
> ✅ Answer: B — Stages files for the next commit.
>
> **Q2: What should NEVER be committed?**
> A) Source code
> B) .gitignore
> C) API keys and passwords
> D) README
> ✅ Answer: C — Secrets never go in version control.
>

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

> **🧠 Quiz**
>
> **Q1: json.dump() vs json.dumps()?**
> A) dump writes to file, dumps returns string
> B) They're identical
> C) dumps is safer
> D) dump is for lists only
> ✅ Answer: A
>
> **Q2: Why use 'with open()' instead of plain open()?**
> A) Faster
> B) Auto-closes even on errors
> C) Only way in Python 3
> D) Encrypts data
> ✅ Answer: B
>

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

Restructure into the package layout above. Add try/except around all user inputs. Validate all data in models.py. Commit the restructure separately from the error handling.

## Review: Days 7–8

**Daily Checklist**

- [ ] I understand try/except/finally
- [ ] I can catch specific exceptions and raise my own
- [ ] I know modules (.py files) vs packages (folders + __init__.py)
- [ ] Nexus is a proper package with validated inputs

> **🧠 Quiz**
>
> **Q1: In what order are except blocks checked?**
> A) Top to bottom (most specific first)
> B) Alphabetical
> C) Least specific first
> D) Random
> ✅ Answer: A — Put specific exceptions before general ones.
>
> **Q2: What makes a folder a Python package?**
> A) Having 3+ files
> B) __init__.py file
> C) Being in Python directory
> D) setup.py file
> ✅ Answer: B
>

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

## Build: Nexus Test Suite

Write tests for models.py (creation, validation, edge cases) and storage.py (save/load round-trip, missing file handling). Use tempfile for storage tests. Aim for at least 10 test functions.

## Review: Days 9–10

**Daily Checklist**

- [ ] I can install and run pytest
- [ ] I write test functions with assert
- [ ] I test exceptions with pytest.raises
- [ ] Nexus has tests for models and storage
- [ ] I run tests before every commit

> **🧠 Quiz**
>
> **Q1: What happens when assert evaluates to False?**
> A) Nothing
> B) Test marked as failed
> C) All tests stop
> D) Warning printed
> ✅ Answer: B — That test fails, others still run.
>

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

## Review: Days 11–14

**Daily Checklist**

- [ ] I understand classes, __init__, self, methods
- [ ] I know @classmethod, __repr__, _ convention
- [ ] I can use list comprehensions for filtering
- [ ] I built a CLI with argparse subcommands
- [ ] Nexus v1.0: add, list, search, delete via CLI

> **🧠 Quiz**
>
> **Q1: What does 'self' refer to?**
> A) The class
> B) The specific instance
> C) The parent class
> D) The module
> ✅ Answer: B — The specific object the method is called on.
>
> **Q2: What is a list comprehension?**
> A) Understanding lists
> B) Compact syntax to create lists from iterables
> C) A linked list type
> D) Sort method
> ✅ Answer: B — e.g., [x for x in items if condition]
>

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

> **🧠 Quiz**
>
> **Q1: What does @timer above a function do?**
> A) Times the import
> B) Wraps the function to add timing behavior
> C) Limits execution time
> D) Delays execution
> ✅ Answer: B — Decorator wraps the function.
>
> **Q2: What does yield do vs return?**
> A) They're the same
> B) yield produces a value and pauses, return exits
> C) yield is faster
> D) yield only works in classes
> ✅ Answer: B — yield pauses the function, saving state for next call.
>

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
