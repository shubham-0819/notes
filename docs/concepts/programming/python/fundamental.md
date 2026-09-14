# Python Fundamentals — Quick Reference (with examples)

> Lookup sheet for day-to-day dev + interviews. Every method has a runnable example next to it.

---

## 1. Basics

```python
# Single-line comment
"""
Multi-line string / docstring
(also used as a "comment block", but it's actually a string literal)
"""

print("Hello, World!")

name = "Shubham"
age = 30
salary = 50000.50
is_active = True
```

### Naming rules

```python
user_name = "John"      # valid (snake_case is convention)
_user = "test"           # valid, "private-ish" by convention
user1 = "abc"             # valid

# 1user = "abc"          # invalid — can't start with digit
# user-name = "abc"       # invalid — hyphen not allowed
```

---

## 2. Data Types

### Numeric

```python
x = 10          # int
y = 10.5        # float
z = 3 + 4j       # complex

abs(-10)           # 10
round(3.1415, 2)    # 3.14
round(2.5)          # 2  <- banker's rounding! rounds to nearest EVEN int
pow(2, 3)            # 8
pow(2, 3, 5)          # 8 % 5 = 3  (modular exponentiation, fast)
divmod(17, 5)          # (3, 2) -> (quotient, remainder)
```

### String (`str`) — immutable

```python
s = "Python Rocks"

s.upper()                  # 'PYTHON ROCKS'
s.lower()                  # 'python rocks'
s.capitalize()             # 'Python rocks'
s.title()                  # 'Python Rocks'
s.swapcase()               # 'pYTHON rOCKS'

s.startswith("Py")         # True
s.endswith("cks")          # True

s.replace("Py", "Cy")      # 'Cython Rocks'
s.replace("o", "0", 1)     # replace only first occurrence -> 'Pyth0n Rocks'

s.split()                  # ['Python', 'Rocks']  (splits on whitespace by default)
s.split("t")               # ['Py', 'hon Rocks']
s.rsplit(" ", 1)           # split from the right, max 1 split -> ['Python', 'Rocks']
s.splitlines()             # split on \n

"-".join(["a", "b", "c"])  # 'a-b-c'

s.find("th")               # 4 (index, -1 if not found, no exception)
s.index("th")              # 4 (like find, but raises ValueError if not found)
s.count("o")                # 2

s.strip()                  # remove leading/trailing whitespace
s.lstrip()                  # left only
s.rstrip()                  # right only
"  hi  ".strip()             # 'hi'
"xxhixx".strip("x")           # 'hi'  <- strips any of the given chars, not a substring

s.isdigit()                 # False
"123".isdigit()               # True
"abc".isalpha()               # True
"abc123".isalnum()             # True
"  ".isspace()                  # True

s.zfill(5)                   # zero-pad numeric-looking strings: '5'.zfill(3) -> '005'
s.ljust(20, "-")               # left-justify, pad with '-'
s.rjust(20, "-")                 # right-justify
s.center(20, "-")                 # center

len(s)                        # 12
s[::-1]                        # reverse a string: 'skcoR nohtyP'
```

#### f-strings (the modern way to format)

```python
name, age = "Shubham", 30

f"{name} is {age} years old"        # 'Shubham is 30 years old'
f"{age:03d}"                         # zero-pad int to width 3 -> '030'
f"{3.14159:.2f}"                      # 2 decimal places -> '3.14'
f"{1000000:,}"                         # thousands separator -> '1,000,000'
f"{name!r}"                             # repr -> "'Shubham'"
f"{age=}"                                # debug spec (3.8+) -> 'age=30'
f"{'yes' if age >= 18 else 'no'}"          # inline expressions work

"{} is {}".format(name, age)                # old .format() style
"%s is %d" % (name, age)                     # old %-style (legacy, avoid in new code)
```

### List (`list`) — mutable, ordered

```python
nums = [1, 2, 3]

nums.append(4)          # [1, 2, 3, 4]              add single item to end
nums.extend([5, 6])     # [1, 2, 3, 4, 5, 6]         add multiple items
nums.insert(1, 10)      # [1, 10, 2, 3, 4, 5, 6]     insert at index

nums.remove(10)         # removes FIRST matching value (ValueError if absent)
nums.pop()              # removes & returns LAST item
nums.pop(0)             # removes & returns item at index 0
nums.clear()            # empties the list

nums = [1, 2, 3, 2]
nums.index(2)            # 1 (first match)
nums.count(2)             # 2

nums.sort()                        # in-place ascending sort, returns None
nums.sort(reverse=True)             # descending
nums.sort(key=lambda x: -x)          # custom sort key
sorted(nums)                          # returns NEW sorted list, original untouched
nums.reverse()                         # in-place reverse
nums2 = nums.copy()                     # shallow copy (or nums[:] or list(nums))
```

