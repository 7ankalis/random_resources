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






# STL Containers: Common Confusions and Access Methods

## Sequence Containers

### array
**Common Confusions:**
1. Not realizing size must be known at compile time
2. Confusing with C-style arrays (STL array knows its size)
3. Thinking bounds checking is automatic (only with `at()`)

**Access Methods:**
```cpp
std::array<int, 5> arr = {1,2,3,4,5};

// 1. Standard array access (no bounds checking)
int x = arr[2];  

// 2. Safe access with bounds checking
int y = arr.at(3);  // throws std::out_of_range if invalid

// 3. Front and back access
int first = arr.front();  // 1
int last = arr.back();    // 5

// 4. Iterator access
for(auto it = arr.begin(); it != arr.end(); ++it) {
    std::cout << *it << " ";
}













----
Dynamic array that can grow/shrink at runtime

Elements stored contiguously

Fast random access

Insertion/removal at end: O(1)

Insertion/removal elsewhere: O(n)



std::vector<int> vec = {1,2,3,4,5};

// 1. Standard index access
int x = vec[1];  // No bounds checking

// 2. Safe access
int y = vec.at(2);  // With bounds checking

// 3. Front/back access
int first = vec.front();
int last = vec.back();

// 4. Iterator access
for(auto it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << " ";
}

// 5. Range-based for loop (C++11+)
for(const auto& val : vec) {
    std::cout << val << " ";
}



---
Doubly linked list

No random access, only sequential

Fast insert/delete anywhere



std::list<int> lst = {1,2,3,4,5};

// Only sequential access possible
int first = lst.front();
int last = lst.back();

// Iterator access required
for(auto it = lst.begin(); it != lst.end(); ++it) {
    std::cout << *it << " ";
}

// Special list operations
lst.splice(lst.begin(), other_list);  // O(1)











---
Ordered, unique values

Based on balanced BST (Red-Black Tree)
std::set<int> s = {1,2,3,4,5};

// Find elements (returns iterator)
auto it = s.find(3);  // O(log n)
if(it != s.end()) {
    std::cout << *it;
}

// Count elements (1 for set, can be >1 for multiset)
size_t count = s.count(2);  // O(log n)

// Range-based access
for(const auto& val : s) {
    std::cout << val << " ";  // Sorted order
}

// Lower/upper bound
auto lb = s.lower_bound(3);  // First not less than 3
auto ub = s.upper_bound(4);  // First greater than 4

---


Key-value pairs

Keys are unique and ordered




std::map<std::string, int> m = {{"a",1}, {"b",2}};

// 1. Key access (creates if doesn't exist)
int x = m["a"];  // 1
int y = m["c"];   // Creates {"c",0}

// 2. Safe access
auto it = m.find("b");  // Returns iterator
if(it != m.end()) {
    std::cout << it->second;
}

// 3. Insert access
auto res = m.insert({"d", 4});  // Returns pair<iterator, bool>

// 4. Range-based access
for(const auto& [key, value] : m) {  // C++17 structured binding
    std::cout << key << ": " << value;
}










std::stack<int> s;
s.push(1);
s.push(2);

// Only top access
int top = s.top();  // 2
s.pop();            // Removes 2, returns void

// No iterators available



| Container         | Unique | Ordered | Structure       | Use Case                     |
|------------------|--------|---------|------------------|------------------------------|
| `vector`         | ✗      | ✓       | Dynamic Array    | Fast random access           |
| `deque`          | ✗      | ✓       | Double-ended     | Insert/delete both ends      |
| `list`           | ✗      | ✓       | Doubly Linked    | Frequent insert/delete       |
| `stack`          | ✗      | ✗       | LIFO             | Function calls               |
| `queue`          | ✗      | ✗       | FIFO             | BFS, scheduling              |
| `priority_queue` | ✗      | ✗       | Heap             | Greedy, Dijkstra             |
| `set`            | ✓      | ✓       | BST              | Unique sorted elements       |
| `multiset`       | ✗      | ✓       | BST              | Duplicates allowed           |
| `map`            | ✓      | ✓       | BST              | Key-value, sorted keys       |
| `multimap`       | ✗      | ✓       | BST              | Multiple values per key      |
| `unordered_set`  | ✓      | ✗       | Hash Table       | Fast lookup, no order        |
| `unordered_map`  | ✓      | ✗       | Hash Table       | Fast key-value lookup        |







