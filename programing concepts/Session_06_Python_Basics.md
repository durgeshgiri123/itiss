# Session 6 – Python Basics: Installation, Data Types & Functions
> **Syllabus:** 2T + 2L | Module B: Python

---

## 1. Installing Python

### Windows
1. Download from https://python.org
2. ✅ Check "Add Python to PATH" during install
3. Verify: `python --version`

### Linux
```bash
sudo apt update
sudo apt install python3 python3-pip
python3 --version
```

### Running Python
```bash
python3 script.py          # run a file
python3                    # interactive REPL
```

---

## 2. First Python Program

```python
# Hello World
print("Hello, World!")

# User input
name = input("Enter your name: ")
print("Hello,", name)
print(f"Hello, {name}!")    # f-string (preferred)
```

---

## 3. Native Data Types

```python
# Integer
age = 25
print(type(age))   # <class 'int'>

# Float
pi = 3.14159
print(type(pi))    # <class 'float'>

# String
name = "Python"
print(type(name))  # <class 'str'>

# Boolean
flag = True
print(type(flag))  # <class 'bool'>

# NoneType
x = None
print(type(x))     # <class 'NoneType'>

# Complex
c = 3 + 4j
print(type(c))     # <class 'complex'>
```

### Type Conversion

```python
int("42")        # → 42
float("3.14")    # → 3.14
str(100)         # → "100"
bool(0)          # → False
bool("")         # → False
bool("hello")    # → True
bool(None)       # → False
```

> ⚠️ **Falsy values in Python:** `0`, `0.0`, `""`, `[]`, `{}`, `()`, `None`, `False`

---

## 4. Indentation

Python uses **indentation (whitespace)** instead of `{}` braces to define code blocks. This is NOT optional — it's **syntax**.

```python
if True:
    print("Inside if")    # 4 spaces or 1 tab
    if True:
        print("Nested")   # 8 spaces
print("Outside if")       # no indent
```

> ⚠️ **Never mix tabs and spaces.** Use 4 spaces (PEP 8 standard). Mixing causes `IndentationError` or `TabError`.

---

## 5. Declaring Functions

```python
# Basic function
def greet():
    print("Hello!")

# Function with parameters
def add(a, b):
    return a + b

# Default parameter
def greet(name="World"):
    print(f"Hello, {name}!")

# Multiple return values
def min_max(lst):
    return min(lst), max(lst)

low, high = min_max([3, 1, 4, 1, 5])
```

---

## 6. Control Flow

```python
# if-elif-else
score = 75
if score >= 90:
    print("A")
elif score >= 75:
    print("B")
else:
    print("C")

# for loop
for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):     # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 10, 2): # 0, 2, 4, 6, 8 (step=2)
    print(i)

# while loop
count = 0
while count < 5:
    print(count)
    count += 1

# loop control
for i in range(10):
    if i == 3: continue   # skip 3
    if i == 7: break      # stop at 7
    print(i)
```

---

## 7. Overview of Objects

In Python, **everything is an object** — integers, strings, functions, classes.

```python
x = 42
print(id(x))         # memory address
print(type(x))       # <class 'int'>
print(dir(x))        # list all methods/attributes
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between `print(type(True))` and `print(type(1))`?**
`type(True)` → `<class 'bool'>`. `type(1)` → `<class 'int'>`. But `bool` is a **subclass** of `int` in Python — `isinstance(True, int)` returns `True`.

**Q2. What is the output of `bool("False")`?**
`True` — because `"False"` is a non-empty string. Only the empty string `""` is falsy.

**Q3. What does `range(5)` produce?**
`0, 1, 2, 3, 4` — range is zero-indexed and the stop value is exclusive.

**Q4. What is the difference between `==` and `is`?**
`==` checks value equality. `is` checks identity (same memory object). `1 == 1.0` is True. `1 is 1.0` is False.

**Q5. What will happen if you mix tabs and spaces in Python?**
`TabError: inconsistent use of tabs and spaces in indentation`. Python 3 does NOT allow mixing.

**Q6. What is the output of `type(None)`?**
`<class 'NoneType'>`

**Q7. Can Python functions return multiple values?**
Yes — they return a tuple. `a, b = func()` unpacks the tuple.

**Q8. What is the difference between `input()` and `raw_input()`?**
In Python 3, `raw_input()` does not exist — `input()` always returns a string. In Python 2, `input()` evaluated expressions.

**Q9. What is `id()` used for?**
Returns the memory address of an object. Used to check if two variables point to the same object.

**Q10. What is the output of `int("3.14")`?**
`ValueError` — `int()` cannot convert a float-string directly. Use `int(float("3.14"))` → `3`.

**Q11. What is the purpose of `__name__ == "__main__"`?**
Checks if the script is being run directly (not imported as a module). Best practice for scripts.

**Q12. What is the difference between a parameter and an argument?**
Parameter is the variable in the function definition. Argument is the actual value passed when calling the function.

---

## 📌 Quick Revision Checklist
- [ ] Python installation on Windows and Linux
- [ ] `print()`, `input()`, `type()`, `id()`, `dir()`
- [ ] Native types: int, float, str, bool, None, complex
- [ ] Falsy values list
- [ ] Type conversion functions
- [ ] Indentation rules (4 spaces, no tab mixing)
- [ ] Function definition with default parameters
- [ ] `range()` — start, stop, step
- [ ] for / while / break / continue
- [ ] `bool` is subclass of `int`
- [ ] `==` vs `is`
