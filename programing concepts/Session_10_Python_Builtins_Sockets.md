# Session 10 – Python: Arguments, Built-ins, Object References & Sockets
> **Syllabus:** 2T + 2L + 2SL | Module B: Python

---

## 1. Optional and Named Arguments

```python
# Positional arguments
def add(a, b):
    return a + b
add(3, 4)           # a=3, b=4

# Default (optional) arguments
def greet(name, msg="Hello"):
    print(f"{msg}, {name}!")
greet("Alice")          # Hello, Alice!
greet("Bob", "Hi")      # Hi, Bob!

# Keyword (named) arguments — order doesn't matter
greet(msg="Hey", name="Carol")

# *args — variable positional arguments (tuple)
def total(*nums):
    return sum(nums)
total(1, 2, 3, 4)   # 10

# **kwargs — variable keyword arguments (dict)
def info(**details):
    for k, v in details.items():
        print(f"{k}: {v}")
info(name="Alice", age=20, city="Delhi")
```

### Argument Order Rule
`def func(pos, /, normal, *, kw_only):`
- Positional → Default → *args → **kwargs

```python
def func(a, b=10, *args, **kwargs):
    pass
```

> ⚠️ Default arguments must come **after** non-default arguments.

---

## 2. Built-in Functions

### type, str, dir

```python
type(42)           # <class 'int'>
type("hi")         # <class 'str'>
type([1,2])        # <class 'list'>

str(42)            # "42"
str(3.14)          # "3.14"
str(True)          # "True"
str(None)          # "None"

dir([])            # list all methods of a list object
dir(str)           # list all string methods
dir(__builtins__)  # list all built-in functions
```

### Important Built-ins

```python
abs(-5)             # 5
round(3.567, 2)     # 3.57
pow(2, 10)          # 1024
divmod(17, 5)       # (3, 2) — quotient and remainder
max(3, 1, 4, 1)     # 4
min([5, 2, 8])      # 2
sum([1, 2, 3])      # 6
len("hello")        # 5
sorted([3,1,2])     # [1, 2, 3]
reversed([1,2,3])   # iterator — use list(reversed(...))
enumerate(["a","b","c"])    # (0,"a"), (1,"b"), (2,"c")
zip([1,2,3], ["a","b","c"]) # (1,"a"), (2,"b"), (3,"c")
map(str, [1,2,3])   # ['1','2','3']
filter(lambda x: x>2, [1,2,3,4])  # [3,4]
any([False, True, False])   # True
all([True, True, True])     # True
isinstance(True, int)        # True (bool is subclass of int)
```

---

## 3. Object References

Python variables are **references (pointers)** to objects, not the objects themselves.

```python
a = [1, 2, 3]
b = a           # b points to SAME list object
b.append(4)
print(a)        # [1, 2, 3, 4] — a also changed!

# To copy, use:
b = a.copy()       # shallow copy
b = a[:]           # shallow copy via slicing
import copy
b = copy.deepcopy(a)   # deep copy (nested structures)
```

### Shallow vs Deep Copy

```python
original = [[1, 2], [3, 4]]
shallow  = original.copy()
deep     = copy.deepcopy(original)

original[0].append(99)
print(shallow[0])   # [1, 2, 99] — inner list IS shared
print(deep[0])      # [1, 2] — completely independent
```

### Integer Caching (Interning)

```python
a = 256
b = 256
a is b   # True — Python caches small integers (-5 to 256)

a = 257
b = 257
a is b   # False — beyond cache range, different objects
```

---

## 4. Socket Programming with Python

### What is a Socket?
A **socket** is an endpoint for sending/receiving data across a network. It's identified by an IP address + port number.

```
Client ←──── TCP/IP ────→ Server
       socket()              socket()
       connect()             bind()
       send/recv()           listen()
                             accept()
                             send/recv()
```

### Simple TCP Server

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(("0.0.0.0", 9999))
server.listen(5)
print("Waiting for connection...")

conn, addr = server.accept()
print(f"Connected from {addr}")
data = conn.recv(1024)
print(f"Received: {data.decode()}")
conn.send(b"Hello from server!")
conn.close()
server.close()
```

### Simple TCP Client

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(("127.0.0.1", 9999))
client.send(b"Hello from client!")
response = client.recv(1024)
print(f"Server says: {response.decode()}")
client.close()
```

### Socket Constants

| Constant | Meaning |
|----------|---------|
| `AF_INET` | IPv4 address family |
| `AF_INET6` | IPv6 address family |
| `SOCK_STREAM` | TCP (reliable, connection-oriented) |
| `SOCK_DGRAM` | UDP (fast, connectionless) |

---

## 5. Self-Learning: String Problems

### Longest Substring Without Repeating Characters

```python
def length_of_longest_substring(s):
    char_set = set()
    left = max_len = 0
    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    return max_len
# "abcabcbb" → 3 ("abc")
```

### Longest Palindromic Substring

```python
def longest_palindrome(s):
    res = ""
    for i in range(len(s)):
        for odd_even in [s[i:i+1], s[i:i+2]]:   # odd and even centers
            while True:
                l = i - (len(res) // 2)
                break
        # Expand around center approach
    pass  # See full solution with expand helper
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between `*args` and `**kwargs`?**
`*args` collects extra positional arguments as a **tuple**. `**kwargs` collects extra keyword arguments as a **dict**.

**Q2. What is the output of `b = a` when a is a list, then `b.append(5)`?**
`a` is also changed — both `a` and `b` reference the **same list object** in memory.

**Q3. What is the difference between shallow and deep copy?**
Shallow copy duplicates the outer object but shares references to inner (nested) objects. Deep copy duplicates everything recursively.

**Q4. What does `any()` return on an empty iterable?**
`False`. `all()` on an empty iterable returns `True` (vacuously true).

**Q5. What is `divmod(17, 5)`?**
`(3, 2)` — returns (quotient, remainder) as a tuple.

**Q6. What is `AF_INET` and `SOCK_STREAM`?**
`AF_INET` = IPv4. `SOCK_STREAM` = TCP protocol (reliable, ordered).

**Q7. What does `isinstance(True, int)` return?**
`True` — because `bool` is a subclass of `int` in Python.

**Q8. What is the purpose of `dir(obj)`?**
Returns a list of all attributes and methods of an object. Useful for discovery and introspection.

**Q9. Can default argument values be mutable objects?**
Yes but DANGEROUS. Using a mutable default like `def f(lst=[])` causes the list to persist across calls — classic Python gotcha. Use `def f(lst=None): if lst is None: lst = []`.

**Q10. What is `enumerate()` used for?**
Iterates with index and value: `for i, v in enumerate(["a","b"])` gives (0,"a"), (1,"b"). Avoids manual counter.

---

## 📌 Quick Revision Checklist
- [ ] Positional / Default / *args / **kwargs
- [ ] Argument order rule
- [ ] `type()`, `str()`, `dir()`, `id()`
- [ ] `map()`, `filter()`, `zip()`, `enumerate()`
- [ ] `any()` and `all()` with edge cases
- [ ] Object references vs copies
- [ ] Shallow copy vs deep copy
- [ ] Integer caching (-5 to 256)
- [ ] Mutable default argument gotcha
- [ ] Socket: TCP server/client flow
- [ ] `AF_INET`, `SOCK_STREAM`, `SOCK_DGRAM`
