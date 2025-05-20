Advanced C++ OOP Cheatsheet for Exam Preparation
This cheatsheet is tailored for a rigorous C++ exam focusing on Object-Oriented Programming (OOP), Standard Template Library (STL), template classes, and operator overloading, with an in-depth exploration of friend functions/classes, operator overloading, and class design decisions. It’s structured to progress logically from core concepts to advanced topics, addressing ambiguities, trade-offs, and best practices. Each section includes detailed explanations, examples, and exam tips to handle complex scenarios.
1. Core OOP Concepts
1.1 Classes and Objects

Definition: A class defines a user-defined type, encapsulating data (fields) and behavior (methods). Objects are instances of classes.
Syntax:class ClassName {
public:
    // Accessible anywhere
    void publicMethod();
private:
    // Accessible only within class
    int privateField;
protected:
    // Accessible in class and derived classes
    int protectedField;
};


Design Choices:
Fields: Use private for data encapsulation; provide public getters/setters for controlled access.
Methods: Define public for external interfaces, private for internal utilities, protected for inheritance.
Naming: Use clear, descriptive names (e.g., calculateArea over calc).


Example:class Rectangle {
private:
    double width, height; // Encapsulated fields
public:
    Rectangle(double w, double h) : width(w), height(h) {} // Constructor
    double getArea() const { return width * height; } // Getter, const for safety
    void setWidth(double w) { if (w >= 0) width = w; } // Setter with validation
};
Rectangle rect(5.0, 3.0);
cout << rect.getArea(); // Output: 15


Do:
Validate inputs in setters.
Use const for methods that don’t modify state.


Don’t:
Expose fields as public without validation.
Overload classes with unrelated responsibilities.



1.2 Constructors and Destructors

Constructor Types:
Default: No parameters, initializes defaults.
Parameterized: Takes parameters for custom initialization.
Copy: Copies another object (ClassName(const ClassName&)).
Move (C++11): Transfers resources (ClassName(ClassName&&)).


Destructor: Cleans up resources, named ~ClassName(), no parameters.
Initializer Lists: Initialize fields efficiently before constructor body.
Syntax:class Student {
private:
    string name;
    int* scores; // Dynamic resource
public:
    Student() : name("Unknown"), scores(new int[10]) {} // Default
    Student(string n, int size) : name(n), scores(new int[size]) {} // Parameterized
    Student(const Student& other) : name(other.name), scores(new int[10]) { // Copy
        copy(other.scores, other.scores + 10, scores);
    }
    Student(Student&& other) noexcept : name(move(other.name)), scores(other.scores) { // Move
        other.scores = nullptr;
    }
    ~Student() { delete[] scores; } // Destructor
};


Design Choices:
Use initializer lists to avoid redundant assignments.
Implement copy/move constructors for classes managing resources.
Mark move constructors noexcept for STL compatibility.


Do:
Follow the Rule of Five (destructor, copy/move constructors, copy/move assignment).
Ensure destructors free all dynamic resources.


Don’t:
Forget deep copies in copy constructors for dynamic resources.
Omit destructors for classes with dynamic memory.



2. Inheritance and Polymorphism
2.1 Inheritance

Definition: Derived classes inherit fields and methods from base classes, enabling code reuse and polymorphism.
Types:
public: Public/protected members retain their access; used for "is-a" relationships.
protected: Public/protected members become protected.
private: All inherited members become private; rarely used.


Syntax:class Vehicle {
protected:
    string brand;
public:
    Vehicle(string b) : brand(b) {}
    virtual void drive() { cout << brand << " drives." << endl; }
};
class Car : public Vehicle {
public:
    Car(string b) : Vehicle(b) {}
    void drive() override { cout << brand << " zooms!" << endl; }
};


Design Choices:
Use public inheritance for natural hierarchies (e.g., Car is-a Vehicle).
Use composition for "has-a" relationships (e.g., Car has-an Engine).


Do: Initialize base class in derived class constructor.
Don’t: Inherit from classes not intended as base classes (e.g., STL containers).

2.2 Virtual Functions and Polymorphism

