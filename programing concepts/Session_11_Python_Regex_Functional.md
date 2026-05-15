# Session 11 – Python: Regular Expressions & Functional Programming
> **Syllabus:** 2T + 4L + 2SL | Module B: Python

---

## 1. Regular Expressions (re module)

A **regular expression (regex)** is a pattern used to match, search, or manipulate strings.

```python
import re
```

### Core Functions

| Function | Use | Returns |
|----------|-----|---------|
| `re.match(pattern, s)` | Match at **start** of string | Match object or None |
| `re.search(pattern, s)` | Search **anywhere** in string | Match object or None |
| `re.findall(pattern, s)` | Find **all** matches | List of strings |
| `re.finditer(pattern, s)` | Find all matches with position | Iterator of match objects |
| `re.sub(pattern, repl, s)` | **Replace** matches | New string |
| `re.split(pattern, s)` | **Split** by pattern | List |
| `re.compile(pattern)` | Compile for reuse | Pattern object |

---

### Regex Metacharacters

| Symbol | Meaning | Example | Matches |
|--------|---------|---------|---------|
| `.` | Any char except newline | `a.c` | "abc", "a1c" |
| `^` | Start of string | `^hello` | "hello world" |
| `$` | End of string | `world$` | "hello world" |
| `*` | 0 or more | `ab*` | "a", "ab", "abbb" |
| `+` | 1 or more | `ab+` | "ab", "abbb" (NOT "a") |
| `?` | 0 or 1 (optional) | `colou?r` | "color", "colour" |
| `{n}` | Exactly n times | `a{3}` | "aaa" |
| `{n,m}` | n to m times | `a{2,4}` | "aa","aaa","aaaa" |
| `[]` | Character class | `[abc]` | "a", "b", or "c" |
| `[^]` | Negated class | `[^abc]` | anything except a,b,c |
| `\|` | OR | `cat\|dog` | "cat" or "dog" |
| `()` | Group | `(ab)+` | "ab", "abab" |
| `\` | Escape | `\.` | literal dot |

### Shorthand Classes

| Symbol | Matches | Opposite |
|--------|---------|---------|
| `\d` | Digit [0-9] | `\D` non-digit |
| `\w` | Word char [a-zA-Z0-9_] | `\W` non-word |
| `\s` | Whitespace [ \t\n\r] | `\S` non-whitespace |
| `\b` | Word boundary | `\B` non-boundary |

---

### Examples

```python
import re

# Search for pattern
result = re.search(r"\d+", "Order #12345 placed")
print(result.group())   # "12345"

# Find all numbers
re.findall(r"\d+", "a1 b22 c333")   # ['1', '22', '333']

# Match email
pattern = r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"
re.search(pattern, "user@example.com")

# Match phone: 10 digits
re.match(r"^\d{10}$", "9876543210")

# Match any single char except newline
re.findall(r".", "ab\nc")   # ['a', 'b', 'c'] — . skips \n

# Match whitespace
re.findall(r"\s+", "hello   world\ttab")   # ['   ', '\t']

# Substitute
re.sub(r"\s+", "-", "hello world")    # "hello-world"

# Split on non-alphanumeric
re.split(r"\W+", "one,two;three four")   # ['one','two','three','four']
```

### Flags

```python
re.IGNORECASE  # or re.I — case insensitive
re.MULTILINE   # ^ and $ match each line
re.DOTALL      # . matches newline too

re.search(r"hello", "HELLO World", re.I)
```

---

## 2. Scripting in Python

```python
#!/usr/bin/env python3
# Script with command-line arguments
import sys

def main():
    if len(sys.argv) < 2:
        print("Usage: script.py <filename>")
        sys.exit(1)
    filename = sys.argv[1]
    print(f"Processing: {filename}")

if __name__ == "__main__":
    main()
```

---

## 3. Functional Programming

### First-Class Functions
Functions are objects — they can be assigned, passed, and returned.

```python
def square(x): return x * x
f = square          # assign
f(5)                # 25

def apply(func, value):
    return func(value)
apply(square, 4)    # 16
```

### Lambda Functions (Anonymous Functions)

```python
square = lambda x: x**2
add    = lambda x, y: x + y
greet  = lambda name: f"Hello, {name}"

