# Session 9 – Python: Tuples, Variables & Sets
> **Syllabus:** 2T + 2L + 2SL | Module B: Python

---

## 1. Tuples

A **tuple** is an ordered, **immutable** (unchangeable), indexed sequence that allows duplicates.

```python
# Creating tuples
colors = ("red", "green", "blue")
single = (42,)          # ⚠️ trailing comma required for single-element tuple
single = 42,            # also valid — parentheses optional
empty  = ()
mixed  = (1, "hello", 3.14, True)

# Without parentheses (tuple packing)
point = 10, 20          # (10, 20)
```

> ⚠️ `(42)` is NOT a tuple — it's just `42` in parentheses. `(42,)` IS a tuple.

### Accessing Elements

```python
colors[0]           # "red"
colors[-1]          # "blue"
colors[1:3]         # ("green", "blue")
```

### Tuple Methods (only 2!)

```python
colors.count("red")     # number of occurrences
colors.index("green")   # index of first occurrence
```

### Tuple Unpacking

```python
a, b, c = colors            # a="red", b="green", c="blue"
x, *rest = (1, 2, 3, 4)    # x=1, rest=[2, 3, 4]
a, b = b, a                 # swap variables — uses tuple packing/unpacking
```

### Converting Between Types

```python
list(colors)    # ("red","green","blue") → ["red","green","blue"]
tuple([1,2,3])  # [1,2,3] → (1, 2, 3)
```

### Why Use Tuples Over Lists?

| | Tuple | List |
|--|-------|------|
| Mutable | ❌ | ✅ |
| Speed | Faster | Slower |
| Memory | Less | More |
| Hashable | ✅ (can be dict key) | ❌ |
| Use case | Fixed data, coordinates, DB rows | Dynamic collections |

---

## 2. Variables & Multiple Assignment

```python
# Declare and assign
x = 10
name = "Alice"

# Multiple assignment
a = b = c = 0          # all point to same object initially

# Tuple unpacking assignment
x, y, z = 1, 2, 3

# Augmented assignment
x += 5    # x = x + 5
x -= 3
x *= 2
x //= 3   # floor division
x **= 2   # exponentiation
x %= 4    # modulo
```

### Variable Naming Rules
- Must start with letter or `_`
- Cannot start with digit
- Case-sensitive (`Name` ≠ `name`)
- Cannot use keywords (`if`, `for`, `while`, etc.)

---

## 3. Joining Lists & Splitting Strings

```python
# Join list → string
words = ["Hello", "World"]
" ".join(words)          # "Hello World"
"-".join(words)          # "Hello-World"
"".join(words)           # "HelloWorld"

# Split string → list
"Hello World".split()          # ["Hello", "World"]
"a,b,c".split(",")             # ["a", "b", "c"]
"a,b,c".split(",", 1)          # ["a", "b,c"] (maxsplit=1)
"hello".split("")              # ❌ ValueError — empty separator not allowed
list("hello")                  # ['h', 'e', 'l', 'l', 'o']

# Strip whitespace
"  hello  ".strip()            # "hello"
"  hello  ".lstrip()           # "hello  "
"  hello  ".rstrip()           # "  hello"
```

---

## 4. Introduction to Sets

A **set** is an unordered, mutable, collection of **unique** elements. No duplicates, no indexing.

```python
s = {1, 2, 3, 4}
s = set([1, 2, 2, 3])    # {1, 2, 3} — duplicates removed
empty_set = set()         # ⚠️ {} creates empty DICT, not set!
```

### Set Operations

```python
s.add(5)           # add element
s.remove(3)        # remove (KeyError if missing)
s.discard(3)       # remove (no error if missing)
s.pop()            # remove and return arbitrary element
s.clear()          # empty the set

# Membership
3 in s             # True — O(1)
```

### Set Math Operations

```python
A = {1, 2, 3, 4}
B = {3, 4, 5, 6}

A | B              # Union: {1,2,3,4,5,6}
A & B              # Intersection: {3,4}
A - B              # Difference: {1,2} (in A but not B)
B - A              # {5,6}
A ^ B              # Symmetric difference: {1,2,5,6} (in one but not both)
A.issubset(B)      # False
A.issuperset(B)    # False
A.isdisjoint(B)    # False (they share elements)
```

---

## 5. Self-Learning Problems

### Swap Tuple Elements

```python
def swap_tuple(t, i, j):
    lst = list(t)       # convert to list (mutable)
    lst[i], lst[j] = lst[j], lst[i]
    return tuple(lst)
```

### Sort Tuples by Score

```python
students = [("Alice", 85), ("Bob", 92), ("Carol", 78)]
students.sort(key=lambda x: x[1], reverse=True)
# [("Bob", 92), ("Alice", 85), ("Carol", 78)]
```

### Cross Pairing of Tuples

```python
A = (1, 2)
B = (3, 4)
pairs = [(a, b) for a in A for b in B]
# [(1,3), (1,4), (2,3), (2,4)]
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between a list and a tuple?**
Both are ordered sequences allowing duplicates and indexing. Tuples are **immutable** (cannot be changed after creation), faster, use less memory, and can be used as dictionary keys. Lists are mutable.

**Q2. How do you create a tuple with ONE element?**
`(42,)` — trailing comma is essential. `(42)` is just the integer 42.

**Q3. What is the output of `{}`? Is it a dict or set?**
It's an empty **dictionary**. To create an empty set, use `set()`.

**Q4. Can a tuple contain mutable objects?**
Yes — a tuple can hold a list: `t = ([1,2], [3,4])`. The list INSIDE can be modified, but you can't reassign `t[0]` to something else.

**Q5. What is the difference between `remove()` and `discard()` in sets?**
Both remove an element, but `remove()` raises `KeyError` if the element is not found. `discard()` silently does nothing.

**Q6. What is the time complexity of set membership check (`in`)?**
O(1) average — sets use hash tables.

**Q7. Can sets contain duplicate elements?**
No — sets automatically remove duplicates. `{1, 2, 2, 3}` stores `{1, 2, 3}`.

**Q8. What is the output of `tuple([1,2,3]) + (4,)`?**
`(1, 2, 3, 4)` — tuple concatenation creates a new tuple.

**Q9. What is tuple unpacking with `*`?**
`a, *rest = (1,2,3,4)` gives `a=1, rest=[2,3,4]`. The `*` collects remaining elements into a list.

**Q10. What is `A ^ B` in set operations?**
Symmetric difference — elements in A OR B but NOT in both.

**Q11. How do you sort a list of tuples by the second element?**
`lst.sort(key=lambda x: x[1])` or `sorted(lst, key=lambda x: x[1])`

**Q12. What is the output of `set([1, 1, 2, 3, 3])`?**
`{1, 2, 3}` — duplicates removed.

---

## 📌 Quick Revision Checklist
- [ ] Tuple: immutable, ordered, allows duplicates
- [ ] Single-element tuple: `(42,)` NOT `(42)`
- [ ] Tuple unpacking and `*rest` syntax
- [ ] Swap variables with tuple unpacking: `a, b = b, a`
- [ ] Set: unordered, unique, mutable
- [ ] `{}` = empty dict; `set()` = empty set
- [ ] Set operations: `|`, `&`, `-`, `^`
- [ ] `remove()` vs `discard()` in sets
- [ ] `.join()` and `.split()` string methods
- [ ] Tuple vs List comparison table
- [ ] Sort tuples with `key=lambda`
