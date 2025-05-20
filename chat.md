# ✅ C++ OOP, STL, and Templates Cheatsheet

> A comprehensive summary for mastering Object-Oriented Programming concepts, STL usage, and Template programming in modern C++.

---

## 🧠 Table of Contents
1. [Classes and Access Specifiers](#1-classes-and-access-specifiers)
2. [Encapsulation: public / private / protected](#2-encapsulation-public--private--protected)
3. [Constructor, Destructor, Copy Constructor](#3-constructor-destructor-copy-constructor)
4. [Inheritance & `protected`](#4-inheritance--protected)
5. [Polymorphism & Virtual Functions](#5-polymorphism--virtual-functions)
6. [Abstract Classes & Interfaces](#6-abstract-classes--interfaces)
7. [Operator Overloading (`surcharge des opérateurs`)](#7-operator-overloading-surcharge-des-opérateurs)
8. [Friend Classes and Functions](#8-friend-classes-and-functions)
9. [The `this` Pointer](#9-the-this-pointer)
10. [STL Containers and Algorithms](#10-stl-containers-and-algorithms)
11. [Templates: Function & Class Templates](#11-templates-function--class-templates)
12. [Best Practices & Gotchas](#12-best-practices--gotchas)

---

## 1. Classes and Access Specifiers

```cpp
class MyClass {
public:
    int publicVar;
    void publicMethod();

private:
    int privateVar;

protected:
    int protectedVar;
};
```

- `public`: Accessible from anywhere.
- `private`: Accessible only inside the class.
- `protected`: Accessible inside the class and derived classes.

---

## 2. Encapsulation: `public` / `private` / `protected`

Encapsulation protects the internal state and exposes only what’s necessary.

```cpp
class BankAccount {
private:
    double balance;

public:
    void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    double getBalance() const {
        return balance;
    }
};
```

✅ Do: Use getters and setters  
🚫 Don’t: Expose member variables as public

---

## 3. Constructor, Destructor, Copy Constructor

```cpp
class Person {
public:
    string name;

    Person(string n) : name(n) {}              // Constructor
    Person(const Person &p) : name(p.name) {}  // Copy constructor
    ~Person() {}                               // Destructor
};
```

---

## 4. Inheritance & `protected`

```cpp
class Animal {
protected:
    string name;

public:
    void speak() { cout << "Animal sound\n"; }
};

class Dog : public Animal {
public:
    void speak() { cout << name << " says Woof!\n"; }
};
```

- `public` inheritance keeps `public` and `protected` members intact.
- `protected` inheritance demotes `public` to `protected`.
- `private` inheritance hides everything.

---

## 5. Polymorphism & Virtual Functions

```cpp
class Shape {
public:
    virtual void draw() { cout << "Shape\n"; }
};

class Circle : public Shape {
public:
    void draw() override { cout << "Circle\n"; }
};
```

- `virtual`: Enables runtime polymorphism.
- `override`: Ensures the method is overriding a base class method.

Use **pointers or references** for polymorphism to work:

```cpp
Shape* s = new Circle();
s->draw(); // Circle
```

---

## 6. Abstract Classes & Interfaces

An abstract class has at least one **pure virtual** function.

```cpp
class Drawable {
public:
    virtual void draw() = 0; // Pure virtual
};
```

- You **cannot instantiate** an abstract class.
- A class with all pure virtuals = **Interface** in C++.

---

## 7. Operator Overloading (`surcharge des opérateurs`)

Syntax:

```cpp
class Vector {
    int x, y;

public:
    Vector(int a, int b) : x(a), y(b) {}

    Vector operator+(const Vector& other) {
        return Vector(x + other.x, y + other.y);
    }

    friend ostream& operator<<(ostream& os, const Vector& v) {
        return os << "(" << v.x << ", " << v.y << ")";
    }
};
```

### Common Operators to Overload:
- `+`, `-`, `*`, `/`
- `==`, `!=`, `<`, `>`
- `<<`, `>>` (streaming)

---

## 8. Friend Classes and Functions

```cpp
class Box;

class Display {
public:
    void show(const Box& b);
};

class Box {
    int width;

public:
    Box(int w) : width(w) {}

    friend void Display::show(const Box& b);
};

void Display::show(const Box& b) {
    cout << "Width: " << b.width << "\n";
}
```

Use `friend` when you **must access private members**, but **use with caution**.

---

## 9. The `this` Pointer

`this` refers to the current object instance.

```cpp
class Counter {
    int value;

public:
    Counter& increment() {
        ++value;
        return *this; // for chaining
    }
};
```

---

## 10. STL Containers and Algorithms

### Containers:
- `vector<T>`: Dynamic array
- `map<K, V>`: Key-value pairs (sorted)
- `unordered_map<K, V>`: Hash map
- `set<T>`: Unique sorted values
- `stack<T>`, `queue<T>`, `priority_queue<T>`

### Algorithms:

```cpp
#include <algorithm>

vector<int> v = {1, 3, 2};
sort(v.begin(), v.end());          // sort
reverse(v.begin(), v.end());       // reverse
auto it = find(v.begin(), v.end(), 2); // search
```

---

## 11. Templates: Function & Class Templates

### Function Template

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

### Class Template

```cpp
template<typename T>
class Box {
    T value;

public:
    Box(T v) : value(v) {}
    T get() const { return value; }
};
```

✅ Use templates to generalize code  
🚫 Don’t overuse—prefer STL when possible

---

## 12. Best Practices & Gotchas

### ✅ DO
- Use `override` for virtual methods
- Always `delete` or use smart pointers
- Prefer composition over inheritance
- Use initializer lists in constructors
- Keep destructors `virtual` in polymorphic base classes

### 🚫 DON’T
- Slice objects (avoid passing by value in inheritance)
- Use raw `new/delete` — prefer `std::unique_ptr`, `std::shared_ptr`
- Overload operators unnecessarily
- Make data members public

---

## 📌 Mini Project Example

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void makeSound() const = 0; // Abstract
    virtual ~Animal() {}
};

class Cat : public Animal {
public:
    void makeSound() const override {
        cout << "Meow\n";
    }
};

class Dog : public Animal {
public:
    void makeSound() const override {
        cout << "Woof\n";
    }
};

void speak(const Animal& a) {
    a.makeSound();
}

int main() {
    Cat c;
    Dog d;

    speak(c); // Meow
    speak(d); // Woof

    return 0;
}
```

---

## 📚 Final Notes

- **OOP is about thinking in objects and behaviors**, not just syntax.
- Understand the **why** of access control, encapsulation, and design.
- Be ready for **tricky syntax questions** (operator overloads, templates, etc.)
- Practicing small examples will help you solidify the ideas.

---
