# Lesson 7 - Interfaces And Abstraction

### Why Abstraction Matters

As programs grow, concrete classes become a liability when they are tightly coupled to each other. When one class depends directly on another specific class, any change to that class risks breaking multiple parts of the system. This leads to brittle code that is difficult to extend, test, or reason about.

Abstraction exists to control this complexity. It allows you to define what a piece of code must do without committing to how it does it. In Java, abstraction is primarily achieved using interfaces and abstract classes.

Both tools exist to separate behavior from implementation, but they do so in different ways and for different reasons.

---

### Why Interfaces Exist

Interfaces exist to define pure behavior contracts. An interface describes what capabilities a class must provide, without storing state or enforcing implementation details.

When you depend on an interface instead of a concrete class, you depend on behavior rather than structure. This dramatically reduces coupling and makes systems easier to change over time.

Interfaces are most powerful when multiple unrelated classes can fulfill the same role.

---

### What An Interface Is

An interface in Java is a type that declares method signatures without providing implementation. A class that implements an interface agrees to provide concrete behavior for all declared methods.

Interfaces have the following properties:

- They cannot be instantiated directly
- They contain method signatures, not method bodies
- They represent capability, not state
- A class may implement multiple interfaces

This is the simplest possible interface:

```java
public interface Payable
{
    double calculatePay();
}
```

This interface says nothing about how pay is calculated. It only states that anything considered Payable must be able to calculate pay.

The lack of implementation is intentional. The interface describes behavior without enforcing design.

---

### Implementing An Interface

A class implements an interface using the implements keyword and must provide all required methods.

```java
public class HourlyEmployee implements Payable
{
    private final double hourlyRate;
    private final int hoursWorked;

    public HourlyEmployee(double hourlyRate, int hoursWorked)
    {
        this.hourlyRate = hourlyRate;
        this.hoursWorked = hoursWorked;
    }

    @Override
    public double calculatePay()
    {
        return hourlyRate * hoursWorked;
    }
}
```

The class now promises that it fulfills the Payable contract. Any code that works with Payable does not need to know that this is an HourlyEmployee.

This separation allows behavior to change independently from usage.

---

### Programming To An Abstraction

Programming to an abstraction means writing code that depends on interfaces or abstract classes instead of concrete implementations.

Consider the following method:

```java
public double processPayroll(HourlyEmployee employee)
{
    return employee.calculatePay();
}
```

This method only works with HourlyEmployee. It cannot handle salaried workers, contractors, or any other pay model.

Now compare it to this version:

```java
public double processPayroll(Payable worker)
{
    return worker.calculatePay();
}
```

The logic is unchanged, but the design is now flexible. New pay models can be added without modifying this method.

This is the core benefit of abstraction.

---

### Abstract Classes And Why They Exist

Abstract classes sit between concrete classes and interfaces. They allow shared implementation while still preventing direct instantiation.

An abstract class can:

- Contain fields
- Contain implemented methods
- Contain abstract methods
- Enforce a partial design

Abstract classes are used when classes share common state or behavior but should not be instantiated on their own.

---

### Defining An Abstract Class

An abstract class uses the abstract keyword and may contain abstract methods, which have no implementation.

```java
public abstract class Employee
{
    private final String name;

    protected Employee(String name)
    {
        this.name = name;
    }

    public String getName()
    {
        return name;
    }

    public abstract double calculatePay();
}
```

This class defines shared state and behavior while leaving pay calculation to subclasses.

The class cannot be instantiated directly.

---

### Extending An Abstract Class

Concrete subclasses must implement all abstract methods.

```java
public class SalariedEmployee extends Employee
{
    private final double salary;

    public SalariedEmployee(String name, double salary)
    {
        super(name);
        this.salary = salary;
    }

    @Override
    public double calculatePay()
    {
        return salary;
    }
}
```

Abstract classes allow shared logic to live in one place while still enforcing specialization.

This avoids duplication without sacrificing correctness.

---

### Interfaces Versus Abstract Classes

Interfaces and abstract classes serve different design purposes.

Interfaces are best when:

- You want to define capability
- Multiple unrelated classes may implement the behavior
- You want maximum flexibility
- You want to avoid shared state