Virtual Functions: Enable runtime polymorphism via dynamic dispatch.
Pure Virtual Functions: Make a class abstract (virtual void func() = 0;).
Syntax:class Animal {
public:
    virtual void speak() = 0; // Pure virtual
    virtual ~Animal() = default; // Virtual destructor
};
class Dog : public Animal {
public:
    void speak() override { cout << "Woof!" << endl; }
};
Animal* pet = new Dog();
pet->speak(); // Output: Woof!
delete pet;


Design Choices:
Use virtual for methods expected to be overridden.
Use override to catch errors in derived classes.
Always provide a virtual destructor in polymorphic base classes.


Do:
Use final (C++11) to prevent further overrides (void speak() override final).
Ensure virtual functions have consistent return types or covariant returns.


Don’t:
Forget virtual destructors, causing undefined behavior on delete.
Overuse virtual functions; they incur runtime overhead.



2.3 Abstract Classes and Interfaces

Abstract Class: Contains at least one pure virtual function; cannot be instantiated.
Interface: An abstract class with only pure virtual functions and no data.
Syntax:class IShape {
public:
    virtual void draw() const = 0;
    virtual double area() const = 0;
    virtual ~IShape() = default;
};
class Circle : public IShape {
private:
    double radius;
public:
    Circle(double r) : radius(r) {}
    void draw() const override { cout << "Drawing Circle" << endl; }
    double area() const override { return 3.14159 * radius * radius; }
};


Design Choices:
Use interfaces for defining contracts (e.g., IShape for all shapes).
Keep interfaces minimal; avoid data members.


Do: Use const for interface methods to ensure immutability.
Don’t: Add implementation details to interfaces.

3. Friend Functions and Classes (Detailed)

Definition: friend grants access to private/protected members to specific functions or classes.
Use Cases:
Operator overloading (e.g., << for output streams).
Utility functions needing internal access.
Classes collaborating closely (e.g., iterators accessing container internals).


Syntax:class Box {
private:
    double width;
public:
    Box(double w) : width(w) {}
    // Friend function
    friend void printWidth(const Box& b);
    // Friend class
    friend class BoxInspector;
};
void printWidth(const Box& b) {
    cout << "Width: " << b.width << endl;
}
class BoxInspector {
public:
    void inspect(const Box& b) {
        cout << "Inspected width: " << b.width << endl;
    }
};


Design Choices:
When to Use Friend:
For operators requiring left-hand operand access (e.g., cout << obj).
When two classes are tightly coupled (e.g., a manager class accessing a worker class).


Alternatives:
Use getters/setters for controlled access.
Use nested classes for internal helpers instead of friend.


Trade-offs:
Pros: Simplifies access for specific operations; avoids public getters for private data.
Cons: Breaks encapsulation; increases coupling; harder to maintain.




Ambiguities and Exam Traps:
Access Scope: Friends have full access to private/protected members, but this doesn’t extend to derived classes unless explicitly declared.
Non-reciprocal: Declaring class A as a friend of class B doesn’t make B a friend of A.
Overuse: Exam questions may penalize unnecessary friend declarations; justify their use.


Do:
Use friend for stream operators (<<, >>) or symmetric operations.
Document why friend is necessary (e.g., performance or necessity).
Pass objects as const references to friends to prevent modification.


Don’t:
Use friend as a shortcut for poor design; prefer encapsulation.
Declare entire classes as friends when only one function is needed.


Example:class Matrix {
private:
    vector<vector<double>> data;
public:
    Matrix(int rows, int cols) : data(rows, vector<double>(cols, 0)) {}
    friend Matrix operator*(double scalar, const Matrix& m); // Friend for scalar*matrix
};
Matrix operator*(double scalar, const Matrix& m) {
    Matrix result(m.data.size(), m.data[0].size());
    for (size_t i = 0; i < m.data.size(); i++)
        for (size_t j = 0; j < m.data[0].size(); j++)
            result.data[i][j] = scalar * m.data[i][j];
    return result;
}



4. Operator Overloading ("Surcharge des Opérateurs") (Detailed)

Definition: Redefine operators for user-defined types to provide intuitive behavior.
Types:
Member Functions: For operators where the left operand is the class object.
Non-Member Functions: Often friend, for operators like << or when the left operand isn’t the class (e.g., scalar * object).


