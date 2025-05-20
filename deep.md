```markdown
# C++ OOP & STL Crash Sheet 🚀

## 1. Access Specifiers
```cpp
class Example {
public:    // Accessible everywhere
    int x;
protected: // Accessible in class + derived
    int y;
private:   // Accessible only in class
    int z;
};
```

---

## 2. Inheritance
```cpp
class Base {
public:
    virtual ~Base() {} // Always virtual destructor
    virtual void print() { cout << "Base"; }
};

class Derived : public Base {
public:
    void print() override { cout << "Derived"; }
};
```

---

## 3. Polymorphism
```cpp
class Shape {
public:
    virtual double area() = 0; // Pure virtual = abstract
};

class Circle : public Shape {
    double r;
public:
    double area() override { return 3.14 * r * r; }
};
```

---

## 4. Operator Overloading
```cpp
class Vector {
    double x, y;
public:
    Vector operator+(const Vector& v) const {
        return {x+v.x, y+v.y};
    }
    
    friend ostream& operator<<(ostream& os, const Vector& v) {
        return os << "(" << v.x << "," << v.y << ")";
    }
};
```

---

## 5. Friend Functions
```cpp
class Secret {
    int code;
    friend void peek(Secret& s); // Grant access
};

void peek(Secret& s) {
    cout << s.code; // Access private member
}
```

---

## 6. STL Containers
```cpp
vector<int> nums = {1, 2, 3};
map<string, int> ages = {{"Alice", 30}, {"Bob", 25}};
unordered_set<string> names;
```

---

## 7. Templates
```cpp
template<typename T>
class Box {
    T content;
public:
    void set(T val) { content = val; }
    T get() { return content; }
};

Box<string> stringBox;
```

---

## 8. Common Pitfalls
**Object Slicing:**
```cpp
Derived d;
Base b = d; // Only copies Base portion
```

**Solution:** Use pointers/references:
```cpp
Base& ref = d;
Base* ptr = new Derived();
```

---

## 9. Best Practices
1. Use `override` keyword
2. Follow Rule of 5 for resource management
3. Prefer `const` correctness
4. Use smart pointers (`unique_ptr`, `shared_ptr`)
5. Avoid raw `new`/`delete`

# Exam Tip: Test virtual destructors and object slicing scenarios!
```