Abstract classes are best when:

- Classes share common fields or implementation
- You want to enforce a base structure
- There is a clear is-a relationship
- Multiple inheritance of classes is not required

In practice, interfaces are more common for system boundaries, while abstract classes are often used internally within hierarchies.

---

### Dependency Inversion Light Introduction

Dependency inversion is a principle stating that high-level code should not depend on low-level implementation details. Both should depend on abstractions.

Bad dependency direction:

```java
public class ReportGenerator
{
    private final FilePrinter printer = new FilePrinter();

    public void generate()
    {
        printer.print("Report content");
    }
}
```

Better dependency direction:

```java
public interface Printer
{
    void print(String content);
}
```

```java
public class FilePrinter implements Printer
{
    @Override
    public void print(String content)
    {
        System.out.println("Writing to file: " + content);
    }
}
```

```java
public class ReportGenerator
{
    private final Printer printer;

    public ReportGenerator(Printer printer)
    {
        this.printer = printer;
    }

    public void generate()
    {
        printer.print("Report content");
    }
}
```

The ReportGenerator controls behavior through abstraction, not implementation.

---

### Interfaces And Plug In Architectures

A plug-in architecture allows behavior to be swapped without changing the core system. Interfaces are the foundation of this idea.

```java
public interface PaymentProcessor
{
    boolean process(double amount);
}
```

```java
public class CreditCardProcessor implements PaymentProcessor
{
    @Override
    public boolean process(double amount)
    {
        return amount <= 5000;
    }
}
```

```java
public class CashProcessor implements PaymentProcessor
{
    @Override
    public boolean process(double amount)
    {
        return true;
    }
}
```

```java
public class Checkout
{
    private final PaymentProcessor processor;

    public Checkout(PaymentProcessor processor)
    {
        this.processor = processor;
    }

    public boolean completePurchase(double amount)
    {
        return processor.process(amount);
    }
}
```

New payment methods plug in without modifying Checkout.

---

### Strategy Pattern

The Strategy Pattern formalizes interchangeable behavior using interfaces.

```java
public interface SortStrategy
{
    void sort(int[] data);
}
```

```java
public class AscendingSort implements SortStrategy
{
    @Override
    public void sort(int[] data)
    {
        java.util.Arrays.sort(data);
    }
}
```

```java
public class DescendingSort implements SortStrategy
{
    @Override
    public void sort(int[] data)
    {
        java.util.Arrays.sort(data);
        for (int i = 0; i < data.length / 2; i++)
        {
            int temp = data[i];
            data[i] = data[data.length - 1 - i];
            data[data.length - 1 - i] = temp;
        }
    }
}
```

```java
public class DataSet
{
    private final SortStrategy strategy;

    public DataSet(SortStrategy strategy)
    {
        this.strategy = strategy;
    }

    public void sort(int[] data)
    {
        strategy.sort(data);
    }
}
```

Behavior changes without modifying the DataSet class.

---

### Comparable Interface

The Comparable interface allows objects to define their natural ordering.

```java
public class Student implements Comparable<Student>
{
    private final String name;
    private final int id;

    public Student(String name, int id)
    {
        this.name = name;
        this.id = id;
    }

    public String getName()
    {
        return name;
    }

    @Override
    public int compareTo(Student other)
    {
        return Integer.compare(this.id, other.id);
    }
}
```

Implementing Comparable allows collections of Student objects to be sorted automatically.

---

### Comparator Interface

Comparator defines comparison logic externally.

```java
import java.util.Comparator;

public class StudentNameComparator implements Comparator<Student>
{
    @Override
    public int compare(Student a, Student b)
    {
        return a.getName().compareTo(b.getName());
    }
}
```

Comparator allows multiple sorting strategies without modifying the class being sorted.

---

### Abstraction As A Design Mindset

Abstraction is not about adding complexity. It is about choosing where complexity belongs.

Good abstraction answers these questions:

- What behavior does this system need
- What should remain stable over time
- What should be allowed to change

Interfaces and abstract classes are tools for enforcing those decisions.

By the end of this lesson, you should understand abstraction not as theory, but as the foundation of flexible, maintainable software design.

---