#### List comprehensions

```python
squares = [x * x for x in range(5)]                 # [0, 1, 4, 9, 16]
evens = [x for x in range(10) if x % 2 == 0]          # [0, 2, 4, 6, 8]
flat = [x for row in [[1,2],[3,4]] for x in row]        # flatten -> [1, 2, 3, 4]
nested = [[i * j for j in range(3)] for i in range(3)]    # 2D matrix
```

### Tuple (`tuple`) — immutable, ordered

```python
point = (10, 20)

point.count(10)      # 1
point.index(20)       # 1

x, y = point            # unpacking
(a, b), c = (1, 2), 3     # nested unpacking

single = (5,)              # NOTE: trailing comma needed for a 1-item tuple, (5) is just an int
```

### Set (`set`) — mutable, unordered, unique elements

```python
nums = {1, 2, 3}

nums.add(4)              # {1, 2, 3, 4}
nums.update([5, 6])       # add multiple -> {1,2,3,4,5,6}

nums.remove(2)              # KeyError if absent
nums.discard(10)             # no error if absent (safer than remove)

nums.pop()                    # removes & returns an ARBITRARY element
nums.clear()

a = {1, 2, 3}
b = {3, 4, 5}

a | b        # union            -> {1,2,3,4,5}
a & b        # intersection     -> {3}
a - b        # difference       -> {1,2}
a ^ b        # symmetric diff   -> {1,2,4,5}
a.issubset(b)     # False
a.issuperset(b)    # False
a.isdisjoint(b)     # False (they share 3)

frozenset([1,2,3])   # immutable set, hashable (usable as dict key)
```

#### Set comprehension

```python
unique_lengths = {len(w) for w in ["hi", "bye", "ok"]}   # {2, 3}
```

### Dictionary (`dict`) — mutable, key-value, insertion-ordered (3.7+)

```python
user = {"name": "Shubham", "age": 30}

user.get("name")                 # 'Shubham'
user.get("missing", "N/A")        # 'N/A'  <- default avoids KeyError
user.keys()                        # dict_keys(['name', 'age'])
user.values()                       # dict_values(['Shubham', 30])
user.items()                         # dict_items([('name','Shubham'), ('age',30)])

user.update({"city": "Noida"})         # merge/add keys
user.pop("age")                          # removes key, returns value (KeyError if absent + no default)
user.pop("missing", None)                 # safe pop with default

user.setdefault("country", "India")        # sets key only if absent, returns the value
user.clear()
user2 = user.copy()                          # shallow copy

# merging dicts (3.9+)
merged = {"a": 1} | {"b": 2}                   # {'a': 1, 'b': 2}
d = {"a": 1}
d |= {"b": 2}                                    # in-place merge
```

#### Dict comprehension

```python
squares = {x: x * x for x in range(5)}           # {0:0, 1:1, 2:4, 3:9, 4:16}
inverted = {v: k for k, v in squares.items()}      # swap keys/values
```

---

## 3. Operators

```python
+   -   *   /   //   %   **      # arithmetic
10 / 3      # 3.333...   true division, always float
10 // 3     # 3           floor division
10 % 3      # 1            modulo
10 ** 2     # 100            exponent
-7 // 2     # -4               floor div rounds toward -infinity, not 0! (gotcha)

==  !=  >  <  >=  <=          # comparison
and  or  not                    # logical
in  not in                       # membership
is  is not                        # identity (compares object identity, not value!)

x = [1, 2]
y = [1, 2]
x == y      # True  (same value)
x is y       # False (different objects in memory)
x is x        # True
```

---

## 4. Conditionals

```python
age = 18

if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")

# Ternary
status = "Adult" if age >= 18 else "Minor"

# Walrus operator (3.8+) — assign inside an expression
if (n := len([1,2,3])) > 2:
    print(f"List has {n} items")
```

---

## 5. Loops

