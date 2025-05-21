# Ultimate C++ OOP Cheatsheet

---

## **1- Classes & Objects**
A **class** is a blueprint for creating **objects** (instances of the class).  

```cpp
class ClassName {
public:
    int attribute;  // Data Member
    void method() { cout << "Hello!" << endl; }  // Member Function
};
```

✔ **Creating an Object**  
```cpp
ClassName obj;  // Object creation
obj.method();   // Call method
```

---

## **2- Access Specifiers**
Control access to class members.  

| Specifier  | Description |
|------------|------------|
| `public`   | Accessible from anywhere |
| `private`  | Accessible only inside the class |
| `protected`| Accessible inside the class & derived classes |

```cpp
class Example {
private:
    int secret;  // Can't be accessed outside
public:
    int data;    // Can be accessed anywhere
};
```

---

## **3- Constructors & Destructors**
Special functions for object **initialization & cleanup**.

✔ **Constructor (Runs when object is created)**  
```cpp
class Car {
public:
    Car() { cout << "Car created!" << endl; }  // Default Constructor
};
```

✔ **Parameterized Constructor**  
```cpp
class Car {
public:
    string brand;
    Car(string b) { brand = b; }  // Constructor with parameter
};
```

✔ **Copy Constructor (Creates a copy of an object)**  
```cpp
class Car {
public:
    string brand;
    Car(const Car &c) { brand = c.brand; }  // Copy Constructor
};
```

✔ **Destructor (Runs when object is destroyed)**  
```cpp
class Car {
public:
    ~Car() { cout << "Car destroyed!" << endl; }
};
```

---

## **4- Encapsulation (Data Hiding)**
✔ **Use `private` variables & `public` methods to access them**  
```cpp
class BankAccount {
private:
    double balance;
public:
    void setBalance(double b) { balance = b; }
    double getBalance() { return balance; }
};
```

---

## **5- Inheritance (Reusing Code)**
✔ **A class can inherit from another class**  
```cpp
class Vehicle {  // Parent class
public:
    void honk() { cout << "Beep beep!" << endl; }
};

// Child class inherits from Vehicle
class Car : public Vehicle {
public:
    void drive() { cout << "Car is moving!" << endl; }
};
```

✔ **Types of Inheritance**  
- `public`: Public members remain public in derived class.
- `private`: Public members become private in derived class.
- `protected`: Public members become protected in derived class.

---

## **6- Polymorphism (Multiple Forms)**
### **✔ Function Overloading (Same name, different parameters)**  
```cpp
class Math {
public:
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
};
```

### **✔ Function Overriding (Redefining parent method in child class)**  
```cpp
class Animal {
public:
    virtual void makeSound() { cout << "Animal sound!" << endl; }
};

class Dog : public Animal {
public:
    void makeSound() override { cout << "Bark!" << endl; }
};
```

✔ **Virtual Function (Allows dynamic method overriding)**  
```cpp
class Parent {
public:
    virtual void show() { cout << "Parent class" << endl; }
};

class Child : public Parent {
public:
    void show() override { cout << "Child class" << endl; }
};

int main() {
    Parent *p = new Child();
    p->show();  // Calls Child's show()
}
```

---

## **7- Operator Overloading**
✔ **Change the behavior of operators for objects**  
```cpp
class Number {
public:
    int value;
    Number(int v) { value = v; }

    // Overloading `+` operator
    Number operator+(const Number &n) {
        return Number(value + n.value);
    }
};
```

✔ **Overloading `==` operator**  
```cpp
class Car {
public:
    string brand;
    Car(string b) { brand = b; }

    bool operator==(const Car &c) {
        return brand == c.brand;
    }
};
```

✔ **Overloading `<<` for Output Stream**  
```cpp
#include <iostream>
using namespace std;

class Car {
public:
    string brand;
    Car(string b) { brand = b; }

    friend ostream& operator<<(ostream &os, const Car &c) {
        os << "Car Brand: " << c.brand;
        return os;
    }
};
```

---

## **10- File Handling**
✔ **Write to a file**  
```cpp
#include <fstream>
using namespace std;

int main() {
    ofstream file("example.txt");
    file << "Hello, file!";
    file.close();
}
```

✔ **Read from a file**  
```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream file("example.txt");
    string line;
    while (getline(file, line)) {
        cout << line << endl;
    }
    file.close();
}
```

---

### **Final Summary**

| Concept | Key Idea |
|---------|---------|
| **Class & Object** | Blueprint & instance of a class |
| **Encapsulation** | Hides data using `private` |
| **Inheritance** | Child class inherits from parent |
| **Polymorphism** | Overloading & Overriding methods |
| **Constructor** | Runs automatically on object creation |
| **Destructor** | Runs automatically when object is destroyed |
| **Operator Overloading** | Change behavior of operators |
| **File Handling** | Read/Write to files |

---