# Used inline
sorted(students, key=lambda s: s["score"])
```

> ⚠️ Lambda is for **simple one-liners**. Complex logic → use `def`.

### map(), filter(), reduce()

```python
# map — apply function to each element
list(map(lambda x: x**2, [1,2,3,4]))     # [1,4,9,16]
list(map(str, [1,2,3]))                   # ['1','2','3']

# filter — keep elements where function returns True
list(filter(lambda x: x%2==0, range(10))) # [0,2,4,6,8]

# reduce — accumulate to single value
from functools import reduce
reduce(lambda a, b: a*b, [1,2,3,4,5])    # 120 (5 factorial)
```

### Generators

```python
# Generator function (lazy evaluation)
def count_up(n):
    for i in range(n):
        yield i

gen = count_up(3)
next(gen)   # 0
next(gen)   # 1
next(gen)   # 2
next(gen)   # StopIteration

# Generator expression (like list comp but lazy)
gen = (x**2 for x in range(1000000))   # memory efficient
```

### Decorators

```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Done")
        return result
    return wrapper

@logger
def add(a, b):
    return a + b

add(2, 3)   # prints log, returns 5
```

---

## 4. Self-Learning: Regex Problems

```python
import re

# String validation — check valid email
def valid_email(email):
    return bool(re.match(r"^[\w.+-]+@[\w-]+\.[a-z]{2,}$", email, re.I))

# Remove leading zeros from IP address
def clean_ip(ip):
    return ".".join(str(int(p)) for p in ip.split("."))
# "192.068.001.005" → "192.68.1.5"

# CamelCase to snake_case
def camel_to_snake(s):
    return re.sub(r"(?<!^)(?=[A-Z])", "_", s).lower()
# "CamelCase" → "camel_case"

# Split log file by timestamp pattern
log = "2024-01-01 ERROR: fail\n2024-01-02 INFO: ok"
parts = re.split(r"\d{4}-\d{2}-\d{2} ", log)
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between `re.match()` and `re.search()`?**
`match()` only checks at the **beginning** of the string. `search()` scans through the **entire** string for a match.

**Q2. What does `.` match in regex?**
Any character **except newline** (`\n`). To match newline too, use `re.DOTALL` flag.

**Q3. What is the difference between `*` and `+` in regex?**
`*` matches zero or more occurrences. `+` matches one or more. `ab*` matches "a"; `ab+` does NOT match "a".

**Q4. What does `^` mean inside `[]` vs outside?**
Inside `[^abc]` = NOT those characters (negated class). Outside `^abc` = start of string.

**Q5. What does `re.findall()` return?**
A list of all non-overlapping matches. If the pattern has groups `()`, returns list of group tuples.

**Q6. What is `\b` in regex?**
Word boundary — matches the position between a word character (`\w`) and a non-word character. `\bcat\b` matches "cat" in "the cat sat" but NOT in "concatenate".

**Q7. What is a lambda function? When should you avoid it?**
An anonymous one-line function. Avoid when the logic is complex, needs debugging, or needs documentation. Use `def` for readability.

**Q8. What is the difference between `map()` and a list comprehension?**
Both apply a function to each element. List comprehension is generally more Pythonic and readable. `map()` returns a lazy iterator (memory efficient for large data).

**Q9. What is a generator? Why use it over a list?**
A generator yields values one at a time (lazy), using O(1) memory regardless of size. A list stores all values in memory at once.

**Q10. What is `reduce()` from functools?**
Applies a function cumulatively to a sequence, reducing it to a single value. Example: `reduce(lambda a,b: a+b, [1,2,3,4])` = 10.

**Q11. What does `re.sub(r"\s+", " ", text)` do?**
Replaces one or more consecutive whitespace characters (spaces, tabs, newlines) with a single space — useful for normalizing text.

---

## 📌 Quick Revision Checklist
- [ ] `re.match()` vs `re.search()` vs `re.findall()`
- [ ] `.`, `^`, `$`, `*`, `+`, `?`, `{n,m}`, `[]`, `|`
- [ ] `\d`, `\w`, `\s`, `\b` and their uppercase opposites
- [ ] `re.sub()` for replacement, `re.split()` for splitting
- [ ] `re.IGNORECASE`, `re.DOTALL`, `re.MULTILINE`
- [ ] Lambda: syntax and when NOT to use
- [ ] `map()`, `filter()`, `reduce()`
- [ ] Generators: `yield` keyword, memory efficiency
- [ ] Decorator pattern
- [ ] `if __name__ == "__main__"` for scripts
