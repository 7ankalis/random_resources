# Ultimate C# OOP Cheatsheet for Beginners

This cheatsheet covers **C# Object-Oriented Programming (OOP)** basics for a test like the Lord of the Rings programming exercise. It includes syntax, best practices, do’s and don’ts, and tips to avoid confusion, focusing on inheritance, interfaces, classes, and common test patterns.

**Current Date**: May 14, 2025

---

## 1. Core OOP Concepts

### 1.1. What is OOP?
OOP organizes code into **objects** that combine data (fields/properties) and behavior (methods). Key principles:
- **Encapsulation**: Hide data, expose only necessary parts via methods/properties.
- **Inheritance**: Reuse code by having classes inherit from others.
- **Polymorphism**: Use a base type to refer to derived types, allowing different behaviors.
- **Abstraction**: Simplify complex systems by modeling only relevant details.

**Test Tip**: Expect to create classes (e.g., `Personnage`), inherit from them (e.g., `Nain`), and implement behaviors (e.g., `Attaquer`).

---

## 2. Classes and Objects

### 2.1. Declaring a Class
A class defines a blueprint for objects.

**Syntax**:
```csharp
public class MyClass
{
    // Fields (data)
    private int myField;

    // Properties (controlled access to fields)
    public int MyProperty { get; set; }

    // Constructor
    public MyClass(int value)
    {
        myField = value;
    }

    // Method
    public void DoSomething()
    {
        Console.WriteLine("Doing something!");
    }
}
```

**Creating an Object**:
```csharp
MyClass obj = new MyClass(10);
obj.DoSomething();
```

### 2.2. Access Modifiers
Control visibility of class members:
- `public`: Accessible everywhere.
- `private`: Accessible only within the class.
- `protected`: Accessible within the class and derived classes.
- `internal`: Accessible within the same assembly.
- `protected internal`: Accessible within the assembly or derived classes.

**Test Tip**: Use `private` for fields, `public` for methods/properties, and `protected` for fields in base classes (e.g., `Personnage` had `protected nom`).

### 2.3. Do’s and Don’ts
- **Do**:
  - Use meaningful class names (e.g., `Nain` for dwarf).
  - Initialize fields in constructors.
  - Use properties over public fields for encapsulation.
- **Don’t**:
  - Expose fields publicly (e.g., `public int x;`); use properties instead.
  - Forget to call the base constructor in derived classes if needed.
- **Trick**: For quick test answers, use auto-implemented properties (`public int X { get; set; }`) to save time.

---

## 3. Constructors

### 3.1. Syntax
Constructors initialize objects. They have the same name as the class and no return type.

```csharp
public class MyClass
{
    private int value;

    public MyClass(int initialValue)
    {
        value = initialValue;
    }
}
```

### 3.2. Calling Base Constructor
In derived classes, call the base class constructor using `: base()`.

```csharp
public class BaseClass
{
    public BaseClass(int x) { }
}

public class DerivedClass : BaseClass
{
    public DerivedClass(int x, int y) : base(x) { }
}
```

### 3.3. Do’s and Don’ts
- **Do**:
  - Validate inputs in constructors (e.g., `if (force < 0) throw new ArgumentException();`).
  - Use constructor chaining for reuse: `public MyClass() : this(0) { }`.
- **Don’t**:
  - Leave fields uninitialized if they’re critical (e.g., `pointsVie = 1000` in `Personnage`).
  - Forget `: base()` if the base class has no parameterless constructor.
- **Trick**: If a test provides a base class like `Personnage`, check its constructor signature to ensure derived classes call it correctly.

---

## 4. Inheritance

### 4.1. Syntax
Inheritance allows a class to inherit members from a base class.

```csharp
public class BaseClass
{
    protected int x;

    public void BaseMethod()
    {
        Console.WriteLine("Base method");
    }
}

public class DerivedClass : BaseClass
{
    public void DerivedMethod()
    {
        x = 10; // Access protected field
        BaseMethod(); // Call base method
    }
}
```

### 4.2. Key Points
- **Base Class**: Parent class (e.g., `Personnage`).
- **Derived Class**: Child class (e.g., `Nain`).
- **Single Inheritance**: C# allows only one base class (no multiple inheritance for classes).
- **Override**: Use `virtual` in the base class and `override` in the derived class for polymorphism.