```python
for i in range(5):
    print(i)

count = 0
while count < 5:
    count += 1

for i in range(10):
    if i == 3:
        continue    # skip this iteration
    if i == 6:
        break         # exit loop
    pass                # no-op placeholder

# for/else — else runs only if loop completes WITHOUT break
for i in range(5):
    if i == 99:
        break
else:
    print("loop finished without break")
```

---

## 6. Functions

```python
def greet(name):
    return f"Hello {name}"

def greet_default(name="Guest"):
    return f"Hello {name}"

def func(*args, **kwargs):
    # args -> tuple of positional args
    # kwargs -> dict of keyword args
    print(args, kwargs)

func(1, 2, a=3, b=4)     # (1, 2) {'a': 3, 'b': 4}

square = lambda x: x * x
add = lambda x, y: x + y

# Type hints (good practice, not enforced at runtime)
def add_typed(a: int, b: int) -> int:
    return a + b

# keyword-only / positional-only args (3.8+)
def f(a, b, /, c, *, d):
    # a, b: positional-only
    # c: positional or keyword
    # d: keyword-only
    pass
```

---

## 7. Exception Handling

```python
try:
    x = 10 / 0
except ZeroDivisionError as e:
    print("Cannot divide by zero:", e)
except (TypeError, ValueError) as e:
    print("Multiple exception types:", e)
else:
    print("Runs only if NO exception was raised")
finally:
    print("Always executed")

# Custom exceptions
class MyError(Exception):
    pass

raise MyError("something went wrong")

# Common built-in exceptions to know:
# ValueError, TypeError, KeyError, IndexError, AttributeError,
# FileNotFoundError, ZeroDivisionError, StopIteration
```

---

## 8. File Handling

```python
with open("file.txt", "r") as f:
    data = f.read()          # whole file as one string

with open("file.txt", "r") as f:
    lines = f.readlines()      # list of lines (keeps \n)

with open("file.txt", "r") as f:
    for line in f:               # memory-efficient line-by-line iteration
        print(line.strip())

with open("out.txt", "w") as f:
    f.write("hello\n")
    f.writelines(["a\n", "b\n"])

with open("out.txt", "a") as f:      # append mode
    f.write("more\n")
```

```text
r   # Read (default)
w   # Write (truncates existing file!)
a   # Append
x   # Create (fails if file exists)
b   # Binary mode, e.g. "rb", "wb"
```

---

## 9. Built-in Functions

```python
len([1, 2, 3])                       # 3
type(5)                                # <class 'int'>
id(x)                                    # memory address (identity)

min([3, 1, 2])                            # 1
max([3, 1, 2])                              # 3
max([3, 1, 2], key=lambda x: -x)              # 1 (min via custom key)
sum([1, 2, 3])                                 # 6
sum([1, 2, 3], 10)                              # 16 (start value)

sorted([3, 1, 2])                                # [1, 2, 3]
sorted([3, 1, 2], reverse=True)                    # [3, 2, 1]
sorted(["bb", "a", "ccc"], key=len)                  # ['a', 'bb', 'ccc']
reversed([1, 2, 3])                                    # iterator -> list() to see [3,2,1]

range(5)                                                 # 0..4
range(2, 10, 2)                                            # 2,4,6,8

enumerate(["a", "b"])                                        # (0,'a'), (1,'b')
enumerate(["a", "b"], start=1)                                  # (1,'a'), (2,'b')

list(zip([1, 2], ["x", "y"]))                                     # [(1,'x'), (2,'y')]
list(zip([1,2,3], ["x","y"]))                                       # stops at shortest: [(1,'x'),(2,'y')]

list(map(lambda x: x * 2, [1, 2, 3]))                                 # [2, 4, 6]
list(filter(lambda x: x % 2 == 0, range(10)))                          # [0,2,4,6,8]

any([False, True, False])                                                 # True
all([True, True, False])                                                    # False

isinstance(5, int)                                                            # True
callable(print)                                                                 # True
```

---

## 10. Slicing

```python
text = "Python"

text[0]        # 'P'
text[-1]        # 'n'  (last char)
text[1:4]        # 'yth'
text[:3]           # 'Pyt'
text[3:]            # 'hon'
text[::2]             # 'Pto'   every 2nd char
text[::-1]              # 'nohtyP'  reverse

nums = [0, 1, 2, 3, 4, 5]
nums[1:4]                    # [1, 2, 3]
nums[:-1]                     # all except last -> [0,1,2,3,4]
nums[::2]                      # every other -> [0,2,4]
nums[::-1]                       # reversed -> [5,4,3,2,1,0]
nums[1:4] = [9, 9]                 # slice assignment -> [0, 9, 9, 4, 5]
del nums[1:3]                        # delete a slice
```

