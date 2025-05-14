# Ultimate C# OOP Cheatsheet for Beginners

This cheatsheet covers **C# Object-Oriented Programming (OOP)** basics for a test like the Lord of the Rings programming exercise. It includes syntax, best practices, do’s and don’ts, and tips to avoid confusion, focusing on inheritance, interfaces, classes, and common test patterns. Updated to address constructor chaining and fragile OOP concepts.

**Current Date**: May 14, 2025

---

## 1. Core OOP Concepts

### 1.1. What is OOP?
OOP organizes code into **objects** combining data (fields/properties) and behavior (methods). Key principles:
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
    private int myField;
    public int MyProperty { get; set; }
    public MyClass(int value)
    {
        myField = value;
    }
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
- `public`: Accessible everywhere.
- `private`: Accessible only within the class.
- `protected`: Accessible within the class and derived classes.
- `internal`: Accessible within the same assembly.
- `protected internal`: Accessible within the assembly or derived classes.

**Test Tip**: Use `private` for fields, `public` for methods/properties, `protected` for base class fields (e.g., `protected string nom` in `Personnage`).

### 2.3. Confusion Clarified
- **Public Fields vs. Properties**:
  - **Fragile**: Public fields (e.g., `public int x;`) allow uncontrolled access, breaking encapsulation.
  - **Fix**: Use properties (e.g., `public int X { get; set; }`) for controlled access.
  - **Test Mistake**: Declaring `public int force;` instead of `public int Force { get; }` in `NainGuerrier`.

### 2.4. Do’s and Don’ts
- **Do**:
  - Use meaningful names (e.g., `Nain` for dwarf).
  - Initialize fields in constructors.
  - Use properties over public fields.
- **Don’t**:
  - Expose fields publicly (e.g., `public int x;`).
  - Forget to initialize critical fields (e.g., `pointsVie`).
- **Trick**: Use auto-implemented properties (`public int X { get; set; }`) to save time in tests.

---

## 3. Constructors

### 3.1. Syntax
Constructors initialize objects, matching the class name with no return type.

**Syntax**:
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

### 3.2. Constructor Chaining with Base Class
Derived classes must call the base class constructor using `: base(...)`.

**Example**:
```csharp
public abstract class Personnage
{
    protected string nom;
    protected int x, y, vitesse;
    public Personnage(string n, int x, int y, int v)
    {
        nom = n;
        x = x;
        y = y;
        vitesse = v;
    }
}

public class Nain : Personnage
{
    public Nain(string nom) : base(nom, 0, 0, 2)
    {
    }
}
```

- **Explanation**:
  - `Personnage` requires `n`, `x`, `y`, `v`.
  - `Nain` calls `: base(nom, 0, 0, 2)` to pass:
    - `nom`: Dwarf’s name.
    - `x = 0`, `y = 0`: Default position.
    - `v = 2`: Dwarf speed (2 km/h).
  - Empty body `{ }` as no additional initialization is needed.

**Alternative** (if caller specifies position):
```csharp
public Nain(string nom, int x, int y) : base(nom, x, y, 2)
{
}
```

### 3.3. Confusion Clarified
- **Missing Base Constructor Call**:
  - **Fragile**: If `Personnage` has no parameterless constructor, `public Nain(string nom) : base(nom) { }` fails if `Personnage` expects more parameters (e.g., `x`, `y`, `v`).
  - **Error**: `Error: 'Personnage' does not contain a constructor that takes 1 argument`.
  - **Fix**: Match the base constructor’s signature:
    ```csharp
    // Wrong
    public Nain(string nom) : base(nom) { }
    // Correct
    public Nain(string nom) : base(nom, 0, 0, 2) { }
    ```
  - **Test Mistake**: Forgetting to provide all base constructor parameters (e.g., `x`, `y`, `v`).
- **Parameterless Base Constructor**:
  - **Fragile**: If `Personnage` has only `public Personnage(string n, int x, int y, int v)`, omitting `: base(...)` tries to call a non-existent `Personnage()`.
  - **Fix**: Always check the base class’s constructor signatures in the problem.
- **Extra Parameters**:
  - **Fragile**: If `Personnage` adds parameters (e.g., `int gold`), update `Nain`:
    ```csharp
    public Personnage(string n, int x, int y, int v, int gold) { ... }
    public Nain(string nom) : base(nom, 0, 0, 2, 0) { }
    ```
  - **Test Mistake**: Missing new parameters in `: base(...)`.

### 3.4. Do’s and Don’ts
- **Do**:
  - Validate inputs (e.g., `if (force < 0) throw new ArgumentException();`).
  - Call `: base(...)` with all required parameters.
  - Use default values for non-critical parameters (e.g., `x = 0`, `y = 0`).