Syntax:class Complex {
private:
    double real, imag;
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    // Member operator
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    // Comparison operator
    bool operator==(const Complex& other) const {
        return real == other.real && imag == other.imag;
    }
    // Friend operator for stream output
    friend ostream& operator<<(ostream& os, const Complex& c) {
        os << c.real << " + " << c.imag << "i";
        return os;
    }
    // Friend for scalar multiplication
    friend Complex operator*(double scalar, const Complex& c) {
        return Complex(scalar * c.real, scalar * c.imag);
    }
};
Complex a(1, 2), b(3, 4);
Complex c = a + b; // (4, 6)
cout << c; // Output: 4 + 6i
Complex d = 2.0 * a; // (2, 4)


Common Operators:
Arithmetic: +, -, *, /, %
Assignment: =, +=, -=, *=, /=
Comparison: ==, !=, <, >, <=, >=
Stream: <<, >>
Subscript: []
Function Call: ()


Design Choices:
Member vs. Non-Member:
Use member functions for operators like +, =, where the left operand is the class.
Use non-member (often friend) for operators like << or when the left operand isn’t the class (e.g., double * Complex).


Return Types:
Arithmetic: Return a new object (Complex for +).
Assignment: Return *this as reference (Complex& for =).
Comparison: Return bool.
Stream: Return ostream& or istream&.


Const Correctness:
Mark member operators const to prevent modifying the left operand.
Use const references for parameters to avoid copying.




Ambiguities and Exam Traps:
Operator Precedence: Overloaded operators retain C++’s precedence rules (e.g., * before +).
Symmetry: Define == and != together; ensure a == b implies !(a != b).
Chaining: Return references for operators like << or += to support chaining (cout << a << b).
Implicit Conversions: Be cautious of implicit type conversions causing ambiguity (e.g., Complex + double).


Do:
Make operator behavior intuitive (e.g., + adds, not subtracts).
Provide consistent operator sets (e.g., if ==, define !=).
Use const for non-mutating operators.
Return by value for arithmetic operators to avoid modifying temporaries.


Don’t:
Overload &&, ||, , (alters short-circuiting or precedence).
Modify operands in arithmetic operators (e.g., a + b shouldn’t change a).
Forget to handle self-assignment in operator= (if (this != &other)).


Advanced Example:class Vector {
private:
    vector<double> elements;
public:
    Vector(initializer_list<double> init) : elements(init) {}
    // Subscript operator
    double& operator[](size_t i) { return elements[i]; }
    const double& operator[](size_t i) const { return elements[i]; }
    // Addition
    Vector operator+(const Vector& other) const {
        Vector result = *this;
        for (size_t i = 0; i < elements.size(); i++)
            result[i] += other[i];
        return result;
    }
    // Assignment
    Vector& operator=(const Vector& other) {
        if (this != &other) {
            elements = other.elements; // Deep copy
        }
        return *this;
    }
    friend ostream& operator<<(ostream& os, const Vector& v) {
        os << "[";
        for (size_t i = 0; i < v.elements.size(); i++)
            os << v.elements[i] << (i < v.elements.size() - 1 ? ", " : "");
        os << "]";
        return os;
    }
};
Vector v1{1, 2}, v2{3, 4};
Vector v3 = v1 + v2; // [4, 6]
cout << v3; // Output: [4, 6]



5. Templates (Detailed)

Definition: Enable generic programming with type parameters.
Syntax:template <typename T>
class Stack {
private:
    vector<T> items;
public:
    void push(const T& item) { items.push_back(item); }
    T pop() {
        T item = items.back();
        items.pop_back();
        return item;
    }
};
Stack<int> s;
s.push(42);
cout << s.pop(); // Output: 42


Template Specialization:template <>
class Stack<string> {
private:
    vector<string> items;
public:
    void push(const string& item) { items.push_back(item); }
    string pop() {
        string item = items.back();
        items.pop_back();
        return item + "!"; // Specialized behavior
    }
};
Stack<string> s;
s.push("Hello");
cout << s.pop(); // Output: Hello!


Design Choices:
Type Constraints: Use static_assert or concepts (C++20) to restrict T (e.g., numeric types).
Function vs. Class Templates: Use function templates for algorithms, class templates for containers.
Explicit Specialization: Provide for types needing unique behavior (e.g., string).