```csharp
public class BaseClass
{
    public virtual void Say() { Console.WriteLine("Base"); }
}

public class DerivedClass : BaseClass
{
    public override void Say() { Console.WriteLine("Derived"); }
}
```

### 4.3. Confusion Clarified
- **Inheritance vs. Interfaces**:
  - **Inheritance**: For “is-a” relationships (e.g., `Nain` is a `Personnage`).
  - **Interfaces**: For “can-do” behaviors (e.g., `IGuerrier` for attacking).
  - **When to Use**: Use inheritance for shared data/behavior (e.g., `pointsVie` in `Personnage`). Use interfaces for specific capabilities (e.g., `Attaquer`).
- **Overriding vs. Hiding**:
  - **Override**: Use `virtual`/`override` for polymorphic behavior.
  - **Hiding**: Use `new` to hide a base method (avoid unless intentional).
    ```csharp
    public class BaseClass { public void Method() { } }
    public class DerivedClass : BaseClass { public new void Method() { } } // Hides, not overrides
    ```
  - **Confusion**: If you forget `override`, you might accidentally hide a method, causing unexpected behavior. Always use `virtual`/`override` for test scenarios.

### 4.4. Do’s and Don’ts
- **Do**:
  - Use inheritance for clear “is-a” relationships (e.g., `NainGuerrier` is a `Nain`).
  - Mark methods as `virtual` in the base class if they need overriding.
  - Access base members with `base` (e.g., `base.Parler()`).
- **Don’t**:
  - Create deep inheritance hierarchies (e.g., avoid `NainGuerrierVoleurMagicien`); prefer interfaces for multiple behaviors.
  - Override methods without `virtual` in the base class (causes compilation error).
  - Forget to call the base constructor if required.
- **Trick**: In tests, inheritance is common for character hierarchies (e.g., `Personnage` → `Nain`). Check if the base class has abstract methods (e.g., `Parler`) that must be overridden.

---

## 5. Abstract Classes

### 5.1. Syntax
Abstract classes cannot be instantiated and often contain abstract methods.

```csharp
public abstract class Personnage
{
    protected string nom;
    protected double pointsVie;

    public Personnage(string nom)
    {
        this.nom = nom;
        pointsVie = 1000;
    }

    public abstract string Parler();
}
```

### 5.2. Key Points
- **Abstract Methods**: Must be overridden in derived classes.
- **Cannot Instantiate**: `new Personnage()` is invalid.
- **Can Have Concrete Methods**: Non-abstract methods can provide shared logic.

### 5.3. Obligations
- Derived classes **must** implement all abstract methods.
- If a derived class doesn’t implement them, it must also be `abstract`.

```csharp
public class Nain : Personnage
{
    public Nain(string nom) : base(nom) { }
    public override string Parler() { return "Groupf"; }
}
```

### 5.4. Do’s and Don’ts
- **Do**:
  - Use abstract classes for shared data and partial behavior (e.g., `Personnage` with `pointsVie` and `SeDeplacer`).
  - Ensure derived classes override all abstract methods.
- **Don’t**:
  - Try to instantiate an abstract class.
  - Forget `override` when implementing abstract methods.
- **Trick**: Tests often use abstract classes like `Personnage` to enforce specific behaviors (e.g., `Parler`). Check for abstract methods in the problem description.

---

## 6. Interfaces

### 6.1. Syntax
Interfaces define a contract that classes must follow.

```csharp
public interface IGuerrier
{
    int Force { get; }
    void Attaquer(Personnage cible);
}

public class NainGuerrier : IGuerrier
{
    public int Force { get; }
    public NainGuerrier(int force)
    {
        Force = force;
    }
    public void Attaquer(Personnage cible)
    {
        cible.SetPointsVie(cible.GetPointsVie() - Force);
    }
}
```

### 6.2. Key Points
- **No Implementation**: Interfaces only declare properties/methods.
- **Multiple Interfaces**: A class can implement multiple interfaces (unlike inheritance).
- **Implicit Implementation**: Members are public by default.

### 6.3. Confusion Clarified
- **Interfaces vs. Abstract Classes**:
  - **Interface**: Use for behaviors (e.g., `IGuerrier` for attacking). No fields or constructors.
  - **Abstract Class**: Use for shared state and partial behavior (e.g., `Personnage` with `pointsVie`).
  - **When to Use**: In tests, interfaces are common for competencies (e.g., `IGuerrier`, `IVoleur`). Use them when multiple classes need the same behavior without shared data.
