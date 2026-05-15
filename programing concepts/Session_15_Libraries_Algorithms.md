# Session 15 – Libraries, Networking, Exploit Dev & Algorithms
> **Syllabus:** 2T + 5L + 4SL | Module B: Python

---

## 1. Key Libraries

### hashlib – Cryptographic Hashing

```python
import hashlib

# MD5 (fast, NOT cryptographically secure — don't use for passwords)
hashlib.md5(b"hello").hexdigest()       # "5d41402abc4b2a76b9719d911017c592"

# SHA-256 (secure)
hashlib.sha256(b"hello").hexdigest()    # long hex string

# SHA-512
hashlib.sha512(b"hello").hexdigest()

# Hash a file
def hash_file(filename):
    h = hashlib.sha256()
    with open(filename, "rb") as f:
        for chunk in iter(lambda: f.read(4096), b""):
            h.update(chunk)
    return h.hexdigest()

# Password hashing (use bcrypt/argon2 for real passwords)
import hashlib, os
salt = os.urandom(32)
key  = hashlib.pbkdf2_hmac("sha256", b"password", salt, 100000)
```

### Hash Algorithm Comparison

| Algorithm | Output Size | Speed | Security | Use Case |
|-----------|------------|-------|---------|---------|
| MD5 | 128 bit | Fast | ❌ Broken | File integrity |
| SHA-1 | 160 bit | Fast | ❌ Weak | Legacy |
| SHA-256 | 256 bit | Medium | ✅ | Blockchain, TLS |
| SHA-512 | 512 bit | Slower | ✅ | High-security |
| bcrypt | Variable | Slow (by design) | ✅ | Password storage |

---

### datetime – Date and Time

```python
from datetime import datetime, date, timedelta

now = datetime.now()
print(now)                          # 2024-01-15 10:30:45.123456
print(now.year, now.month, now.day) # 2024 1 15
print(now.strftime("%d/%m/%Y %H:%M"))  # "15/01/2024 10:30"

# Parse string to datetime
dt = datetime.strptime("15/01/2024", "%d/%m/%Y")

# Arithmetic
tomorrow  = now + timedelta(days=1)
last_week = now - timedelta(weeks=1)

# Difference between dates
d1 = date(2024, 1, 1)
d2 = date(2024, 12, 31)
diff = d2 - d1
print(diff.days)   # 365
```

### strftime Format Codes

| Code | Meaning | Example |
|------|---------|---------|
| `%Y` | 4-digit year | 2024 |
| `%m` | Month 01-12 | 01 |
| `%d` | Day 01-31 | 15 |
| `%H` | Hour 00-23 | 10 |
| `%M` | Minute 00-59 | 30 |
| `%S` | Second 00-59 | 45 |
| `%A` | Weekday name | Monday |
| `%B` | Month name | January |

---

## 2. Server-Client Architecture

```
Client                        Server
  │                              │
  │──── TCP connect ────────────>│
  │                              │ bind() + listen()
  │<─── accept() ────────────────│
  │                              │
  │──── send(request) ─────────>│
  │<─── recv(response) ──────────│
  │                              │
  │──── close() ────────────────>│
```

### HTTP Request via Socket

```python
import socket

def http_get(host, path="/"):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host, 80))
    request = f"GET {path} HTTP/1.1\r\nHost: {host}\r\nConnection: close\r\n\r\n"
    s.send(request.encode())
    response = b""
    while True:
        data = s.recv(4096)
        if not data:
            break
        response += data
    s.close()
    return response.decode(errors="replace")
```

---

## 3. Exploit Development Techniques (Ethical Security)

### Banner Grabbing

```python
import socket

def banner_grab(ip, port):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(3)
    try:
        s.connect((ip, port))
        banner = s.recv(1024).decode(errors="replace").strip()
        return banner
    except:
        return None
    finally:
        s.close()
```

### Port Scanner

```python
import socket
from concurrent.futures import ThreadPoolExecutor

def scan_port(ip, port):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(1)
    result = s.connect_ex((ip, port))   # returns 0 if open
    s.close()
    return port if result == 0 else None

def scan_range(ip, start=1, end=1024):
    open_ports = []
    with ThreadPoolExecutor(max_workers=100) as executor:
        futures = [executor.submit(scan_port, ip, p) for p in range(start, end+1)]
        for f in futures:
            if f.result(): open_ports.append(f.result())
    return sorted(open_ports)
```

### Debugging Basics

```python
# pdb — Python debugger
import pdb
pdb.set_trace()           # breakpoint (old style)
breakpoint()              # Python 3.7+ — preferred

# pdb commands:
# n — next line
# s — step into function
# c — continue
# p var — print variable
# l — list code
# q — quit
```

---