Ambiguities:
Name Lookup: Template code may cause dependent name issues; use typename or this->.
Instantiation: Templates are instantiated at compile-time; errors may appear late.


Do:
Use templates for reusable, type-safe code.
Provide default template parameters when appropriate (template <typename T = int>).


Don’t:
Overuse templates; simple inheritance may suffice.
Ignore compilation overhead from excessive template use.



6. Standard Template Library (STL)

Containers:
vector: Dynamic array, fast random access (vector<int> v; v.push_back(1);).
array: Fixed-size array, stack-allocated (array<int, 5> a = {1, 2, 3, 4, 5};).
list: Doubly-linked list, fast insertions (list<int> l; l.push_front(1);).
map: Ordered key-value pairs (map<string, int> m; m["key"] = 42;).
unordered_map: Hash-based key-value pairs, faster lookup.


Iterators:
Types: begin(), end(), rbegin(), rend(), const_iterator.
Example: for (auto it = v.begin(); it != v.end(); ++it) cout << *it;.


Algorithms:
sort(v.begin(), v.end()): Sort in ascending order.
find_if(v.begin(), v.end(), pred): Find element matching predicate.
accumulate(v.begin(), v.end(), 0): Sum elements.


Example:vector<int> v = {5, 2, 9, 1};
sort(v.begin(), v.end()); // v = {1, 2, 5, 9}
auto it = find_if(v.begin(), v.end(), [](int x) { return x > 3; });
cout << *it; // Output: 5


Do:
Use auto for iterator types (C++11).
Prefer STL algorithms over manual loops.


Don’t:
Modify containers during iteration (causes iterator invalidation).
Ignore STL’s exception guarantees (e.g., vector::push_back may throw).



7. Class Design Decisions and Trade-offs

Field Definitions:
Type Choice: Use int vs. unsigned based on semantics; prefer std::string over char*.
Initialization: Initialize all fields in constructors; use initializer lists for efficiency.
Const Members: Use const fields for immutable data; initialize in initializer list.

class Circle {
private:
    const double pi = 3.14159; // Const field
    double radius;
public:
    Circle(double r) : radius(r) {}
};


Access Control:
Private by Default: Only expose what’s necessary via public methods.
Protected for Inheritance: Use for fields/methods needed by derived classes.
Avoid Public Fields: Use getters/setters for validation and flexibility.


Memory Management:
Use smart pointers (unique_ptr, shared_ptr) for dynamic resources (C++11).
Avoid raw pointers unless necessary (e.g., interfacing with C libraries).


Trade-offs:
Performance vs. Safety: Raw pointers are faster but error-prone; smart pointers are safer but have overhead.
Flexibility vs. Simplicity: Complex hierarchies allow extensibility but increase maintenance.


Ambiguities:
Name Hiding: Derived class members hide base class members unless qualified (Base::method()).
Diamond Problem: Multiple inheritance can cause ambiguity; use virtual inheritance.

class A { public: virtual void func() {} };
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {}; // Virtual inheritance avoids duplication



8. Comprehensive Example
This example integrates OOP, templates, operator overloading, friend functions, and STL:
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

template <typename T>
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
    
    // Friend function for scalar multiplication
    friend Matrix operator*(double scalar, const Matrix& m) {
        Matrix result(m.rows, m.cols);
        for (size_t i = 0; i < m.rows; i++)
            for (size_t j = 0; j < m.cols; j++)
                result[i][j] = scalar * m.data[i][j];
        return result;
    }
    
    // Friend function for output
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
    cout << m3 << endl << m4;
    return 0;
}

9. Exam Tips for Handling Ambiguity

Read Carefully: Questions may hide edge cases (e.g., missing virtual, incorrect override).
Trace Polymorphism: Understand how virtual function calls resolve in complex hierarchies.
Check Operator Overloading: Ensure correct return types and const correctness.
Memory Management: Spot missing destructors or copy/move operations.
Template Errors: Look for missing typename or incorrect specializations.
Practice Scenarios:
Write a class with overloaded operators and test edge cases (e.g., self-assignment).
Create a hierarchy with virtual functions and trace calls through pointers.
Debug a template class with incorrect type usage.