- **Implementing Interfaces**:
  - **Must Implement All Members**: If `IGuerrier` has `Force` and `Attaquer`, both must be implemented.
  - **Public by Default**: Interface members are implicitly public; don’t add `public` in the interface declaration.
- **Multiple Interfaces**:
  ```csharp
  public class NainGuerrierVoleur : Nain, IGuerrier, IVoleur
  {
      public int Force { get; }
      public int Dexterite { get; }
      public void Attaquer(Personnage cible) { }
      public void Voler(Personnage cible) { }
  }
  ```

### 6.4. Do’s and Don’ts
- **Do**:
  - Use interfaces for flexible behaviors (e.g., `IGuerrier` for warriors).
  - Name interfaces with `I` prefix (e.g., `IVoleur`).
  - Implement all interface members explicitly.
- **Don’t**:
  - Add implementation in interfaces (use default methods only if advanced).
  - Forget to make interface members public in the implementing class.
- **Trick**: Tests often use interfaces for competencies (e.g., `IGuerrier` in the Lord of the Rings problem). Check if the problem lists specific behaviors (e.g., “attack,” “steal”) that suggest interfaces.

---

## 7. Properties

### 7.1. Syntax
Properties provide controlled access to fields.

```csharp
public class MyClass
{
    private int myField;
    public int MyProperty
    {
        get { return myField; }
        set { myField = value; }
    }

    // Auto-implemented property
    public int AutoProperty { get; set; }
}
```

### 7.2. Key Points
- **Get/Set**: Control read/write access.
- **Auto-Implemented**: Use `{ get; set; }` for simple properties.
- **Read-Only**: Use `{ get; }` or `{ get; private set; }`.

```csharp
public int Force { get; } // Set only in constructor
```

### 7.3. Do’s and Don’ts
- **Do**:
  - Use properties instead of public fields (e.g., `public int Force { get; }`).
  - Make properties read-only if they shouldn’t change after initialization.
- **Don’t**:
  - Use public fields (e.g., `public int x;`); they break encapsulation.
  - Overcomplicate getters/setters unless needed.
- **Trick**: In tests, use auto-implemented properties to save time unless validation is required (e.g., `Force` between 0 and 100).

---

## 8. Methods

### 8.1. Syntax
Methods define behavior.

```csharp
public class MyClass
{
    public void MyMethod(int param)
    {
        Console.WriteLine(param);
    }

    public int Calculate(int x, int y)
    {
        return x + y;
    }
}
```

### 8.2. Key Points
- **Return Type**: Specify `void` or a type (e.g., `int`, `string`).
- **Parameters**: Can be passed by value or reference (`ref`, `out`).
- **Overloading**: Same method name, different parameters.

```csharp
public void DoSomething() { }
public void DoSomething(int x) { }
```

### 8.3. Do’s and Don’ts
- **Do**:
  - Use clear method names (e.g., `Attaquer` for attack).
  - Validate parameters (e.g., `if (x < 0) throw new ArgumentException();`).
- **Don’t**:
  - Create overly long methods; break them into smaller ones.
  - Ignore return values if the method returns something.
- **Trick**: In tests, expect to implement methods like `Parler` or `Attaquer`. Check the problem for exact behavior (e.g., “reduce health by X%”).

---

## 9. Polymorphism

### 9.1. Syntax
Polymorphism allows derived classes to override base class behavior.

```csharp
public class BaseClass
{
    public virtual void Say() { Console.WriteLine("Base"); }
}

public class DerivedClass : BaseClass
{
    public override void Say() { Console.WriteLine("Derived"); }
}

BaseClass obj = new DerivedClass();
obj.Say(); // Outputs: Derived
```

### 9.2. Key Points
- **Virtual/Override**: Enables runtime polymorphism.
- **Abstract Methods**: Force derived classes to provide implementation.
- **Interface Polymorphism**: Use interfaces for flexible behavior.

```csharp
IGuerrier warrior = new NainGuerrier(10);
warrior.Attaquer(target);
```

### 9.3. Do’s and Don’ts
- **Do**:
  - Use `virtual`/`override` for methods that need different implementations.
  - Use interfaces for multiple behaviors (e.g., `IGuerrier`, `IVoleur`).
