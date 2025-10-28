
# 🐍 Python Object-Oriented Programming (OOPs) – Detailed Notes

## 1️⃣ Introduction to OOP
Object-Oriented Programming (OOP) organizes code around **objects**.
Objects represent **real-world entities** and contain **data (attributes)** and **behavior (methods)**.

### ✅ Benefits
- Reusability
- Encapsulation
- Abstraction
- Modularity
- Maintainability

---

## 2️⃣ Basic OOP Terminology

| Term | Description | Example |
|------|--------------|----------|
| **Class** | Blueprint for objects | `class Car:` |
| **Object** | Instance of class | `car1 = Car()` |
| **Method** | Function inside class | `def start(self):` |
| **Attribute** | Variable inside class | `self.color = "Red"` |

---

## 3️⃣ Class and Object Example

```python
class Car:
    def __init__(self, brand, model):
        self.brand = brand
        self.model = model

    def show_details(self):
        print(f"Car Brand: {self.brand}, Model: {self.model}")

car1 = Car("Tesla", "Model S")
car1.show_details()
```
**Output:**
```
Car Brand: Tesla, Model: Model S
```

---

## 4️⃣ Class vs Instance Variables

```python
class Employee:
    company_name = "Infosys"
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary
```

| Type | Defined | Shared | Example |
|------|----------|---------|----------|
| Instance Variable | Inside constructor using `self` | ❌ No | `self.name` |
| Class Variable | Inside class directly | ✅ Yes | `company_name` |

---

## 5️⃣ Types of Methods

```python
class Student:
    school = "ItTechGenie Academy"

    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def show(self):                      # Instance Method
        print(f"Name: {self.name}, Marks: {self.marks}")

    @classmethod
    def school_info(cls):                # Class Method
        print(f"School Name: {cls.school}")

    @staticmethod
    def greet():                         # Static Method
        print("Welcome to ItTechGenie Python Course!")

Student.greet()
Student.school_info()
s1 = Student("Divya", 88)
s1.show()
```

---

## 6️⃣ Encapsulation

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # private variable

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(5000)
acc.deposit(2000)
print(acc.get_balance())   # ✅ 7000
print(acc.__balance)       # ❌ AttributeError
```

---

## 7️⃣ Inheritance

```python
class Vehicle:
    def start(self):
        print("Engine started")

class Car(Vehicle):
    def drive(self):
        print("Car is driving")

c = Car()
c.start()
c.drive()
```

### Types of Inheritance
- Single
- Multilevel
- Hierarchical
- Multiple

---

## 8️⃣ Polymorphism

```python
class Animal:
    def sound(self):
        print("Some generic sound")

class Dog(Animal):
    def sound(self):
        print("Bark!")

class Cat(Animal):
    def sound(self):
        print("Meow!")

for a in [Dog(), Cat(), Animal()]:
    a.sound()
```

**Output:**
```
Bark!
Meow!
Some generic sound
```

---

## 9️⃣ Abstraction

```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass

class CreditCard(Payment):
    def process_payment(self, amount):
        print(f"Processing Credit Card payment of ₹{amount}")

pay = CreditCard()
pay.process_payment(5000)
```

---

## 🔟 Constructor & Destructor

```python
class Student:
    def __init__(self, name):
        self.name = name
        print(f"{self.name} object created")

    def __del__(self):
        print(f"{self.name} object destroyed")

s1 = Student("Rahul")
del s1
```

---

## 🏦 Real-world Example – Bank System

```python
class Account:
    def __init__(self, acc_no, name, balance=0):
        self.acc_no = acc_no
        self.name = name
        self.__balance = balance

    def deposit(self, amount):
        self.__balance += amount
        print(f"Deposited ₹{amount}")

    def withdraw(self, amount):
        if amount <= self.__balance:
            self.__balance -= amount
            print(f"Withdrawn ₹{amount}")
        else:
            print("Insufficient balance!")

    def check_balance(self):
        print(f"Available Balance: ₹{self.__balance}")

acc1 = Account(12345, "Kavya", 10000)
acc1.deposit(2000)
acc1.withdraw(500)
acc1.check_balance()
```

**Output:**
```
Deposited ₹2000
Withdrawn ₹500
Available Balance: ₹11500
```

---

## 📚 Summary of OOP Concepts

| Concept | Description | Example |
|----------|--------------|----------|
| **Class** | Blueprint | `class Car:` |
| **Object** | Instance of class | `car1 = Car()` |
| **Encapsulation** | Data hiding | `__privateVar` |
| **Inheritance** | Reuse code | `class B(A)` |
| **Polymorphism** | Multiple behavior | Overriding |
| **Abstraction** | Hide details | `@abstractmethod` |

---

## 💡 Interview Questions

1. What is OOP? List 4 pillars.
2. Difference between `@staticmethod` and `@classmethod`?
3. Explain `self` in Python.
4. What is MRO (Method Resolution Order)?
5. Can we access private variables outside a class?

---