---

## 11. OOP

```python
class Employee:
    company = "Acme"          # class attribute (shared)

    def __init__(self, name, salary):
        self.name = name        # instance attribute
        self.salary = salary

    def greet(self):
        return f"Hello {self.name}"

    def __repr__(self):          # used by repr()/debugger, should be unambiguous
        return f"Employee({self.name!r}, {self.salary})"

    def __str__(self):            # used by print()/str(), should be readable
        return f"{self.name} (${self.salary})"

    def __eq__(self, other):        # customize == behavior
        return self.name == other.name

class Developer(Employee):            # inheritance
    def __init__(self, name, salary, language):
        super().__init__(name, salary)   # call parent constructor
        self.language = language

    def greet(self):                      # method override
        return f"{super().greet()}, I code in {self.language}"

# Properties (getter/setter without explicit method calls)
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def area(self):
        return 3.14159 * self._radius ** 2

c = Circle(5)
c.area          # accessed like an attribute, computed on the fly

# Static & class methods
class Util:
    @staticmethod
    def add(a, b):           # no self/cls, just a namespaced function
        return a + b

    @classmethod
    def create_default(cls):   # receives the class itself
        return cls(name="default")

# Dataclasses (3.7+) — auto-generates __init__, __repr__, __eq__
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

p = Point(1, 2)   # __init__, __repr__ generated for you
```

---

## 12. Pythonic Idioms & Tricks

```python
# Swap variables (no temp var needed)
a, b = 1, 2
a, b = b, a

# Multiple assignment
x, y, z = 1, 2, 3

# Extended unpacking
first, *middle, last = [1, 2, 3, 4, 5]     # first=1, middle=[2,3,4], last=5
first, *rest = [1, 2, 3]                     # first=1, rest=[2,3]

# Chained comparisons
1 < x < 10          # equivalent to (1 < x) and (x < 10)

# Ternary / conditional expression
val = "even" if x % 2 == 0 else "odd"

# Membership check is O(1) on sets/dicts, O(n) on lists — use a set for repeated lookups
big_list = list(range(100000))
big_set = set(big_list)
99999 in big_set        # fast
99999 in big_list         # slow

# Counting with Counter
from collections import Counter
Counter("mississippi")        # Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})
Counter([1,1,2,3]).most_common(1)   # [(1, 2)]

# Default values without KeyError
from collections import defaultdict
d = defaultdict(list)
d["missing"].append(1)          # no KeyError, auto-creates []

# Grouping / merging dict-of-dicts patterns
from collections import ChainMap

# Named tuples — lightweight, immutable, readable records
from collections import namedtuple
Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
p.x, p.y            # 1, 2

# itertools essentials
import itertools
list(itertools.chain([1,2], [3,4]))              # [1,2,3,4] flatten iterables
list(itertools.combinations([1,2,3], 2))            # [(1,2),(1,3),(2,3)]
list(itertools.permutations([1,2], 2))                # [(1,2),(2,1)]
list(itertools.product([0,1], repeat=2))                # [(0,0),(0,1),(1,0),(1,1)]
list(itertools.groupby("aaabbc"))                          # groups consecutive equal items

# Flatten a list of lists (one-liner)
flat = [x for sub in [[1,2],[3,4]] for x in sub]

# Remove duplicates while preserving order
seen = set()
deduped = [x for x in [3,1,2,3,1] if not (x in seen or seen.add(x))]

# Merge two lists into a dict
keys, vals = ["a","b"], [1,2]
dict(zip(keys, vals))          # {'a': 1, 'b': 2}

# List of dicts -> sort by a field
people = [{"name": "B", "age": 30}, {"name": "A", "age": 25}]
sorted(people, key=lambda p: p["age"])

# Getting index + value together
for i, val in enumerate(["a", "b", "c"]):
    print(i, val)

# Star-unpacking function args
def add3(a, b, c): return a + b + c
args = [1, 2, 3]
add3(*args)              # unpack list as positional args

kwargs = {"a": 1, "b": 2, "c": 3}
add3(**kwargs)             # unpack dict as keyword args

# Context managers (custom, via contextlib)
from contextlib import contextmanager

@contextmanager
def managed_resource():
    print("acquire")
    yield "resource"
    print("release")

with managed_resource() as r:
    print(r)

# Generators — memory-efficient lazy sequences
def gen_squares(n):
    for i in range(n):
        yield i * i

g = gen_squares(5)
next(g)          # 0
list(g)            # remaining: [1, 4, 9, 16]

# Generator expression (like list comp but lazy, uses () not [])
sum(x*x for x in range(1000000))    # doesn't build the full list in memory

# String multiplication / repetition
"ab" * 3           # 'ababab'
[0] * 5              # [0, 0, 0, 0, 0]  (careful with mutable objects, see gotcha below)

# GOTCHA: don't do this for lists of mutable objects
rows = [[0] * 3] * 3     # all 3 inner lists are the SAME object!
rows[0][0] = 1              # changes every row -> [[1,0,0],[1,0,0],[1,0,0]]
# correct way:
rows = [[0] * 3 for _ in range(3)]

# GOTCHA: mutable default arguments
def bad(x, lst=[]):        # default list is created ONCE, shared across calls
    lst.append(x)
    return lst

def good(x, lst=None):
    if lst is None:
        lst = []
    lst.append(x)
    return lst

# Shallow vs deep copy
import copy
a = [[1, 2], [3, 4]]
b = a.copy()                 # shallow: inner lists still shared
c = copy.deepcopy(a)           # deep: fully independent

# Truthy / falsy values (all "empty" things are falsy)
bool([])        # False
bool({})         # False
bool("")          # False
bool(0)            # False
bool(None)           # False

# One-liner file line count
with open("file.txt") as f:
    count = sum(1 for _ in f)
```

