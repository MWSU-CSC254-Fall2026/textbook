# Lesson 3 - Class Relationships and Visibility

### Why Class Relationships Matter

Object oriented programs are made of many small classes working together. Each class represents a piece of data or behavior, and the relationships between those classes determine how well the entire program holds together. Poorly designed relationships lead to confusing dependencies, tangled code, and difficulty making changes. Well designed relationships keep responsibilities separate and predictable.

This lesson explains the most common kinds of class relationships, how to express them in Java, and how to use visibility rules to protect each class’s internal details.

---

### Composition And HAS-A Relationships

Composition describes a strong form of ownership in which one object is considered a structural part of another. The lifetime of the part is tied to the lifetime of the whole. If the whole object no longer exists, the part typically has no meaningful standalone role.

Java does not have a keyword or syntax feature that directly encodes composition. Instead, composition emerges from design intent. You express it through how the objects are created and how they are used:

- The whole object usually creates the part internally.
- The part is not meant to be shared outside the whole.
- The part exists only to support the behavior of the whole.

```java
public class Engine {

    private int horsepower;

    public Engine(int horsepower) {
        this.horsepower = horsepower;
    }

    public int getHorsepower() {
        return horsepower;
    }
}

public class Car {

    private final Engine engine;

    public Car(int horsepower) {
        engine = new Engine(horsepower);
    }

    public int totalPower() {
        return engine.getHorsepower();
    }
}
```

This code does not show composition through any special syntax. Instead, the design indicates that a car owns its engine. The car creates the engine itself, manages its lifetime, and uses it exclusively. The relationship is understood through intent and usage, not through Java language features.

---

### Aggregation As Shared Ownership

Aggregation represents a weaker form of ownership. A class may refer to objects that are not truly its parts. Those objects can be created elsewhere, shared elsewhere, or persist independently after the aggregator is no longer used.

As with composition, Java provides no language-level mechanism for marking a relationship as aggregation. The difference comes from the surrounding design:

- The objects are usually provided from outside rather than constructed internally.
- The objects have a meaningful life beyond the aggregator.
- Multiple structures may safely refer to the same object.

```java
public class Student {

    private String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

public class Classroom {

    private final Student[] roster;

    public Classroom(Student[] roster) {
        this.roster = roster;
    }

    public int countStudents() {
        return roster.length;
    }
}
```

Although the syntax resembles composition, the intent is different. A classroom does not own the students. Those students might appear in many other contexts, such as a database or an advising system. The lifetime of each student is not controlled by the classroom, and this independence is what marks the relationship as aggregation.

---

### Association As Knowledge Without Ownership

Association is the loosest relationship. One class simply knows about another. There is no implication of ownership, no control over lifetime, and no expectation that the referenced object exists solely for the referring class.

In Java, association often appears when a method takes an object as a parameter or when a temporary reference is stored during an interaction. Because this is the most flexible relationship, it also looks the most ordinary in code.

```java
public class Book {

    private String title;

    public Book(String title) {
        this.title = title;
    }
}

public class Librarian {

    private Book lastCheckedOut;

    public void checkOut(Book b) {
        lastCheckedOut = b;
    }
}
```

This code shows that a librarian interacts with books, but neither object owns the other. The relationship is incidental and temporary. Once again, the Java syntax does not distinguish association from other relationships. The meaning arises from how the objects behave in the domain and how long the relationship lasts.

---

### Dependency Direction And UML Arrows

Developers sometimes use Universal Modeling Language (UML) diagrams to draw out a 'map' of software before they start designing. UML diagrams have a lot of components and uses, but one of those uses is to show dependency. UML diagrams use arrows to show which class depends on which. The direction matters because it determines the flow of knowledge.

A simple ASCII representation:

```
Car --> Engine
```

This reads “Car depends on Engine.” If Engine changes, Car may need to change. Recognizing dependency direction helps you avoid cycles that make programs harder to maintain.

General rules:
- A class should depend only on what it truly needs.
- Avoid two classes depending on each other unless absolutely necessary.
- Try to keep relationships one-way so the design stays untangled.

