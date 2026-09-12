# Python Fundamentals Quick Notes

## 1. Python Basics

```python
# Single-line comment

"""
Multi-line comment
"""

print("Hello, World!")

# Variables
name = "Shubham"
age = 30
salary = 50000.50
is_active = True
```

### Naming Rules

```python
user_name = "John"      # Valid
_user = "test"          # Valid
user1 = "abc"           # Valid

# 1user = "abc"         # Invalid
# user-name = "abc"     # Invalid
```



## 2. Data Types

### Numeric Types

```python
x = 10          # int
y = 10.5        # float
z = 3 + 4j      # complex
```

#### Common Numeric Functions

```python
abs(-10)          # 10
round(3.1415, 2)  # 3.14
pow(2, 3)         # 8
```

### String (`str`)

```python
name = "Python"
```

#### Common String Methods

```python
name.upper()          # PYTHON
name.lower()          # python
name.capitalize()     # Python
name.title()          # Python

name.startswith("Py")
name.endswith("on")

name.replace("Py", "Cy")
name.split("t")
"-".join(["a", "b"])

name.find("th")
name.count("o")

name.strip()
name.lstrip()
name.rstrip()
```

#### String Formatting

```python
name = "Shubham"
age = 30

f"{name} is {age} years old"
"{} is {}".format(name, age)
```

### List (`list`)

```python
nums = [1, 2, 3]
```

#### Common List Methods

```python
nums.append(4)
nums.extend([5, 6])
nums.insert(1, 10)

nums.remove(10)
nums.pop()
nums.clear()

nums.index(2)
nums.count(2)

nums.sort()
nums.reverse()
nums.copy()
```

#### List Comprehension

```python
squares = [x * x for x in range(5)]

evens = [x for x in range(10) if x % 2 == 0]
```

### Tuple (`tuple`)

```python
point = (10, 20)
```

#### Common Tuple Methods

```python
point.count(10)
point.index(20)
```

#### Unpacking

```python
x, y = point
```

### Set (`set`)

```python
nums = {1, 2, 3}
```

#### Common Set Methods

```python
nums.add(4)
nums.update([5, 6])

nums.remove(2)
nums.discard(10)

nums.pop()
nums.clear()
```

#### Set Operations

```python
a = {1, 2, 3}
b = {3, 4, 5}

a | b    # Union
a & b    # Intersection
a - b    # Difference
a ^ b    # Symmetric Difference
```

### Dictionary (`dict`)

```python
user = {
    "name": "Shubham",
    "age": 30
}
```

#### Common Dictionary Methods

```python
user.get("name")
user.keys()
user.values()
user.items()

user.update({"city": "Noida"})
user.pop("age")

user.setdefault("country", "India")
user.clear()
user.copy()
```

#### Dictionary Comprehension

```python
squares = {x: x * x for x in range(5)}
```



## 3. Operators

### Arithmetic

```python
+   -   *   /   //   %   **
```

```python
10 / 3    # 3.333
10 // 3   # 3
10 % 3    # 1
10 ** 2   # 100
```

### Comparison

```python
==  !=  >  <  >=  <=
```

### Logical

```python
and
or
not
```

### Membership

```python
in
not in
```

### Identity

```python
is
is not
```



## 4. Conditional Statements

```python
age = 18

if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")
```

### Ternary Operator

```python
status = "Adult" if age >= 18 else "Minor"
```



## 5. Loops

### For Loop

```python
for i in range(5):
    print(i)
```

### While Loop

```python
count = 0

while count < 5:
    count += 1
```

### Loop Controls

```python
break
continue
pass
```



## 6. Functions

```python
def greet(name):
    return f"Hello {name}"

greet("Shubham")
```

### Default Arguments

```python
def greet(name="Guest"):
    return f"Hello {name}"
```

### `*args` and `**kwargs`

```python
def func(*args, **kwargs):
    pass
```

### Lambda Function

```python
square = lambda x: x * x
```



## 7. Exception Handling

```python
try:
    x = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
finally:
    print("Always executed")
```



## 8. File Handling

```python
with open("file.txt", "r") as f:
    data = f.read()
```

### File Modes

```python
r   # Read
w   # Write
a   # Append
x   # Create
b   # Binary
```



## 9. Useful Built-in Functions

```python
len()
type()
id()

min()
max()
sum()

sorted()
reversed()

range()
enumerate()
zip()

map()
filter()

any()
all()
```

### Examples

```python
len([1, 2, 3])

enumerate(["a", "b"])

list(zip([1, 2], ["x", "y"]))
```



## 10. Slicing

```python
text = "Python"

text[0]      # P
text[-1]     # n

text[1:4]    # yth
text[:3]
text[::2]
text[::-1]
```



## 11. OOP Syntax

```python
class Employee:
    def __init__(self, name):
        self.name = name

    def greet(self):
        return f"Hello {self.name}"

emp = Employee("Shubham")
emp.greet()
```

### Inheritance

```python
class Developer(Employee):
    pass
```



## 12. Common Pythonic Syntax

```python
# Swap variables
a, b = b, a

# Multiple assignment
x, y, z = 1, 2, 3

# Membership
if "a" in "apple":
    pass

# Enumerate
for idx, value in enumerate(items):
    pass

# Dictionary iteration
for key, value in user.items():
    pass

# List unpacking
first, *middle, last = numbers
```



## 13. Frequently Used Standard Libraries

```python
import math
import random
import datetime
import os
import sys
import json
import collections
```

### Examples

```python
math.sqrt(16)

random.randint(1, 10)

datetime.datetime.now()

json.dumps({"name": "Shubham"})
```



# Python Cheat Sheet Summary

```text
str    -> upper(), lower(), split(), join(), replace()
list   -> append(), extend(), insert(), pop(), sort()
tuple  -> count(), index()
set    -> add(), update(), remove(), union(), intersection()
dict   -> get(), keys(), values(), items(), update()

if / elif / else
for / while
def / lambda
try / except
class / inheritance

range(), enumerate(), zip()
map(), filter()
len(), min(), max(), sum()
```

> Covers the most commonly used Python fundamentals, data types, methods, operators, control flow, functions, OOP, and built-in utilities used in day-to-day development and coding interviews.