- **Don’t**:
  - Omit `: base(...)` if the base has no parameterless constructor.
  - Initialize base fields directly (e.g., `this.nom = nom;`) if the base constructor handles it.
- **Trick**: In tests, check the base class (e.g., `Personnage`) constructor signature and pass defaults for parameters like position or speed.

---

## 4. Inheritance

### 4.1. Syntax
Inheritance reuses code from a base class.

**Syntax**:
```csharp
public class BaseClass
{
    protected int x;
    public virtual void Say() { Console.WriteLine("Base"); }
}

public class DerivedClass : BaseClass
{
    public override void Say() { Console.WriteLine("Derived"); }
}
```

### 4.2. Confusion Clarified
- **Overriding vs. Hiding**:
  - **Fragile**: Using `new` instead of `override` hides the base method, breaking polymorphism.
    ```csharp
    // Wrong: Hides method
    public new void Say() { }
    // Correct: Overrides method
    public override void Say() { }
    ```
  - **Error**: Non-polymorphic behavior (e.g., `BaseClass obj = new DerivedClass(); obj.Say();` calls base method).
  - **Fix**: Use `virtual` in base, `override` in derived.
  - **Test Mistake**: Forgetting `override` for methods like `Parler`.
- **Deep Hierarchies**:
  - **Fragile**: Deep inheritance (e.g., `Personnage` → `Nain` → `NainGuerrier` → `NainGuerrierVoleur`) can lead to fragile code if base changes.
  - **Fix**: Keep hierarchies shallow; use interfaces for behaviors (e.g., `IGuerrier`).
  - **Test Mistake**: Creating unnecessary subclasses instead of interfaces.
- **Accessing Base Members**:
  - **Fragile**: Incorrectly accessing base fields/methods without `base`.
    ```csharp
    // Wrong
    Say();
    // Correct
    base.Say();
    ```
  - **Fix**: Use `base` to call base class methods or constructors.

### 4.3. Do’s and Don’ts
- **Do**:
  - Use inheritance for “is-a” relationships (e.g., `Nain` is a `Personnage`).
  - Mark methods `virtual` if they need overriding.
  - Use `base` to access base members.
- **Don’t**:
  - Create deep hierarchies (e.g., avoid `NainGuerrierVoleurMagicien`).
  - Override without `virtual` in the base (causes error).
  - Forget `: base(...)` for base constructor.
- **Trick**: In tests, expect hierarchies like `Personnage` → `Nain`. Check for abstract methods (e.g., `Parler`) to override.

---

## 5. Abstract Classes

### 5.1. Syntax
Abstract classes cannot be instantiated and may have abstract methods.

**Syntax**:
```csharp
public abstract class Personnage
{
    protected string nom;
    public Personnage(string n, int x, int y, int v) { nom = n; }
    public abstract string Parler();
}
```

### 5.2. Confusion Clarified
- **Missing Abstract Method Implementation**:
  - **Fragile**: Failing to override abstract methods like `Parler` in `Nain` causes a compilation error.
    ```csharp
    // Wrong: Missing override
    public class Nain : Personnage { }
    // Correct
    public class Nain : Personnage
    {
        public Nain(string nom) : base(nom, 0, 0, 2) { }
        public override string Parler() { return "Groupf"; }
    }
    ```
  - **Fix**: Override all abstract methods in non-abstract derived classes.
  - **Test Mistake**: Forgetting to implement `Parler` in `Nain`.
- **Instantiating Abstract Classes**:
  - **Fragile**: Trying to create `new Personnage()` fails.
    ```csharp
    Personnage p = new Personnage("Test"); // Error
    ```
  - **Fix**: Use derived classes (e.g., `new Nain("Thorin")`).

### 5.3. Do’s and Don’ts
- **Do**:
  - Use abstract classes for shared state (e.g., `nom`, `pointsVie`).
  - Override all abstract methods in derived classes.
- **Don’t**:
  - Instantiate abstract classes.
  - Forget `override` for abstract methods.
- **Trick**: Check test problems for abstract classes like `Personnage` and ensure all abstract methods are implemented.

---

## 6. Interfaces

### 6.1. Syntax
Interfaces define a contract.

**Syntax**:
```csharp
public interface IGuerrier
{
    int Force { get; }
    void Attaquer(Personnage cible);
}

public class NainGuerrier : IGuerrier
{
    public int Force { get; }
    public void Attaquer(Personnage cible) { }
}
```

