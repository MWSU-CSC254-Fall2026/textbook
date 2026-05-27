# Lesson 6 - Polymorphism

### What Polymorphism Actually Means

Polymorphism means many forms. In object oriented programming, it describes the ability to treat different concrete objects as instances of a shared supertype, while still allowing each object to behave according to its own implementation.

This is not about syntax tricks or convenience. Polymorphism is a core mechanism that allows programs to grow without becoming tangled in conditionals and special cases. When used correctly, it replaces decision making logic with behavior that is distributed across related types.

You already use polymorphism implicitly whenever you work with inheritance or interfaces. This lesson makes that behavior explicit and teaches you how to design around it intentionally.

---

### Dynamic Dispatch and Runtime Method Selection

Dynamic dispatch is the mechanism that makes polymorphism work. When a method is called on an object reference, Java decides which method implementation to run at runtime, based on the actual object type, not the variable type.

Consider the following example.

```java
public class Animal
{
    public void speak()
    {
        System.out.println("The animal makes a sound");
    }
}
```

```java
public class Dog extends Animal
{
    @Override
    public void speak()
    {
        System.out.println("The dog barks");
    }
}
```

```java
Animal a = new Dog();
a.speak();
```

Even though the variable `a` is typed as `Animal`, the output will be:

```text
The dog barks
```

This happens because Java looks at the actual object stored in memory, not the declared variable type, when choosing which overridden method to execute.

Dynamic dispatch is what allows a single method call to produce different behavior depending on the object involved.

---

### Overriding Versus Overloading

Overriding and overloading are frequently confused, but they solve very different problems.

Overriding happens across a class hierarchy and is resolved at runtime. Overloading happens within the same class and is resolved at compile time.

```java
public class Printer
{
    public void print(int value)
    {
        System.out.println(value);
    }

    public void print(String value)
    {
        System.out.println(value);
    }
}
```

This is method overloading. Java selects which method to call based on the parameter types at compile time.

By contrast, overriding depends on inheritance.

```java
public class Base
{
    public void show()
    {
        System.out.println("Base");
    }
}

public class Derived extends Base
{
    @Override
    public void show()
    {
        System.out.println("Derived");
    }
}
```

```java
Base b = new Derived();
b.show();
```

This is overriding, and the decision is made at runtime.

Polymorphism relies on overriding, not overloading. Overloading does not provide substitutable behavior across types.

---

### The Liskov Substitution Principle

The Liskov Substitution Principle states:

If a type B is a subtype of type A, then objects of type A should be replaceable with objects of type B without breaking the program.

In practical terms, this means that subclasses must honor the behavioral expectations established by their superclass.

For example, if a superclass promises that a method returns a non-negative number, a subclass must not violate that expectation.

Violations of this principle often look like this:

- A subclass throws exceptions that the base class does not
- A subclass weakens method guarantees
- A subclass changes the meaning of a method

When Liskov is violated, polymorphism becomes unsafe, and callers are forced to special-case subclasses, defeating the purpose of inheritance.

---

### Programming to the Supertype

Programming to the supertype means writing code that depends on an abstract type, such as a superclass or interface, rather than a specific implementation.

```java
public void makeAnimalSpeak(Animal animal)
{
    animal.speak();
}
```

This method does not care whether the object is a `Dog`, `Cat`, or any other subclass. It only relies on the contract defined by `Animal`.

This approach leads to code that is:

- Easier to extend
- Easier to test
- Less tightly coupled
- More resilient to change

If new animal types are added later, this method does not need to change at all.

---

### Replacing Branching Logic With Polymorphism

A common beginner design uses conditionals to select behavior.

```java
public void drawShape(String type)
{
    if (type.equals("circle"))
    {
        drawCircle();
    }
    else if (type.equals("square"))
    {
        drawSquare();
    }
}
```

This design does not scale. Every new shape requires modifying this method.

Polymorphism eliminates the need for branching.

```java
public abstract class Shape
{
    public abstract void draw();
}
```

```java
public class Circle extends Shape
{
    @Override
    public void draw()
    {
        System.out.println("Drawing a circle");
    }
}
```

```java
public class Square extends Shape
{
    @Override
    public void draw()
    {
        System.out.println("Drawing a square");
    }
}
```

```java
public void render(Shape shape)
{
    shape.draw();
}
```

Now behavior is selected by the object itself, not by conditional logic. Adding a new shape requires no changes to existing rendering code.

---

### Families of Types

A family of types is a group of related classes that share a common abstraction and differ only in their behavior.

Examples include:

- Shapes that can be drawn
- Game entities that can update and render
- Payment methods that can process transactions
- File formats that can be saved and loaded

Each family has:

- A shared contract
- Multiple interchangeable implementations
- A caller that relies only on the contract

Polymorphism allows these families to grow naturally without rewriting existing code.

---

### Polymorphism in Plugin and Extension Systems

Many extensible systems are built entirely around polymorphism.

A plugin system typically defines an interface or abstract base class. External modules implement that contract and are loaded dynamically.

```java
public interface Plugin
{
    void execute();
}
```

The core application never needs to know which plugins exist ahead of time. It simply executes whatever implementations are available.

This pattern appears in graphics pipelines, game engines, build tools, and IDEs.

---

### Double Dispatch (Light Overview)

Single dispatch selects behavior based on the runtime type of one object. Double dispatch selects behavior based on the runtime types of two objects.

Java does not support double dispatch directly, but it can be simulated using method calls that pass `this` back into another object.

This technique is useful in cases like collision systems or type based interactions, but it is considered advanced and should be used carefully.

---

### Abstracting Operations Versus Data Structures

Polymorphism is best used to abstract operations, not data storage.

If types differ primarily in how they behave, polymorphism is a good fit. If they differ only in data representation, composition or configuration is usually better.

Misusing inheritance to represent data variations leads to rigid hierarchies and brittle designs.

---

### Real World Examples

Polymorphism appears naturally in many domains:

- Graphics systems where shapes draw themselves
- Game engines where entities update independently
- UI frameworks where components respond to events
- File systems where different formats load and save differently

In each case, the calling code is simplified by trusting the object to know how to behave.

---

### Key Takeaways

- Polymorphism allows different objects to respond differently to the same method call
- Dynamic dispatch selects methods at runtime based on actual object type
- Overriding enables polymorphism, overloading does not
- Liskov Substitution ensures polymorphism remains safe
- Programming to supertypes produces flexible, extensible designs
- Polymorphism replaces branching logic with distributed behavior

This lesson completes the core object oriented trio: encapsulation, inheritance, and polymorphism. Together, they enable systems that grow without collapsing under their own complexity.