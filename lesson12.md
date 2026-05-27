# Lesson 12 - Design Patterns

### Why Design Patterns Exist

Design patterns are named, reusable solutions to common design problems. They do not provide code you copy directly. Instead, they provide a shared vocabulary and a proven structure for solving recurring architectural challenges.

Patterns help you recognize when a design problem is familiar and guide you toward a solution that balances flexibility, clarity, and maintainability.

A design pattern is not a requirement. It is a tool. Knowing patterns helps you reason about design choices, not blindly apply them.

---

### Strategy Pattern

The Strategy pattern encapsulates interchangeable behavior behind a common interface. Instead of hard-coding conditional logic, behavior is selected by providing a different implementation.

This pattern ties directly back to interfaces and abstraction from Week 7.

Conceptually, Strategy answers the question: “What varies, and how do I isolate it?”

```java
public interface PaymentStrategy
{
    void pay(double amount);
}
```

```java
public class CreditCardPayment implements PaymentStrategy
{
    public void pay(double amount)
    {
        System.out.println("Paid with credit card: " + amount);
    }
}
```

```java
public class CashPayment implements PaymentStrategy
{
    public void pay(double amount)
    {
        System.out.println("Paid with cash: " + amount);
    }
}
```

```java
public class Checkout
{
    private PaymentStrategy strategy;

    public Checkout(PaymentStrategy strategy)
    {
        this.strategy = strategy;
    }

    public void process(double amount)
    {
        strategy.pay(amount);
    }
}
```

The Checkout class does not know or care how payment works. You can introduce new payment types without modifying existing logic. This reduces branching, improves testability, and aligns with the “program to an abstraction” principle.

---

### Factory Method Pattern

The Factory Method pattern centralizes object creation logic. Instead of constructing objects directly with new, creation is delegated to a method that decides which concrete type to return.

This pattern addresses the problem of tight coupling to concrete classes.

```java
public abstract class Document
{
    public abstract void print();
}
```

```java
public class PdfDocument extends Document
{
    public void print()
    {
        System.out.println("Printing PDF");
    }
}
```

```java
public class WordDocument extends Document
{
    public void print()
    {
        System.out.println("Printing Word document");
    }
}
```

```java
public class DocumentFactory
{
    public static Document create(String type)
    {
        if (type.equalsIgnoreCase("pdf"))
        {
            return new PdfDocument();
        }

        if (type.equalsIgnoreCase("word"))
        {
            return new WordDocument();
        }

        throw new IllegalArgumentException("Unknown document type");
    }
}
```

Factories separate creation from usage. When object creation becomes complex or conditional, factories prevent that complexity from spreading throughout your codebase.

---

### Singleton Pattern

The Singleton pattern ensures that only one instance of a class exists and provides global access to it.

```java
public class Configuration
{
    private static Configuration instance;

    private Configuration()
    {
    }

    public static Configuration getInstance()
    {
        if (instance == null)
        {
            instance = new Configuration();
        }

        return instance;
    }
}
```

While this pattern is common, it is often discouraged.

Singletons introduce global state, make testing harder, hide dependencies, and create tight coupling. In most cases, dependency injection or passing objects explicitly is a better design choice.

Singleton should be used sparingly and deliberately, not by default.

---

### Composite Pattern

The Composite pattern allows you to treat individual objects and groups of objects uniformly. It is commonly used for tree-like structures.

```java
public interface FileComponent
{
    int getSize();
}
```

```java
public class FileLeaf implements FileComponent
{
    private int size;

    public FileLeaf(int size)
    {
        this.size = size;
    }

    public int getSize()
    {
        return size;
    }
}
```

```java
import java.util.ArrayList;
import java.util.List;

public class Directory implements FileComponent
{
    private List<FileComponent> children = new ArrayList<>();

    public void add(FileComponent component)
    {
        children.add(component);
    }

    public int getSize()
    {
        int total = 0;

        for (FileComponent component : children)
        {
            total += component.getSize();
        }

        return total;
    }
}
```

Composite structures appear in file systems, UI hierarchies, and scene graphs. The key idea is that clients do not need to distinguish between a single object and a group of objects.

---

### Observer Pattern

The Observer pattern defines a one-to-many dependency between objects. When one object changes state, all registered observers are notified.

This pattern supports event-driven design.

```java
public interface Observer
{
    void update(int value);
}
```

```java
import java.util.ArrayList;
import java.util.List;

public class Subject
{
    private List<Observer> observers = new ArrayList<>();
    private int state;

    public void addObserver(Observer observer)
    {
        observers.add(observer);
    }

    public void setState(int value)
    {
        state = value;
        notifyObservers();
    }

    private void notifyObservers()
    {
        for (Observer observer : observers)
        {
            observer.update(state);
        }
    }
}
```

Observer decouples producers of change from consumers of change. Many modern frameworks implement observer-like behavior through listeners, callbacks, or reactive streams.

---

### Optional Patterns Overview

Several additional patterns are worth recognizing:

- Adapter translates one interface into another expected by a client.
- Decorator adds behavior to objects dynamically without inheritance.
- Command encapsulates actions as objects, enabling undo or queuing.
- MVC, MVP, and MVVM are architectural patterns that organize entire applications rather than individual classes.

These patterns become more relevant as systems grow larger and more modular.

---

### When Not to Use Patterns

Patterns are solutions, not goals.

Using patterns too early or unnecessarily leads to overengineering, increased complexity, and harder-to-read code. A simple solution is often better than a “clever” one.

You should introduce a pattern only when a real design pressure exists, such as duplication, rigidity, or frequent change.

Recognizing when not to use a pattern is just as important as knowing how to implement one.

---