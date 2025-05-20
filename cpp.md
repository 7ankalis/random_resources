✅ C++ OOP, STL, and Templates Cheatsheet

A comprehensive, exam-focused guide for mastering Object-Oriented Programming, STL, and Template programming in modern C++. Designed for clarity, depth, and beauty, this cheatsheet covers key concepts, best practices, and potential exam pitfalls.


🧠 Table of Contents

Classes and Access Specifiers
Encapsulation: public / private / protected
Constructor, Destructor, Copy Constructor
Inheritance & protected
Polymorphism & Virtual Functions
Abstract Classes & Interfaces
Operator Overloading (surcharge des opérateurs)
Friend Classes and Functions
The this Pointer
STL Containers and Algorithms
Templates: Function & Class Templates
Best Practices & Gotchas
Mini Project Example
Final Notes & Exam Tips


1. Classes and Access Specifiers
Definition: A class defines a user-defined type, combining data (fields) and behavior (methods). Objects are instances of classes.
Syntax:
class MyClass {
public:
    int publicVar;           // Accessible anywhere
    void publicMethod();     // Public interface
private:
    int privateVar;          // Class-only access
protected:
    int protectedVar;        // Class and derived class access
};

Key Concepts:

Class vs. Struct: class defaults to private; struct defaults to public.
Access Specifiers:
public: Accessible from anywhere.
private: Accessible only within the class (default for class).
protected: Accessible in class and derived classes.



Example:
class Rectangle {
public:
    Rectangle(double w, double h) : width(w), height(h) {}
    double getArea() const { return width * height; }
private:
    double width, height;
};
Rectangle rect(5.0, 3.0);
cout << rect.getArea(); // Output: 15

Dos and Don’ts:

✅ Do: Use meaningful names for fields and methods.
❌ Don’t: Expose sensitive data as public.


2. Encapsulation: public / private / protected
Definition: Encapsulation hides internal state and exposes only necessary interfaces, ensuring data integrity.
Syntax:
class BankAccount {
private:
    double balance;
public:
    BankAccount(double b) : balance(b) {}
    void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
    double getBalance() const { return balance; }
};

Key Concepts:

Use private for data members to prevent direct access.
Provide public getters/setters with validation.
Use const for methods that don’t modify state.

Example:
BankAccount account(1000);
account.deposit(500);
cout << account.getBalance(); // Output: 1500

Dos and Don’ts:

✅ Do: Validate inputs in setters (e.g., amount > 0).
✅ Do: Use const for getter methods.
❌ Don’t: Make data members public without justification.


3. Constructor, Destructor, Copy Constructor
Definition:

Constructor: Initializes an object.
Destructor: Cleans up resources.
Copy Constructor: Creates a new object as a copy of another.
Move Constructor (C++11): Transfers resources for efficiency.

Syntax:
class Person {
private:
    string name;
    int* scores;
public:
    // Default constructor
    Person() : name("Unknown"), scores(new int[10]) {}
    // Parameterized constructor
    Person(string n, int size) : name(n), scores(new int[size]) {}
    // Copy constructor
    Person(const Person& other) : name(other.name), scores(new int[10]) {
        copy(other.scores, other.scores + 10, scores);
    }
    // Move constructor
    Person(Person&& other) noexcept : name(move(other.name)), scores(other.scores) {
        other.scores = nullptr;
    }
    // Destructor
    ~Person() { delete[] scores; }
};

Design Choices:

Use initializer lists for efficient initialization.
Implement copy/move constructors for classes managing dynamic resources.
Follow the Rule of Five for resource-owning classes.

Dos and Don’ts:

✅ Do: Use noexcept for move constructors.
✅ Do: Free resources in destructors.
❌ Don’t: Forget deep copies in copy constructors.


4. Inheritance & protected
Definition: Inheritance allows a derived class to inherit fields and methods from a base class, promoting code reuse.
Inheritance Types:



Type
Effect on Base Members



public
Public/protected members retain access


protected
Public/protected become protected


private
All members become private (rarely used)


Syntax:
class Animal {
protected:
    string name;
public:
    Animal(string n) : name(n) {}
    virtual void speak() { cout << name << " makes a sound.\n"; }
};
class Dog : public Animal {
public:
    Dog(string n) : Animal(n) {}
    void speak() override { cout << name << " says Woof!\n"; }
};

Design Choices:

Use public inheritance for "is-a" relationships (e.g., Dog is-an Animal).
Use composition for "has-a" relationships (e.g., Car has-an Engine).

Dos and Don’ts:

✅ Do: Initialize base class in derived constructor.
❌ Don’t: Inherit from classes not designed as base classes (e.g., STL containers).


5. Polymorphism & Virtual Functions
Definition: Polymorphism allows derived classes to override base class behavior, enabled by virtual functions.
Key Features:

Virtual: Declared with virtual for dynamic dispatch.
Override: Use override to ensure correct overriding.
Pure Virtual: = 0 makes a function abstract.

Syntax:
class Shape {
public:
    virtual void draw() const { cout << "Drawing Shape\n"; }
    virtual ~Shape() = default; // Virtual destructor
};
class Circle : public Shape {
public:
    void draw() const override { cout << "Drawing Circle\n"; }
};
Shape* s = new Circle();
s->draw(); // Output: Drawing Circle
delete s;

Design Choices:

Use virtual for methods expected to be overridden.
Always provide a virtual destructor in polymorphic base classes.

Dos and Don’ts:

✅ Do: Use final to prevent further overrides (C++11).
✅ Do: Use pointers/references for polymorphism.
❌ Don’t: Omit virtual destructors, causing undefined behavior.


6. Abstract Classes & Interfaces
Definition:

Abstract Class: Contains at least one pure virtual function; cannot be instantiated.
Interface: An abstract class with only pure virtual functions, no data.

Syntax:
class Drawable {
public:
    virtual void draw() const = 0; // Pure virtual
    virtual double area() const = 0;
    virtual ~Drawable() = default;
};
class Circle : public Drawable {
private:
    double radius;
public:
    Circle(double r) : radius(r) {}
    void draw() const override { cout << "Drawing Circle\n"; }
    double area() const override { return 3.14159 * radius * radius; }
};

Design Choices:

Use interfaces to define contracts (e.g., Drawable for shapes).
Keep interfaces minimal, focusing on behavior.

Dos and Don’ts:

✅ Do: Use const for interface methods.
❌ Don’t: Add data members to interfaces.


7. Operator Overloading (surcharge des opérateurs)
Definition: Redefine operators for user-defined types to provide intuitive behavior.
Types:

Member Functions: For operators where the left operand is the class (e.g., obj + obj).
Non-Member Functions: Often friend, for operators like << or when the left operand isn’t the class (e.g., scalar * obj).

Syntax:
class Complex {
private:
    double real, imag;
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    // Member: +
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    // Member: ==
    bool operator==(const Complex& other) const {
        return real == other.real && imag == other.imag;
    }
    // Friend: <<
    friend ostream& operator<<(ostream& os, const Complex& c) {
        os << c.real << " + " << c.imag << "i";
        return os;
    }
    // Friend: scalar * complex
    friend Complex operator*(double scalar, const Complex& c) {
        return Complex(scalar * c.real, scalar * c.imag);
    }
};
Complex a(1, 2), b(3, 4);
Complex c = a + b; // (4, 6)
cout << c; // Output: 4 + 6i
Complex d = 2.0 * a; // (2, 4)

Common Operators:



Category
Operators
Typical Return Type



Arithmetic
+, -, *, /, %
Class


Assignment
=, +=, -=, *=, /=
Class&


Comparison
==, !=, <, >, <=, >=
bool


Stream
<<, >>
ostream&, istream&


Subscript
[]
T&, const T&


Function Call
()
Varies


Design Choices:

Member vs. Non-Member:
Member: For +, =, where the left operand is the class.
Non-Member: For <<, >>, or scalar * obj.


Return Types:
Arithmetic: Return new object by value.
Assignment: Return *this by reference.
Stream: Return ostream& for chaining.


Const Correctness:
Mark member operators const.
Use const references for parameters.



Ambiguities and Exam Traps:

Precedence: Overloaded operators retain C++ precedence.
Symmetry: Define == and != together.
Chaining: Support cout << a << b with reference returns.
Implicit Conversions: Avoid ambiguity (e.g., Complex + double).

Dos and Don’ts:

✅ Do: Make operators intuitive.
✅ Do: Handle self-assignment in operator=.
❌ Don’t: Overload &&, ||, , (alters semantics).
❌ Don’t: Modify operands in arithmetic operators.


8. Friend Classes and Functions
Definition: friend grants access to private/protected members for specific functions or classes.
Use Cases:

Operator overloading (e.g., << for streams).
Utility functions needing internal access.
Tightly coupled classes (e.g., iterator accessing container).

Syntax:
class Box;
class Display {
public:
    void show(const Box& b);
};
class Box {
private:
    double width;
public:
    Box(double w) : width(w) {}
    friend void Display::show(const Box& b); // Friend function
    friend class Inspector; // Friend class
};
void Display::show(const Box& b) {
    cout << "Width: " << b.width << "\n";
}
class Inspector {
public:
    void inspect(const Box& b) { cout << "Inspected width: " << b.width << "\n"; }
};

Design Choices:



Aspect
Consideration



When to Use
For operators (<<, >>) or tightly coupled operations.


Alternatives
Getters/setters, nested classes, or public interfaces.


Granularity
Prefer friend functions over friend classes for minimal access.


Trade-offs:

Pros: Simplifies access; avoids public getters.
Cons: Breaks encapsulation; increases coupling.

Ambiguities and Exam Traps:

Access Scope: Friends access all private/protected members, but not derived classes.
Non-reciprocal: A friend of B doesn’t make B friend of A.
Overuse: Exams may penalize unnecessary friend declarations.

Dos and Don’ts:

✅ Do: Use friend for stream operators or symmetric operations.
✅ Do: Pass objects as const references to friends.
❌ Don’t: Use friend as a design shortcut.
❌ Don’t: Declare entire classes as friends unnecessarily.


9. The this Pointer
Definition: this is a pointer to the current object instance, used to access members or enable method chaining.
Syntax:
class Counter {
private:
    int value;
public:
    Counter(int v = 0) : value(v) {}
    Counter& increment() {
        ++value;
        return *this; // Enables chaining
    }
    int getValue() const { return value; }
};
Counter c;
c.increment().increment();
cout << c.getValue(); // Output: 2

Key Uses:

Disambiguate member variables (e.g., this->value = value).
Return *this for method chaining.
Pass the current object to other functions.

Dos and Don’ts:

✅ Do: Use this for clarity in complex methods.
❌ Don’t: Overuse this when member access is unambiguous.


10. STL Containers and Algorithms
Definition: The STL provides generic containers, iterators, and algorithms for efficient data handling.
Containers:



Container
Description
Example



vector<T>
Dynamic array, fast random access
vector<int> v; v.push_back(1);


array<T, N>
Fixed-size array
array<int, 3> a = {1, 2, 3};


list<T>
Doubly-linked list
list<int> l; l.push_front(1);


map<K, V>
Sorted key-value pairs
map<string, int> m; m["key"] = 1;


unordered_map<K, V>
Hash-based key-value pairs
unordered_map<string, int> um;


set<T>
Unique sorted values
set<int> s; s.insert(1);


Iterators:

Navigate containers: begin(), end(), rbegin(), rend().
Example: for (auto it = v.begin(); it != v.end(); ++it).

Algorithms:
#include <algorithm>
vector<int> v = {5, 2, 9, 1};
sort(v.begin(), v.end()); // v = {1, 2, 5, 9}
auto it = find(v.begin(), v.end(), 2); // Points to 2
reverse(v.begin(), v.end()); // v = {9, 5, 2, 1}

Dos and Don’ts:

✅ Do: Use auto for iterator types.
✅ Do: Leverage STL algorithms for efficiency.
❌ Don’t: Modify containers during iteration (invalidates iterators).


11. Templates: Function & Class Templates
Definition: Templates enable generic programming by allowing type-agnostic code.
Function Template:
template<typename T>
T add(T a, T b) {
    return a + b;
}
cout << add(1, 2); // Output: 3
cout << add(1.5, 2.5); // Output: 4

Class Template:
template<typename T>
class Box {
private:
    T value;
public:
    Box(T v) : value(v) {}
    T get() const { return value; }
};
Box<int> b(42);
cout << b.get(); // Output: 42

Template Specialization:
template<>
class Box<string> {
private:
    string value;
public:
    Box(string v) : value(v) {}
    string get() const { return value + "!"; }
};
Box<string> s("Hello");
cout << s.get(); // Output: Hello!

Design Choices:

Type Constraints: Use static_assert or C++20 concepts to restrict T.
Specialization: Provide for types needing unique behavior.
Name Lookup: Use typename for dependent types (e.g., typename T::value_type).

Dos and Don’ts:

