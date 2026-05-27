# Lesson 4 - Inheritance

### Why Inheritance Exists

Inheritance is a mechanism for modeling specialization. It allows one class to represent a more specific version of another class while reusing and extending its behavior.

Inheritance answers the question: “Is this thing a more specific kind of that thing?”

For example, a *Textbook* is a *Book*. A *Manager* is an *Employee*. A *SavingsAccount* is a *BankAccount*. In each case, the specialized object shares the core identity of the more general object but adds or refines behavior.

Inheritance is powerful, but it is also one of the most commonly misused features in object oriented programming. This lesson focuses on using inheritance deliberately, safely, and sparingly.

---

### The IS-A Relationship

An inheritance relationship must represent a true “is-a” relationship.

If class B extends class A, then every instance of B must also be a valid instance of A in every meaningful sense.

This rule is conceptual, not syntactic. Java will allow inheritance even when the relationship is nonsensical, so the responsibility falls on you to decide when it makes sense.

Examples of valid IS-A relationships:
- A *Dog* is an *Animal*
- A *Car* is a *Vehicle*
- A *StudentAccount* is an *Account*

Examples of invalid or suspicious relationships:
- A *Car* is an *Engine*
- A *Square* is a *Rectangle* (classic design trap)
- A *Store* is an *Inventory*

When the IS-A relationship is weak or forced, inheritance will create more problems than it solves.

---

### Superclasses and Subclasses

In Java, inheritance is expressed using the `extends` keyword.

- The superclass defines common state and behavior.
- The subclass inherits that behavior and may add or override functionality.

```java
public class Book
{
    private String title;
    private String author;

    public Book(String title, String author)
    {
        this.title = title;
        this.author = author;
    }

    public String getDescription()
    {
        return title + " by " + author;
    }
}
```

```java
public class Textbook extends Book
{
    private String subject;

    public Textbook(String title, String author, String subject)
    {
        super(title, author);
        this.subject = subject;
    }

    public String getSubject()
    {
        return subject;
    }
}
```

In this example, *Textbook* automatically gains all public and protected behavior of *Book*. You do not copy fields or methods. They are inherited.

The subclass focuses only on what makes it different.

---

### Constructor Chaining and super

When a subclass is created, the superclass portion of the object must be constructed first. Java enforces this through constructor chaining.

A subclass constructor must call a superclass constructor using `super(...)`. If you do not explicitly call it, Java will attempt to call a no-argument superclass constructor automatically.

```java
public Textbook(String title, String author, String subject)
{
    super(title, author);
    this.subject = subject;
}
```

The `super` call must be the first statement in the constructor. This ensures that the base class is fully initialized before the subclass adds its own state.

This rule prevents partially constructed objects and enforces a clear construction order.

---

### Method Overriding

A subclass may replace or refine behavior inherited from its superclass by overriding methods.

To override a method:
- The method name must match exactly.
- The parameter list must match exactly.
- The return type must be compatible.

```java
@Override
public String getDescription()
{
    return super.getDescription() + " (Textbook)";
}
```

The `@Override` annotation is optional but strongly recommended. It allows the compiler to verify that a method is truly being overridden and not accidentally redefined.

Overriding allows specialized behavior while preserving the public contract of the superclass.

---

### Polymorphism Through Inheritance

Inheritance enables polymorphism, which means that a superclass reference can point to a subclass object.

```java
Book book = new Textbook("Calculus I", "Stewart", "Mathematics");
System.out.println(book.getDescription());
```

Even though the variable type is *Book*, the overridden method in *Textbook* is called. The method executed depends on the actual object, not the variable type.

This allows code to operate on general types while benefiting from specialized behavior. On first glance this looks strange and confusing, but this behavior is incredibly useful for certain architectures and program structures.

---

### Abstract Classes

An abstract class represents an incomplete concept. It cannot be instantiated directly and may contain abstract methods that subclasses must implement.

```java
public abstract class Account
{
    protected double balance;

    public Account(double balance)
    {
        this.balance = balance;
    }

    public abstract void applyMonthlyUpdate();

    public double getBalance()
    {
        return balance;
    }
}
```

```java
public class SavingsAccount extends Account
{
    public SavingsAccount(double balance)
    {
        super(balance);
    }

    @Override
    public void applyMonthlyUpdate()
    {
        balance += balance * 0.01;
    }
}
```

Abstract classes allow you to define shared structure while forcing subclasses to provide specific behavior. They are especially useful when every subclass must follow the same conceptual rules but with different implementations.

---

### When NOT To Use Inheritance

Inheritance should not be your default design choice.

Avoid inheritance when:
- The relationship is “has-a” instead of “is-a”
- Behavior needs to vary at runtime
- You want to avoid tight coupling between classes

Example using composition instead of inheritance:

```java
public class Engine
{
    public void start()
    {
        System.out.println("Engine started");
    }
}
```

```java
public class Car
{
    private Engine engine;

    public Car()
    {
        engine = new Engine();
    }

    public void start()
    {
        engine.start();
    }
}
```

A *Car* has an *Engine*. It is not an *Engine*. Composition models this relationship accurately and keeps responsibilities clean.

Inheritance locks classes together permanently. Composition allows flexibility.

---

### Protected Fields and Their Tradeoffs

The `protected` keyword allows subclasses to access superclass fields directly.

Pros:
- Reduces boilerplate getters
- Simplifies some inheritance hierarchies

Cons:
- Breaks encapsulation
- Allows subclasses to depend on internal representation
- Makes refactoring harder

A safer alternative is:
- Keep fields private
- Provide protected or public methods instead

Use `protected` fields only when you are confident about the inheritance hierarchy and control all subclasses.

---

### Inheritance in Other Languages

Java allows only single inheritance of classes. A class may extend exactly one superclass.

Other languages differ:
- C++ allows multiple inheritance
- Python allows multiple inheritance
- Java avoids it to reduce ambiguity and complexity

Java uses interfaces to provide multiple behavioral contracts without inheriting implementation.