### 6.2. Confusion Clarified
- **Non-Public Interface Members**:
  - **Fragile**: Interface members must be public in the implementing class.
    ```csharp
    // Wrong
    void Attaquer(Personnage cible) { }
    // Correct
    public void Attaquer(Personnage cible) { }
    ```
  - **Error**: Compilation error if interface members aren’t public.
  - **Fix**: Always make interface implementations public.
  - **Test Mistake**: Making `Attaquer` private in `NainGuerrier`.
- **Missing Interface Members**:
  - **Fragile**: Failing to implement all interface members (e.g., `Force` or `Attaquer`) causes an error.
    ```csharp
    // Wrong
    public class NainGuerrier : IGuerrier { }
    ```
  - **Fix**: Implement all properties and methods.
- **Interfaces vs. Abstract Classes**:
  - **Fragile**: Using an abstract class for behaviors (e.g., `Guerrier` instead of `IGuerrier`) limits flexibility.
  - **Fix**: Use interfaces for “can-do” behaviors (e.g., `IGuerrier`), abstract classes for shared state.

### 6.3. Do’s and Don’ts
- **Do**:
  - Use interfaces for behaviors (e.g., `IGuerrier` for attacking).
  - Name interfaces with `I` prefix (e.g., `IVoleur`).
  - Make interface members public.
- **Don’t**:
  - Forget to implement all interface members.
  - Add implementation in interfaces (unless using default methods, rare in tests).
- **Trick**: Expect interfaces like `IGuerrier` for competencies. Check for required methods/properties.

---

## 7. Properties

### 7.1. Syntax
Properties control field access.

**Syntax**:
```csharp
public class MyClass
{
    private int myField;
    public int MyProperty
    {
        get { return myField; }
        set { myField = value; }
    }
    public int AutoProperty { get; set; }
}
```

### 7.2. Confusion Clarified
- **Validation in Properties**:
  - **Fragile**: Auto-implemented properties (`{ get; set; }`) don’t allow validation.
    ```csharp
    // Fragile: No validation
    public int Force { get; set; }
    ```
  - **Fix**: Use a backing field for validation:
    ```csharp
    private int force;
    public int Force
    {
        get { return force; }
        set { if (value >= 0 && value <= 100) force = value; }
    }
    ```
  - **Test Mistake**: Forgetting to validate `Force` (0–100) in `NainGuerrier`.

### 7.3. Do’s and Don’ts
- **Do**:
  - Use properties over public fields.
  - Make properties read-only if immutable (e.g., `public int Force { get; }`).
- **Don’t**:
  - Use public fields (e.g., `public int force;`).
  - Skip validation for constrained properties.
- **Trick**: Use read-only properties for test attributes like `Force` to ensure immutability.

---

## 8. Methods

### 8.1. Syntax
Methods define behavior.

**Syntax**:
```csharp
public class MyClass
{
    public void MyMethod(int param) { }
    public int Calculate(int x, int y) { return x + y; }
}
```

### 8.2. Confusion Clarified
- **Null Parameter Checks**:
  - **Fragile**: Not checking for null parameters can cause `NullReferenceException`.
    ```csharp
    // Fragile
    public void Attaquer(Personnage cible)
    {
        cible.SetPointsVie(cible.GetPointsVie() - 10);
    }
    ```
  - **Fix**: Add null checks:
    ```csharp
    public void Attaquer(Personnage cible)
    {
        if (cible == null) throw new ArgumentNullException(nameof(cible));
        cible.SetPointsVie(cible.GetPointsVie() - 10);
    }
    ```
  - **Test Mistake**: Forgetting null checks in methods like `Attaquer`.
- **Method Overloading Fragility**:
  - **Fragile**: Overloaded methods with similar signatures can cause confusion.
    ```csharp
    public void Do(int x) { }
    public void Do(double x) { }
    ```
  - **Fix**: Use distinct parameter types or names.

### 8.3. Do’s and Don’ts
- **Do**:
  - Use clear method names (e.g., `Attaquer`).
  - Validate parameters (e.g., null checks, ranges).
- **Don’t**:
  - Ignore null parameters.
  - Create overly complex methods.
- **Trick**: In tests, implement methods like `Attaquer` exactly as specified (e.g., “reduce health by Force%”).

---

## 9. Polymorphism

### 9.1. Syntax
Polymorphism allows different behaviors via base types.

**Syntax**:
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

### 9.2. Confusion Clarified
- **Forgetting `virtual`/`override`**:
  - **Fragile**: Without `virtual` in the base, `override` in the derived class fails.
    ```csharp
    // Wrong
    public void Say() { }
    public override void Say() { } // Error
    ```
  - **Fix**: Use `virtual` and `override`.
  - **Test Mistake**: Forgetting `override` for `Parler`.