- **Don’t**:
  - Use `new` to hide methods unless intentional (causes confusion).
  - Forget `override` for virtual/abstract methods.
- **Trick**: Tests often use polymorphism (e.g., `Personnage` reference to `Nain`). Ensure overridden methods like `Parler` match the expected output.

---

## 10. Common Test Patterns

Based on the Lord of the Rings exercise, here are patterns to expect:

### 10.1. Abstract Base Class
- **Pattern**: A base class like `Personnage` with abstract methods (e.g., `Parler`).
- **What to Do**:
  - Create derived classes (e.g., `Nain`) that override abstract methods.
  - Call the base constructor with `: base()`.
- **Example**:
  ```csharp
  public abstract class Personnage
  {
      protected string nom;
      public Personnage(string nom) { this.nom = nom; }
      public abstract string Parler();
  }

  public class Nain : Personnage
  {
      public Nain(string nom) : base(nom) { }
      public override string Parler() { return "Groupf"; }
  }
  ```

### 10.2. Interfaces for Competencies
- **Pattern**: Interfaces like `IGuerrier` or `IVoleur` for specific behaviors.
- **What to Do**:
  - Implement interfaces in classes (e.g., `NainGuerrier : IGuerrier`).
  - Ensure all interface members are public.
- **Example**:
  ```csharp
  public interface IGuerrier
  {
      int Force { get; }
      void Attaquer(Personnage cible);
  }

  public class NainGuerrier : Nain, IGuerrier
  {
      public int Force { get; }
      public NainGuerrier(string nom, int force) : base(nom)
      {
          Force = force;
      }
      public void Attaquer(Personnage cible) { }
  }
  ```

### 10.3. Inheritance for Specialization
- **Pattern**: Derived classes like `NainGuerrier` or `NainGuerrierVoleur`.
- **What to Do**:
  - Extend the base class (e.g., `NainGuerrier : Nain`).
  - Add new attributes/methods for specific behaviors.
- **Example**:
  ```csharp
  public class NainGuerrier : Nain
  {
      public int Force { get; }
      public NainGuerrier(string nom, int force) : base(nom)
      {
          Force = force;
      }
  }
  ```

### 10.4. Method Implementation
- **Pattern**: Methods like `Attaquer` or `Voler` with specific logic.
- **What to Do**:
  - Read the problem carefully for method behavior (e.g., “reduce health by Force%”).
  - Validate inputs (e.g., `Force` between 0 and 100).
- **Example**:
  ```csharp
  public void Attaquer(Personnage cible)
  {
      double damage = cible.GetPointsVie() * (Force / 100.0);
      cible.SetPointsVie(cible.GetPointsVie() - damage);
  }
  ```

---

## 11. Do’s and Don’ts for Tests

### 11.1. Do’s
- **Read the Problem Carefully**: Check for abstract methods, interfaces, or specific behaviors (e.g., “Parler returns Groupf”).
- **Use Meaningful Names**: Class names like `Nain`, method names like `Attaquer`.
- **Validate Inputs**: Ensure attributes like `Force` or `Dexterite` meet constraints.
- **Override Abstract Methods**: Always implement methods like `Parler` in derived classes.
- **Call Base Constructors**: Use `: base()` if the base class has a constructor.
- **Test Your Code**: If allowed, write a `Main` method to verify behavior (e.g., check if `Attaquer` reduces health correctly).

### 11.2. Don’ts
- **Don’t Modify Provided Code**: If given a class like `Personnage`, don’t change it.
- **Don’t Ignore Abstract Methods**: Failing to override `Parler` causes a compilation error.
- **Don’t Overcomplicate**: Avoid advanced features (e.g., generics, lists) unless required.
- **Don’t Forget Access Modifiers**: Make interface methods public, fields private/protected.
- **Don’t Skip Validation**: Invalid inputs (e.g., negative `Force`) can cause errors.

---

## 12. Common Pitfalls and Tricks

### 12.1. Pitfalls
- **Forgetting `override`**: Causes compilation errors for virtual/abstract methods.
  ```csharp
  // Wrong
  public string Parler() { return "Groupf"; }
  // Correct
  public override string Parler() { return "Groupf"; }
  ```
- **Incorrect Base Constructor**: If `Personnage` has no parameterless constructor, you must call `: base(nom, x, y, v)`.
- **Public Fields**: Use properties instead (e.g., `public int Force { get; }` not `public int force;`).
- **Interface Members Not Public**: Interface implementations must be public.
  ```csharp
  // Wrong
  void IGuerrier.Attaquer(Personnage cible) { }
  // Correct
  public void Attaquer(Personnage cible) { }
  ```

