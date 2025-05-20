# 🧠 C++ OOP, STL, Templates, `friend`, and Operator Overloading Cheatsheet

---

## 📦 Table of Contents

1. [Access Specifiers: `public`, `private`, `protected`](#access-specifiers)
2. [Classes, Objects, and Encapsulation](#classes-and-objects)
3. [Inheritance & Access Control](#inheritance)
4. [Virtual Functions & Polymorphism](#virtual-functions)
5. [Abstract Classes & Interfaces](#abstract-classes)
6. [Constructor, Destructor, and Copying](#constructor-destructor-copy)
7. [Friend Functions and Classes](#friend)
8. [Operator Overloading](#operator-overloading)
9. [Do’s and Don’ts](#dos-and-donts)
10. [STL & Templates (Overview)](#stl-templates)

---

## 🔐 Access Specifiers

| Specifier   | Accessible From               |
|-------------|-------------------------------|
| `public`    | Anywhere                      |
| `private`   | Inside the class only         |
| `protected` | Class & derived classes       |

---

## 🧱 Classes and Objects <a name="classes-and-objects"></a>

```cpp
class BankAccount {
private:
    double balance;

public:
    BankAccount() : balance(0) {}

    void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    double getBalance() const {
        return balance;
    }
};
```

### 🔎 Why `const` after method?

It means `getBalance()` **won’t modify any member variables**. Safe to call on `const` objects.

---

## 👪 Inheritance <a name="inheritance"></a>

```cpp
class Person {
protected:
    std::string name;
public:
    Person(std::string n) : name(n) {}
    void greet() { std::cout << "Hi " << name << "\n"; }
};

class Student : public Person {
private:
    int id;
public:
    Student(std::string n, int i) : Person(n), id(i) {}
    void show() { std::cout << name << " (" << id << ")\n"; }
};
```

- `public` inheritance: retains access levels.
- `protected`: public becomes protected.
- `private`: all inherited stuff becomes private.

---

## 🔁 Virtual Functions <a name="virtual-functions"></a>

Used for **runtime polymorphism** via base class pointers/references.

```cpp
class Animal {
public:
    virtual void sound() const {
        std::cout << "Some animal\n";
    }
};

class Dog : public Animal {
public:
    void sound() const override {
        std::cout << "Woof!\n";
    }
};

void makeSound(const Animal& a) {
    a.sound(); // 🧠 Calls derived's `sound()` if virtual
}
```

- Use `override` to ensure overriding.
- Make destructor `virtual` if base class has virtual methods!

---

## 🧩 Abstract Classes and Interfaces <a name="abstract-classes"></a>

```cpp
class Shape {
public:
    virtual double area() const = 0; // Pure virtual = abstract
    virtual ~Shape() = default;
};
```

- Cannot instantiate abstract classes.
- Acts like interfaces.
- Derived class must **implement all pure virtuals**.

---

## 🏗️ Constructor, Destructor, and Copying <a name="constructor-destructor-copy"></a>

```cpp
class MyClass {
private:
    int* data;

public:
    MyClass(int value) {
        data = new int(value);
    }

    // Rule of Three
    ~MyClass() { delete data; }

    MyClass(const MyClass& other) {
        data = new int(*other.data); // Deep copy
    }

    MyClass& operator=(const MyClass& other) {
        if (this != &other) {
            delete data;
            data = new int(*other.data);
        }
        return *this;
    }
};
```

---

## 🧑‍🤝‍🧑 `friend` Keyword <a name="friend"></a>

Gives external functions or classes access to **private/protected** members.

### 📌 Friend Function

```cpp
class BankAccount {
private:
    double balance;

public:
    BankAccount(double b) : balance(b) {}
    friend void showBalance(const BankAccount&);
};

void showBalance(const BankAccount& a) {
    std::cout << "Balance: " << a.balance << "\n";
}
```

### 📌 Friend Class

```cpp
class Secret;

class Hacker {
public:
    void steal(const Secret& s);
};

class Secret {
private:
    std::string password = "top_secret";
    friend class Hacker;
};

void Hacker::steal(const Secret& s) {
    std::cout << s.password;
}
```

### ✅ Notes
- Friendship is **not mutual**.
- It is **not inherited**.
- Use only if absolutely necessary!

---

## ➕ Operator Overloading <a name="operator-overloading"></a>

Allows natural use of operators with custom types.

### 🧱 Member Function Syntax

```cpp
class Point {
public:
    int x, y;
    Point(int x, int y) : x(x), y(y) {}

    Point operator+(const Point& other) const {
        return Point(x + other.x, y + other.y);
    }

    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};
```

### 🧾 Friend Overload for `<<`

```cpp
friend std::ostream& operator<<(std::ostream& os, const Point& p) {
    os << "(" << p.x << ", " << p.y << ")";
    return os;
}
```

### 🧪 Full Example

```cpp
class Complex {
private:
    double real, imag;

public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}

    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }

    friend std::ostream& operator<<(std::ostream& os, const Complex& c) {
        os << c.real << " + " << c.imag << "i";
        return os;
    }
};
```

---

## ✅ Do’s and Don’ts <a name="dos-and-donts"></a>

### ✅ Do:

- Use `const` for getters and read-only methods.
- Mark base class destructors as `virtual` if polymorphism is involved.
- Prefer member operator overloads unless symmetric or needs access → use `friend`.
- Encapsulate data with access specifiers.
- Use `override` for safety.

### ❌ Don't:

- Overuse `friend` — it breaks encapsulation.
- Overload operators unnecessarily (don’t force it).
- Forget to define the **Rule of Three** when using raw pointers.
- Use raw `new`/`delete` if you can use smart pointers (`std::unique_ptr`).

---

## 📚 STL & Templates (Overview) <a name="stl-templates"></a>

### Templates

```cpp
template<typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}
```

### STL Containers

- `std::vector<T>` – Dynamic array
- `std::map<Key, Value>` – Key-value dictionary
- `std::set<T>` – Unique sorted elements
- `std::stack<T>` / `std::queue<T>` – LIFO/FIFO containers

---

## 📌 Useful Keywords Reference

| Keyword     | Purpose                        |
|-------------|--------------------------------|
| `override`  | Explicitly override base method |
| `virtual`   | Enables polymorphism            |
| `friend`    | Allows external access          |
| `const`     | Prevents mutation               |
| `mutable`   | Allows mutation in const method |
| `final`     | Prevents further overriding     |
| `static`    | Shared across all objects       |

---

Made with ❤️ for your C++ exam prep.