- **Base vs. Derived Behavior**:
  - **Fragile**: Calling a base method instead of the overridden one.
    ```csharp
    // Fragile
    BaseClass obj = new DerivedClass();
    obj.Say(); // Depends on virtual/override
    ```
  - **Fix**: Ensure proper polymorphism setup.

### 9.3. Do’s and Don’ts
- **Do**:
  - Use `virtual`/`override` for polymorphism.
  - Test polymorphic behavior in `Main` if allowed.
- **Don’t**:
  - Use `new` to hide methods (breaks polymorphism).
  - Forget `override` for virtual/abstract methods.
- **Trick**: Expect `Personnage` references to `Nain` in tests; ensure overridden methods work.

---

## 10. Common Test Patterns

### 10.1. Abstract Base Class
- **Pattern**: Base class like `Personnage` with abstract methods (e.g., `Parler`).
- **Example**:
  ```csharp
  public abstract class Personnage
  {
      protected string nom;
      public Personnage(string n, int x, int y, int v) { nom = n; }
      public abstract string Parler();
  }
  public class Nain : Personnage
  {
      public Nain(string nom) : base(nom, 0, 0, 2) { }
      public override string Parler() { return "Groupf"; }
  }
  ```

### 10.2. Interfaces for Competencies
- **Pattern**: Interfaces like `IGuerrier` for behaviors.
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
- **Pattern**: Classes like `NainGuerrier`.
- **Example**:
  ```csharp
  public class NainGuerrier : Nain
  {
      public int Force { get; }
      public NainGuerrier(string nom, int force) : base(nom) { Force = force; }
  }
  ```

---

## 11. Do’s and Don’ts for Tests

### 11.1. Do’s
- Read the problem for constructor signatures (e.g., `Personnage` parameters).
- Validate inputs (e.g., `Force` 0–100).
- Override all abstract methods.
- Use `: base(...)` correctly.
- Test in `Main` if allowed.

### 11.2. Don’ts
- Modify provided classes (e.g., `Personnage`).
- Ignore abstract methods or interface members.
- Use public fields.
- Skip null checks or validation.
- Forget base constructor parameters.

---

## 12. Common Pitfalls and Tricks

### 12.1. Pitfalls
- **Missing Base Constructor Parameters**:
  ```csharp
  // Wrong
  public Nain(string nom) : base(nom) { }
  // Correct
  public Nain(string nom) : base(nom, 0, 0, 2) { }
  ```
- **Non-Public Interface Members**:
  ```csharp
  // Wrong
  void Attaquer(Personnage cible) { }
  // Correct
  public void Attaquer(Personnage cible) { }
  ```
- **No Validation**:
  ```csharp
  // Fragile
  public int Force { get; set; }
  // Correct
  public int Force { get; private set; }
  ```

### 12.2. Tricks
- Use auto-implemented properties for speed.
- Default parameters in `: base(...)` (e.g., `0, 0, 2`).
- Write `Main` to test:
  ```csharp
  static void Main()
  {
      Nain nain = new Nain("Thorin");
      Console.WriteLine(nain.Parler()); // Groupf
  }
  ```

---

## 13. Syntax Quick Reference

- **Class**: `public class MyClass { }`
- **Constructor**: `public MyClass(int x) { }`
- **Inheritance**: `public class Derived : Base { }`
- **Interface**: `public interface IMyInterface { void Method(); }`
- **Property**: `public int MyProperty { get; set; }`
- **Method**: `public void MyMethod() { }`
- **Override**: `public override void Method() { }`

---

## 14. Test Strategy
1. Identify base class (e.g., `Personnage`) and its constructor.
2. Check for interfaces (e.g., `IGuerrier`).
3. Implement derived classes with correct `: base(...)`.
4. Validate constraints (e.g., `Force` 0–100).
5. Test outputs in `Main` if allowed.

---

## 15. Example: Lord of the Rings Solution
```csharp
public abstract class Personnage
{
    protected string nom;
    protected double pointsVie;
    public Personnage(string n, int x, int y, int v) { nom = n; pointsVie = 1000; }
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
    public Nain(string nom) : base(nom, 0, 0, 2) { }
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
        if (cible == null) throw new ArgumentNullException(nameof(cible));
        double damage = cible.GetPointsVie() * (Force / 100.0);
        cible.SetPointsVie(cible.GetPointsVie() - damage);
    }
}
```

---

## 16. Final Tips
- Practice constructor chaining (e.g., `: base(nom, 0, 0, 2)`).
- Memorize interface and override syntax.
- Validate all inputs.
- Keep solutions simple for timed tests.
- Check base class signatures in the problem.

Good luck on your test! 🚀