## 4. Self-Learning – Algorithms

### Sorting Algorithms

#### Bubble Sort – O(n²)
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr
```

#### Merge Sort – O(n log n) — Divide and Conquer
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid  = len(arr) // 2
    left  = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

#### Binary Search – O(log n)
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:   return mid
        elif arr[mid] < target:  left = mid + 1
        else:                    right = mid - 1
    return -1   # not found
```

---

### Time Complexity Reference

| Algorithm | Best | Average | Worst | Space |
|-----------|------|---------|-------|-------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) |
| Binary Search | O(1) | O(log n) | O(log n) | O(1) |

---

### Divide and Conquer vs Dynamic Programming

| | Divide and Conquer | Dynamic Programming |
|--|-------------------|-------------------|
| Approach | Divide into independent subproblems | Overlapping subproblems |
| Reuse solutions | ❌ | ✅ (memoization/tabulation) |
| Examples | Merge sort, Binary search | Fibonacci, Knapsack |
| Time saving | No | Yes (avoids recomputation) |

---

### Problem Solving

#### Find Single Number (XOR Trick)
```python
def single_number(nums):
    result = 0
    for n in nums:
        result ^= n     # XOR cancels paired numbers
    return result
# [2, 2, 3, 4, 4] → 3
# XOR: 2^2=0, 4^4=0, 0^3=3
```

#### GCD of Two Numbers (Euclidean Algorithm)
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
# gcd(48, 18) → 6

# LCM using GCD
def lcm(a, b):
    return (a * b) // gcd(a, b)
```

### Bit Manipulation Tricks

```python
# Check if number is even/odd
n & 1          # 0 = even, 1 = odd

# Multiply/divide by 2
n << 1         # n * 2
n >> 1         # n // 2

# Check if power of 2
n & (n-1) == 0     # True if power of 2 (n > 0)

# Count set bits (Brian Kernighan)
def count_bits(n):
    count = 0
    while n:
        n &= n - 1   # clears lowest set bit
        count += 1
    return count

# XOR: a ^ a = 0, a ^ 0 = a
# Swap without temp
a, b = 5, 3
a ^= b; b ^= a; a ^= b    # a=3, b=5
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. Why should you not use MD5 for password hashing?**
MD5 is fast (bad for passwords — easy to brute force) and has known collision vulnerabilities. Use bcrypt, scrypt, or Argon2 which are intentionally slow.

**Q2. What is the difference between hashing and encryption?**
Hashing is one-way (cannot be reversed). Encryption is two-way (can be decrypted with key). Use hashing for passwords, encryption for data that needs to be retrieved.

**Q3. What is the time complexity of binary search? What is required?**
O(log n). Requires the array to be **sorted** first.

**Q4. What is the XOR trick for finding a single number?**
`x ^ x = 0` and `x ^ 0 = x`. XORing all numbers cancels out pairs, leaving the unique one.

**Q5. What is the difference between Divide and Conquer and Dynamic Programming?**
D&C divides into independent subproblems — solutions aren't reused. DP solves overlapping subproblems and caches results to avoid recomputation.

**Q6. What does `connect_ex()` return vs `connect()`?**
`connect_ex()` returns an error code (0 = success) instead of raising an exception — useful for port scanning.

**Q7. What is Euclidean algorithm for GCD?**
Repeatedly replace (a, b) with (b, a % b) until b = 0. The last non-zero value is the GCD.

**Q8. How do you check if a number is a power of 2 using bit manipulation?**
`n > 0 and (n & (n-1)) == 0` — powers of 2 have exactly one set bit; subtracting 1 clears it and sets all lower bits.

**Q9. What is `strftime` vs `strptime`?**
`strftime` = format datetime → string ("format time"). `strptime` = parse string → datetime ("parse time").

**Q10. What does `breakpoint()` do in Python 3.7+?**
Drops into the `pdb` debugger at that line. Replaced the older `import pdb; pdb.set_trace()` pattern.

---

## 📌 Quick Revision Checklist
- [ ] hashlib: md5, sha256, sha512, pbkdf2_hmac
- [ ] Hashing vs encryption (one-way vs two-way)
- [ ] datetime: now(), strftime, strptime, timedelta
- [ ] Socket HTTP GET from scratch
- [ ] Banner grabbing and port scanning concepts
- [ ] `connect_ex()` vs `connect()`
- [ ] pdb: breakpoint(), n/s/c/p commands
- [ ] Merge sort — divide and conquer
- [ ] Binary search — O(log n), sorted array required
- [ ] Time complexity table for sorting
- [ ] D&C vs DP comparison
- [ ] XOR single number trick
- [ ] GCD — Euclidean algorithm
- [ ] Bit manipulation: even/odd, power of 2, count bits
