# Session 12 – Python: OOP & Linux File System
> **Syllabus:** 2T + 3L | Module B: Python

---

## 1. Object-Oriented Programming (OOP)

### Core Concepts

| Concept | Definition |
|---------|-----------|
| **Class** | Blueprint/template for objects |
| **Object/Instance** | Concrete realization of a class |
| **Attribute** | Data stored in an object |
| **Method** | Function defined inside a class |
| **Encapsulation** | Bundling data and methods; restricting direct access |
| **Inheritance** | Child class inherits from parent class |
| **Polymorphism** | Same interface, different behavior |
| **Abstraction** | Hiding implementation details |

---

### Classes and Objects

```python
class Employee:
    company = "TechCorp"          # class attribute (shared by all instances)

    def __init__(self, name, salary):   # constructor
        self.name = name               # instance attribute
        self.salary = salary

    def display(self):
        print(f"{self.name} earns {self.salary}")

    def __str__(self):              # string representation
        return f"Employee({self.name})"

    def __repr__(self):             # developer representation
        return f"Employee(name={self.name!r}, salary={self.salary!r})"

# Create objects
e1 = Employee("Alice", 60000)
e2 = Employee("Bob", 75000)

e1.display()                    # Alice earns 60000
print(Employee.company)         # TechCorp (class attribute)
print(e1.company)               # TechCorp (accessed via instance too)
```

---

### Access Modifiers (Convention-based in Python)

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance          # public
        self._interest = 0.05           # protected (convention: one underscore)
        self.__pin = 1234               # private (name mangling: two underscores)

    def get_pin(self):
        return self.__pin               # access via method

acc = BankAccount(1000)
print(acc.balance)          # 1000  ✅
print(acc._interest)        # 0.05  ✅ (accessible but discouraged)
print(acc.__pin)            # AttributeError ❌
print(acc._BankAccount__pin)  # 1234  ✅ (name mangling — Python's "private")
```

> ⚠️ Python has no TRUE private. `__attr` just renames to `_ClassName__attr`. This is called **name mangling**.

---

### Inheritance

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):              # inherits from Animal
    def speak(self):            # method overriding
        print(f"{self.name} says Woof!")

class Cat(Animal):
    def speak(self):
        print(f"{self.name} says Meow!")

# Polymorphism
animals = [Dog("Rex"), Cat("Whiskers")]
for a in animals:
    a.speak()                   # each behaves differently

# Super()
class GuideDog(Dog):
    def __init__(self, name, owner):
        super().__init__(name)  # call parent constructor
        self.owner = owner
```

### Multiple Inheritance

```python
class A:
    def method(self): print("A")

class B(A):
    def method(self): print("B")

class C(A):
    def method(self): print("C")

class D(B, C):    # MRO: D → B → C → A
    pass

D().method()    # "B" — follows MRO (Method Resolution Order)
print(D.__mro__)  # shows the order
```

### Special (Dunder) Methods

```python
class Point:
    def __init__(self, x, y): self.x, self.y = x, y
    def __add__(self, other): return Point(self.x+other.x, self.y+other.y)
    def __eq__(self, other):  return self.x==other.x and self.y==other.y
    def __len__(self):         return int((self.x**2 + self.y**2)**0.5)
    def __str__(self):         return f"({self.x}, {self.y})"

p1 = Point(1, 2)
p2 = Point(3, 4)
print(p1 + p2)   # (4, 6) — uses __add__
```

---

## 2. Linux File System (OOP Lab Context)

```python
import os

# Current directory
os.getcwd()                      # '/home/user'

# List files and folders
os.listdir(".")                  # all in current dir
os.listdir("/etc")

# Absolute vs relative path
os.path.abspath("file.txt")      # full path
os.path.relpath("/home/user/file.txt", "/home")  # "user/file.txt"

# Check existence
os.path.exists("/etc/hosts")     # True
os.path.isfile("/etc/hosts")     # True
os.path.isdir("/etc")            # True

# Create / remove directory
os.mkdir("new_folder")           # create one level
os.makedirs("a/b/c")             # create nested
os.rmdir("new_folder")           # remove empty dir
import shutil
shutil.rmtree("a")               # remove non-empty dir

# Check permissions
os.access("file.txt", os.R_OK)   # readable?
os.access("file.txt", os.W_OK)   # writable?
os.access("file.txt", os.X_OK)   # executable?

# File stats
stat = os.stat("file.txt")
print(stat.st_size)              # file size in bytes
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between a class attribute and an instance attribute?**
Class attribute is shared across all instances (one copy). Instance attribute belongs to a specific object (separate copy per instance). If you set `obj.class_attr = value`, it creates a NEW instance attribute shadowing the class attribute.

**Q2. What is `__init__`? Is it a constructor?**
It's an **initializer**, not a constructor. Python's actual constructor is `__new__()` which creates the object. `__init__` initializes it after creation. In practice, `__init__` is what we customize.

**Q3. What is name mangling in Python?**
Double underscore prefix (`__attr`) renames the attribute to `_ClassName__attr` to prevent accidental overriding in subclasses. NOT truly private — still accessible via the mangled name.

**Q4. What is the difference between `__str__` and `__repr__`?**
`__str__` is for end-users — readable string. `__repr__` is for developers — unambiguous, should ideally allow `eval()` to recreate the object. `print(obj)` calls `__str__`; REPL calls `__repr__`.

**Q5. What is MRO (Method Resolution Order)?**
The order Python searches for a method in class hierarchy for multiple inheritance. Uses C3 linearization algorithm. Check with `ClassName.__mro__` or `help(ClassName)`.

**Q6. What does `super()` do?**
Returns a proxy object of the parent class, used to call parent methods without hardcoding the parent class name. Especially important in multiple inheritance.

**Q7. What is polymorphism with an example?**
Same method name behaves differently for different types. Example: `len("hello")` = 5, `len([1,2,3])` = 3 — same `len()` function, different behavior based on type.

**Q8. What is the difference between `os.mkdir()` and `os.makedirs()`?**
`mkdir()` creates only one directory (fails if parent doesn't exist). `makedirs()` creates all intermediate directories recursively.

**Q9. What is encapsulation? Is it the same as information hiding?**
Encapsulation bundles data and methods into a class. Information hiding restricts access to internal state (via access modifiers). Encapsulation implements information hiding.

**Q10. What is `isinstance()` vs `type()` for type checking?**
`isinstance(obj, Dog)` returns True for `Dog` instances AND subclasses. `type(obj) == Dog` is True ONLY for exact `Dog` instances. Use `isinstance()` for proper OOP code.

---

## 📌 Quick Revision Checklist
- [ ] Class vs Object / Instance
- [ ] `__init__`, `__str__`, `__repr__`
- [ ] Class attribute vs instance attribute
- [ ] Public, `_protected`, `__private` (name mangling)
- [ ] Inheritance + `super()` usage
- [ ] Method overriding
- [ ] Multiple inheritance + MRO
- [ ] Polymorphism concept
- [ ] Dunder methods: `__add__`, `__eq__`, `__len__`
- [ ] `os.getcwd()`, `listdir()`, `path.exists()`, `mkdir()`, `makedirs()`
- [ ] `isinstance()` vs `type()`