---

### Visibility And Scope

Visibility controls what parts of a class can be accessed from outside. It protects invariants and prevents accidental misuse.

Java has four levels:

- private - accessible only inside the same class
- package-private - accessible inside the same package (no keyword)
- protected - accessible inside the same package and through subclasses
- public - accessible from anywhere

```java
public class BankAccount {

    private int balance;            // internal state
    protected String accountName;   // subclass access
    int branchId;                   // package access
    public int getBalance() {       // safe external access
        return balance;
    }

    public void deposit(int amount) {
        balance = balance + amount;
    }
}
```

The field *balance* is private because only the class should modify it directly. This ensures the invariant “balance is always valid” is kept inside the class.

---

### Designing Multi Class Interactions

When multiple classes collaborate, the goal is to give each class a clear, single purpose and the smallest amount of knowledge needed to perform its job. You want each interaction to be intentional, not accidental.

Example: a simplified order system.

```java
public class Product {

    private String name;
    private int price;

    public Product(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public int getPrice() {
        return price;
    }
}

public class OrderLine {

    private Product product;
    private int quantity;

    public OrderLine(Product product, int quantity) {
        this.product = product;
        this.quantity = quantity;
    }

    public int lineTotal() {
        return product.getPrice() * quantity;
    }
}

public class Order {

    private OrderLine[] lines;

    public Order(OrderLine[] lines) {
        this.lines = lines;
    }

    public int totalCost() {
        int total = 0;
        for (OrderLine line : lines) {
            total = total + line.lineTotal();
        }
        return total;
    }
}
```

Each class has a focused responsibility. *Product* stores pricing information, *OrderLine* combines a product with quantity, and *Order* computes totals. The relationships are simple and flow in one direction.

---

### Bidirectional Associations

Sometimes two classes store references to each other. This is called a bidirectional association.

```
A <--> B
```

Although it can model certain real situations, it easily causes problems:

- Update cycles, where each tries to change the other
- Confusing ownership rules
- Difficulty enforcing invariants
- Harder testing, because each side requires the other

Use this sparingly. Most of the time, one direction of knowledge is enough.

---

### Cohesion And Coupling

Cohesion describes how well the parts of a single class belong together. High cohesion means the class has a clear job.

Coupling describes how strongly one class depends on another. Low coupling means classes interact cleanly and with minimal entanglement.

As a general design rule:
- Aim for high cohesion inside each class
- Aim for low coupling between classes

These two ideas combine to produce code that is easier to extend and reason about.

---

### Introducing The Single Responsibility Principle

The Single Responsibility Principle states that a class should have one reason to change. This helps avoid “god classes” that try to do everything.

A simple example:

Bad:

```java
public class Report {

    private String data;

    public String loadDataFromDatabase() {
        return "sample";
    }

    public void printReport() {
        System.out.println(data);
    }
}
```

Good separation:

```java
public class ReportData {

    private String data;

    public ReportData(String data) {
        this.data = data;
    }

    public String getText() {
        return data;
    }
}

public class ReportPrinter {

    public void print(ReportData d) {
        System.out.println(d.getText());
    }
}
```

Separating responsibilities reduces accidental coupling and makes later changes predictable.

---

### Using Interfaces To Stabilize Relationships

Interfaces allow a class to depend on behavior rather than a concrete type. This prevents tight coupling and makes future changes safer.

```java
public interface Priceable {
    int getPrice();
}

public class Item implements Priceable {

    private int price;

    public Item(int price) {
        this.price = price;
    }

    public int getPrice() {
        return price;
    }
}

public class Cart {

    private Priceable[] items;

    public Cart(Priceable[] items) {
        this.items = items;
    }

    public int total() {
        int sum = 0;
        for (Priceable p : items) {
            sum = sum + p.getPrice();
        }
        return sum;
    }
}
```

The class *Cart* now depends on the interface *Priceable*. This keeps the relationship stable even if different item types are added later. We will discuss interfaces in detail in the next chapter.