---

## 13. Standard Library Quick Hits

```python
import math
math.sqrt(16)         # 4.0
math.floor(3.7)         # 3
math.ceil(3.2)            # 4
math.gcd(12, 18)            # 6
math.inf                      # infinity value

import random
random.randint(1, 10)              # inclusive both ends
random.choice([1, 2, 3])              # random single element
random.sample([1,2,3,4], 2)             # random unique subset
random.shuffle(my_list)                   # shuffles in place

import datetime
datetime.datetime.now()                     # current datetime
datetime.date.today()                         # current date
d = datetime.date(2024, 1, 1)
d.strftime("%Y-%m-%d")                          # format -> '2024-01-01'
datetime.datetime.strptime("2024-01-01", "%Y-%m-%d")   # parse string -> datetime

import os
os.getcwd()                       # current working directory
os.listdir(".")                     # list files in dir
os.path.join("a", "b.txt")            # OS-safe path join -> 'a/b.txt'
os.path.exists("file.txt")              # bool
os.environ.get("HOME")                    # env var

import sys
sys.argv                           # list of CLI args
sys.exit(1)                          # exit with status code

import json
json.dumps({"name": "Shubham"})           # dict -> JSON string
json.loads('{"name": "Shubham"}')           # JSON string -> dict
json.dump(obj, open("f.json", "w"))           # write to file
json.load(open("f.json"))                       # read from file

import re
re.match(r"\d+", "123abc")              # match at start
re.search(r"\d+", "abc123")               # search anywhere
re.findall(r"\d+", "a1b22c333")             # ['1', '22', '333']
re.sub(r"\d+", "#", "a1b22c333")              # 'a#b#c#'
```

---

## Cheat Sheet Summary

```text
str    -> upper(), lower(), split(), join(), replace(), strip(), find()
list   -> append(), extend(), insert(), pop(), sort(), slicing
tuple  -> count(), index(), unpacking
set    -> add(), update(), remove(), union(), intersection()
dict   -> get(), keys(), values(), items(), update(), setdefault()

if / elif / else            walrus :=
for / while                  break / continue / for-else
def / lambda                   *args / **kwargs / type hints
try / except / else / finally    custom exceptions
class / inheritance                @property, @staticmethod, @classmethod, dataclass

range(), enumerate(), zip()
map(), filter(), sorted(key=...)
len(), min(), max(), sum(), any(), all()

collections: Counter, defaultdict, namedtuple
itertools: chain, combinations, permutations, product, groupby
generators: yield, generator expressions
gotchas: mutable default args, shallow vs deep copy, [[0]*n]*n aliasing
```