### 12.2. Tricks
- **Auto-Implemented Properties**: Save time with `public int X { get; set; }`.
- **Constructor Chaining**: Reuse constructors with `: this()`.
  ```csharp
  public MyClass() : this(0) { }
  public MyClass(int x) { }
  ```
- **Quick Validation**: Use simple checks like `if (x < 0) throw new ArgumentException();`.
- **Test Debugging**: Write a `Main` method to test your classes:
  ```csharp
  static void Main()
  {
      Nain nain = new Nain("Thorin");
      Console.WriteLine(nain.Parler()); // Groupf
  }
  ```
- **Check Problem Constraints**: Look for ranges (e.g., `Force` 0–100) or specific outputs (e.g., “Bonjour”).

---

## 13. Syntax Quick Reference

### 13.1. Class Declaration
```csharp
public class MyClass { }
public abstract class MyAbstractClass { }
```

### 13.2. Constructor
```csharp
public MyClass(int x) { }
```

### 13.3. Inheritance
```csharp
public class Derived : Base { }
```

### 13.4. Interface
```csharp
public interface IMyInterface
{
    int Property { get; }
    void Method();
}
```

### 13.5. Property
```csharp
public int MyProperty { get; set; }
public int ReadOnly { get; }
```

### 13.6. Method
```csharp
public void MyMethod(int x) { }
public virtual void VirtualMethod() { }
public abstract void AbstractMethod();
```

### 13.7. Override
```csharp
public override void Method() { }
```

---

## 14. Test Strategy
Based on the Lord of the Rings exercise:
1. **Identify the Base Class**: Look for an abstract class like `Personnage` with fields (e.g., `nom`, `pointsVie`) and abstract methods (e.g., `Parler`).
2. **Check for Interfaces**: Expect interfaces like `IGuerrier` or `IVoleur` for competencies.
3. **Implement Derived Classes**: Create classes like `Nain`, `NainGuerrier` with required behaviors.
4. **Validate Constraints**: Ensure attributes (e.g., `Force`) meet problem constraints.
5. **Test Behavior**: If possible, write a `Main` method to verify outputs (e.g., health after `Attaquer`).
6. **Keep It Simple**: Use inheritance for hierarchies, interfaces for behaviors, and avoid over-engineering.

---

## 15. Example: Lord of the Rings Test Solution
Here’s a quick example based on your test, summarizing key concepts:

```csharp
public abstract class Personnage
{
    protected string nom;
    protected double pointsVie;

    public Personnage(string nom)
    {
        this.nom = nom;
        pointsVie = 1000;
    }

    public double GetPointsVie() { return pointsVie; }
    public void SetPointsVie(double pv) { pointsVie = pv; }
    public abstract string Parler();
}

public interface IGuerrier
{
    int Force { get; }
    void Attaquer(Personnage cible);
}

public class Nain : Personnage
{
    public Nain(string nom) : base(nom) { }
    public override string Parler() { return "Groupf"; }
}

public class NainGuerrier : Nain, IGuerrier
{
    public int Force { get; }
    public NainGuerrier(string nom, int force) : base(nom)
    {
        if (force < 0 || force > 100) throw new ArgumentException();
        Force = force;
    }
    public void Attaquer(Personnage cible)
    {
        double damage = cible.GetPointsVie() * (Force / 100.0);
        cible.SetPointsVie(cible.GetPointsVie() - damage);
    }
}
```

**Key Points**:
- `Personnage`: Abstract base with fields and abstract `Parler`.
- `Nain`: Overrides `Parler` with “Groupf”.
- `NainGuerrier`: Implements `IGuerrier`, adds `Force` and `Attaquer`.
- Validates `Force` in constructor.

---

## 16. Final Tips
- **Practice Simple Hierarchies**: Create a base class and 1–2 derived classes with overridden methods.
- **Memorize Interface Syntax**: Know how to declare and implement interfaces.
- **Check for Abstract Methods**: Always override them in derived classes.
- **Validate Everything**: Add checks for ranges or nulls in constructors/methods.
- **Stay Calm**: Tests like this are straightforward if you follow the problem’s instructions.

Good luck on your test! 🚀