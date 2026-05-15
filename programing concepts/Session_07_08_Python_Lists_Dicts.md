# Sessions 7 & 8 – Python: Lists & Dictionaries
> **Syllabus:** 4T + 4L + 2SL | Module B: Python

---

## 1. Dictionaries

A **dictionary** is an ordered (Python 3.7+), mutable, key-value pair collection. Keys must be unique and hashable.

```python
# Define
student = {"name": "Alice", "age": 20, "grade": "A"}
empty = {}
empty = dict()

# Access
print(student["name"])           # "Alice"
print(student.get("age"))        # 20
print(student.get("phone", "N/A"))  # "N/A" (safe default)
```

> ⚠️ `dict["key"]` raises `KeyError` if missing. `dict.get("key")` returns `None` — safer!

### Modifying Dictionaries

```python
student["age"] = 21              # update existing
student["email"] = "a@b.com"     # add new key
student.update({"age": 22, "city": "Delhi"})  # update multiple

del student["grade"]             # remove key (KeyError if missing)
student.pop("age")               # remove and return value
student.popitem()                # remove last inserted item
student.clear()                  # empty the dict
```

### Iterating

```python
# Keys
for key in student:
    print(key)

for key in student.keys():
    print(key)

# Values
for val in student.values():
    print(val)

# Key-Value pairs
for key, val in student.items():
    print(f"{key}: {val}")
```

### Useful Methods

```python
len(student)                     # number of key-value pairs
"name" in student                # True — check key existence
list(student.keys())             # ['name', 'age', 'grade']
list(student.values())           # ['Alice', 20, 'A']
```

### Dictionary Comprehension

```python
squares = {x: x**2 for x in range(1, 6)}
# {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

---

## 2. Lists

A **list** is an ordered, mutable, indexed sequence that allows duplicates.

```python
fruits = ["apple", "banana", "cherry"]
mixed  = [1, "hello", 3.14, True, None]
nested = [[1, 2], [3, 4], [5, 6]]
empty  = []
```

### Adding Elements

```python
fruits.append("mango")            # add to end
fruits.insert(1, "grape")         # insert at index 1
fruits.extend(["kiwi", "papaya"]) # add multiple items
```

### Accessing & Slicing

```python
fruits[0]        # first element
fruits[-1]       # last element
fruits[1:3]      # index 1 and 2 (stop is exclusive)
fruits[::-1]     # reversed list
fruits[::2]      # every other element
```

### Searching & Checking

```python
"apple" in fruits               # True
fruits.index("banana")          # returns index (ValueError if not found)
fruits.count("apple")           # count occurrences
```

### Deleting Elements

```python
fruits.remove("banana")         # remove first occurrence (ValueError if missing)
fruits.pop()                    # remove and return last element
fruits.pop(1)                   # remove and return element at index 1
del fruits[0]                   # delete by index
del fruits[1:3]                 # delete slice
fruits.clear()                  # empty the list
```

### Sorting & Reversing

```python
nums = [3, 1, 4, 1, 5, 9]
nums.sort()                     # in-place ascending
nums.sort(reverse=True)         # in-place descending
sorted(nums)                    # returns NEW sorted list (original unchanged)
nums.reverse()                  # reverse in-place
```

> ⚠️ `sort()` modifies the original. `sorted()` returns a new list.

### List Operators

```python
[1, 2] + [3, 4]    # [1, 2, 3, 4]  — concatenation
[0] * 5            # [0, 0, 0, 0, 0] — repetition
len([1, 2, 3])     # 3
```

### List Comprehension

```python
squares  = [x**2 for x in range(1, 6)]              # [1, 4, 9, 16, 25]
evens    = [x for x in range(10) if x % 2 == 0]     # [0, 2, 4, 6, 8]
filtered = [x*2 for x in range(5) if x != 2]        # [0, 2, 6, 8]
```

---

## 3. Self-Learning Problems

### Longest Consecutive Sequence – O(n)

```python
def longest_consecutive(nums):
    num_set = set(nums)        # O(1) lookup
    max_len = 0
    for n in num_set:
        if n - 1 not in num_set:    # only start from sequence start
            length = 1
            while n + length in num_set:
                length += 1
            max_len = max(max_len, length)
    return max_len

print(longest_consecutive([100, 4, 200, 1, 3, 2]))  # 4 (1,2,3,4)
```

### Product of Array Except Self

```python
def product_except_self(nums):
    n = len(nums)
    result = [1] * n
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    return result
```

### Merge Overlapping Intervals

```python
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between `append()` and `extend()`?**
`append()` adds a single element (even a list as one element). `extend()` iterates and adds each element from an iterable. `[1,2].append([3,4])` → `[1, 2, [3, 4]]`. `[1,2].extend([3,4])` → `[1, 2, 3, 4]`.

**Q2. What is the difference between `remove()` and `pop()`?**
`remove(value)` removes the first occurrence of a value. `pop(index)` removes by index and returns the value. `pop()` with no argument removes the last item.

**Q3. What is the difference between `sort()` and `sorted()`?**
`sort()` is an in-place list method (returns None). `sorted()` is a built-in function that returns a NEW sorted list. Works on any iterable.

**Q4. What does `list[::-1]` do?**
Returns a reversed copy of the list using slicing (step = -1).

**Q5. What is the difference between `dict["key"]` and `dict.get("key")`?**
`dict["key"]` raises `KeyError` if key doesn't exist. `dict.get("key")` returns `None` (or a default) without raising an error.

**Q6. Are dictionaries ordered in Python?**
Yes — from Python 3.7+, dictionaries maintain insertion order. Before 3.7, they were unordered.

**Q7. Can dictionary keys be lists?**
No — keys must be **hashable** (immutable). Lists are mutable and unhashable. Tuples can be keys.

**Q8. What is the output of `[1, 2, 3] * 2`?**
`[1, 2, 3, 1, 2, 3]`

**Q9. What does `list.pop()` return if list is empty?**
`IndexError: pop from empty list`

**Q10. What is the time complexity of `in` operator for a list vs a set?**
List: O(n) — linear search. Set/dict: O(1) — hash lookup. For frequent membership checks, use a set.

**Q11. What will `nums.sort()` return?**
`None` — it's an in-place method. Common mistake: `sorted_list = nums.sort()` gives `sorted_list = None`.

**Q12. How do you merge two dictionaries in Python 3.9+?**
`merged = dict1 | dict2` — the `|` merge operator. Older way: `{**dict1, **dict2}`.

---

## 📌 Quick Revision Checklist
- [ ] dict creation, access ([] vs get()), modification
- [ ] dict.keys(), values(), items(), pop(), popitem(), clear()
- [ ] Dictionary comprehension
- [ ] list.append() vs extend() vs insert()
- [ ] list.remove() vs pop() vs del
- [ ] sort() vs sorted() — in-place vs new
- [ ] List slicing: [start:stop:step], [::-1]
- [ ] List comprehension with condition
- [ ] Falsy keys in dict lookup
- [ ] Hashable requirement for dict keys
- [ ] O(n) list vs O(1) set for `in` check