✅ Do: Use templates for reusable, type-safe code.
✅ Do: Provide default template parameters when appropriate.
❌ Don’t: Overuse templates when simpler solutions suffice.


12. Best Practices & Gotchas
✅ Best Practices:

Use override for virtual methods to catch errors.
Use smart pointers (unique_ptr, shared_ptr) for dynamic memory.
Prefer composition over inheritance for flexibility.
Use initializer lists in constructors for efficiency.
Make destructors virtual in polymorphic base classes.

🚫 Gotchas:

Object Slicing: Avoid passing polymorphic objects by value.
Memory Leaks: Always free dynamic memory or use smart pointers.
Operator Overloading: Ensure intuitive behavior; avoid overloading &&, ||.
Name Hiding: Derived class members hide base members; use Base::method().
Template Errors: Watch for missing typename or instantiation issues.

Class Design Choices:



Aspect
Consideration
Example



Field Types
Use std::string over char*
string name;


Initialization
Use initializer lists
: width(w), height(h)


Memory
Prefer smart pointers
unique_ptr<int> ptr;


Access Control
Private by default, public getters
private: double balance;



13. Mini Project Example
This example integrates OOP, templates, operator overloading, friend functions, and STL:
#include <iostream>
#include <vector>
#include <stdexcept>
using namespace std;

template<typename T>
class Matrix {
private:
    vector<vector<T>> data;
    size_t rows, cols;
public:
    Matrix(size_t r, size_t c) : rows(r), cols(c), data(r, vector<T>(c, 0)) {}
    // Subscript operator
    vector<T>& operator[](size_t i) { return data[i]; }
    const vector<T>& operator[](size_t i) const { return data[i]; }
    // Addition operator
    Matrix operator+(const Matrix& other) const {
        if (rows != other.rows || cols != other.cols)
            throw runtime_error("Matrix dimensions mismatch");
        Matrix result(rows, cols);
        for (size_t i = 0; i < rows; i++)
            for (size_t j = 0; j < cols; j++)
                result[i][j] = data[i][j] + other[i][j];
        return result;
    }
    // Assignment operator
    Matrix& operator=(const Matrix& other) {
        if (this != &other) {
            rows = other.rows;
            cols = other.cols;
            data = other.data;
        }
        return *this;
    }
    // Friend: scalar multiplication
    friend Matrix operator*(double scalar, const Matrix& m) {
        Matrix result(m.rows, m.cols);
        for (size_t i = 0; i < m.rows; i++)
            for (size_t j = 0; j < m.cols; j++)
                result[i][j] = scalar * m.data[i][j];
        return result;
    }
    // Friend: output
    friend ostream& operator<<(ostream& os, const Matrix& m) {
        for (size_t i = 0; i < m.rows; i++) {
            for (size_t j = 0; j < m.cols; j++)
                os << m.data[i][j] << " ";
            os << endl;
        }
        return os;
    }
};

int main() {
    Matrix<double> m1(2, 2), m2(2, 2);
    m1[0][0] = 1; m1[0][1] = 2;
    m1[1][0] = 3; m1[1][1] = 4;
    m2[0][0] = 5; m2[0][1] = 6;
    m2[1][0] = 7; m2[1][1] = 8;
    Matrix<double> m3 = m1 + m2; // [6, 8; 10, 12]
    Matrix<double> m4 = 2.0 * m1; // [2, 4; 6, 8]
    cout << "m3:\n" << m3 << "\nm4:\n" << m4;
    return 0;
}


14. Final Notes & Exam Tips

Understand the Why: Focus on the purpose of encapsulation, polymorphism, and templates, not just syntax.
Spot Ambiguities:
Missing virtual destructors.
Incorrect override usage.
Self-assignment in operator=.
Template name lookup errors (typename).


Practice Scenarios:
Write a class with multiple overloaded operators.
Debug a polymorphic hierarchy with virtual functions.
Test templates with different types.


Time Management: Sketch class hierarchies or operator logic before coding.
Key Focus Areas:
Operator overloading: Ensure intuitive behavior and correct return types.
Friend functions: Justify their use; prefer alternatives when possible.
Class design: Prioritize encapsulation and memory safety.




This cheatsheet is crafted for clarity, beauty, and exam readiness. Copy the markdown content and render it locally using a markdown viewer (e.g., VS Code, Obsidian, or a browser extension). Practice the examples, understand the trade-offs, and tackle your C++ exam with confidence! 🚀
