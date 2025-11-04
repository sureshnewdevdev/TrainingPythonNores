# 🐍 Python Basics Interview Questions and Answers

---

## **1. What is Python?**
**Answer:**  
Python is a high-level, interpreted, and object-oriented programming language with dynamic semantics. It emphasizes code readability and supports multiple paradigms such as procedural, functional, and object-oriented programming.

---

## **2. What are the key features of Python?**
**Answer:**
- Easy to learn and read  
- Interpreted and dynamically typed  
- Extensive standard library  
- Cross-platform support  
- Supports OOP and functional programming  
- Strong community support  

---

## **3. What is the difference between Python 2 and Python 3?**
| Feature | Python 2 | Python 3 |
|----------|-----------|-----------|
| Print statement | `print "Hello"` | `print("Hello")` |
| Division | 5/2 = 2 | 5/2 = 2.5 |
| Unicode | ASCII by default | Unicode by default |
| Support | Discontinued | Actively supported |

---

## **4. What are Python data types?**
**Answer:**  
- Numeric (int, float, complex)  
- Sequence (list, tuple, range)  
- Text (str)  
- Set (set, frozenset)  
- Mapping (dict)  
- Boolean (bool)  

---

## **5. What is a variable in Python?**
**Answer:**  
A variable is a name that references a memory location where data is stored.
```python
x = 10
name = "Gopi"
```

---

## **6. What is type casting in Python?**
**Answer:**  
Converting one data type to another.  
```python
x = int("10")
y = float(20)
z = str(30)
```

---

## **7. What are lists in Python?**
**Answer:**  
A list is an ordered, mutable collection of elements.
```python
fruits = ["apple", "banana", "mango"]
fruits.append("orange")
print(fruits)
```

---

## **8. What is the difference between list and tuple?**
| Feature | List | Tuple |
|----------|-------|--------|
| Mutability | Mutable | Immutable |
| Syntax | `[]` | `()` |
| Speed | Slower | Faster |

---

## **9. What are dictionaries in Python?**
**Answer:**  
A dictionary stores data in key-value pairs.  
```python
person = {"name": "Gopi", "age": 28}
print(person["name"])
```

---

## **10. What is the use of indentation in Python?**
**Answer:**  
Indentation defines code blocks (e.g., inside loops, functions, and classes).  
```python
if True:
    print("Indented block")
```

---

## **11. What are Python operators?**
**Answer:**  
- Arithmetic (`+`, `-`, `*`, `/`, `%`)  
- Comparison (`==`, `!=`, `>`, `<`)  
- Logical (`and`, `or`, `not`)  
- Assignment (`=`, `+=`, `-=`)  
- Membership (`in`, `not in`)  
- Identity (`is`, `is not`)  

---

## **12. What is a function in Python?**
**Answer:**  
A block of code that performs a specific task and can be reused.
```python
def greet(name):
    return "Hello, " + name

print(greet("Gopi"))
```

---

## **13. What is the difference between return and print?**
**Answer:**  
- `return` sends a value back to the caller  
- `print` outputs to the console  

---

## **14. What are arguments and parameters?**
**Answer:**  
- **Parameter**: Variable defined in a function.  
- **Argument**: Actual value passed when calling the function.  
```python
def greet(name):  # name = parameter
    print("Hello", name)

greet("Gopi")  # "Gopi" = argument
```

---

## **15. What are *args and **kwargs?**
**Answer:**  
They allow variable-length arguments in functions.  
```python
def example(*args, **kwargs):
    print(args)
    print(kwargs)
```

---

## **16. What are loops in Python?**
**Answer:**  
Used for iteration.  
```python
# for loop
for i in range(3):
    print(i)

# while loop
i = 0
while i < 3:
    print(i)
    i += 1
```

---

## **17. What is the difference between break, continue, and pass?**
| Statement | Description |
|------------|-------------|
| break | Exits the loop |
| continue | Skips current iteration |
| pass | Does nothing (placeholder) |

---

## **18. What are modules and packages?**
**Answer:**  
- **Module:** A single Python file (`math`, `os`, etc.)  
- **Package:** A collection of modules in a directory with `__init__.py`

---

## **19. What is exception handling?**
**Answer:**  
Used to handle runtime errors using `try`, `except`, `finally`.  
```python
try:
    print(10 / 0)
except ZeroDivisionError:
    print("Cannot divide by zero")
finally:
    print("Done")
```

---

## **20. What is a class and object in Python?**
**Answer:**  
- **Class:** Blueprint for creating objects.  
- **Object:** Instance of a class.  
```python
class Person:
    def __init__(self, name):
        self.name = name

p = Person("Gopi")
print(p.name)
```

---

## **21. What is inheritance in Python?**
**Answer:**  
Allows one class to derive properties of another.  
```python
class Animal:
    def sound(self):
        print("Animal Sound")

class Dog(Animal):
    def sound(self):
        print("Bark")

Dog().sound()
```

---

## **22. What is polymorphism?**
**Answer:**  
Different classes can have the same method name with different behavior.
```python
class Bird:
    def speak(self): print("Chirp")

class Dog:
    def speak(self): print("Bark")

for obj in [Bird(), Dog()]:
    obj.speak()
```

---

## **23. What are lambda functions?**
**Answer:**  
Anonymous functions using the `lambda` keyword.  
```python
add = lambda x, y: x + y
print(add(3, 4))
```

---

## **24. What are Python decorators?**
**Answer:**  
Used to modify the behavior of functions or methods.  
```python
def decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@decorator
def greet():
    print("Hello")

greet()
```

---

## **25. What is the difference between shallow and deep copy?**
**Answer:**
- **Shallow copy:** Creates a new object but references the same inner objects.  
- **Deep copy:** Creates a new object with recursively copied inner objects.  
```python
import copy
a = [[1, 2], [3, 4]]
b = copy.copy(a)     # shallow
c = copy.deepcopy(a) # deep
```

---

## **26. What are Python libraries used for data analysis?**
**Answer:**  
- `NumPy` – numerical computation  
- `Pandas` – data manipulation  
- `Matplotlib` – visualization  
- `Scikit-learn` – machine learning  

---

## **27. How is memory managed in Python?**
**Answer:**  
Python uses an automatic garbage collector to reclaim memory for objects no longer in use.

---

## **28. What is the difference between local and global variables?**
**Answer:**  
- **Local:** Defined inside a function.  
- **Global:** Defined outside all functions.  
```python
x = 10
def func():
    global x
    x = 20
```

---

## **29. What is list comprehension?**
**Answer:**  
A concise way to create lists.  
```python
squares = [x*x for x in range(5)]
print(squares)
```

---

## **30. What is the difference between `is` and `==`?**
**Answer:**
- `is`: checks object identity  
- `==`: checks value equality  
```python
a = [1, 2]
b = [1, 2]
print(a == b)  # True
print(a is b)  # False
```

