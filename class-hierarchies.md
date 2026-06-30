# Class Hierarchies

Before we explore the core concepts of polymorphism and advanced inheritance, we must establish the foundational mechanics of how classes relate to one another in Java. This document defines the essential terminology and rules you need to understand before proceeding.

## Class Hierarchies: Parents and Children

In Java, and most other object-oriented programming languages, classes can interact with each other in specific ways. One of the most common relationships between classes is that of a `parent-child` relationship, also called a `superclass-subclass` relationship.

You often see this when two classes have shared characteristics, or when one class is acting as a 'general' class and another as a more specific version of that general class. Here is an example:

```java
// This is the 'parent class', or 'superclass'. Notice how it's very general.
// It is just the same as any other class. Being a 'parent' doesn't require any extra syntax.
public class Vehicle 
{
    private String type;

    public Vehicle(String type) 
    {
        this.type = type;
    }

    public void startEngine() 
    {
        System.out.println("Starting engine...");
    }
}

// This is the 'child class', or 'subclass'. Notice how it's more specific than its parent.
// It, too, is just like a standard class, but there are a couple new terms added.
public class Car extends Vehicle // 'extends Vehicle' declares this class a child of Vehicle.
{
    private String brand;

    public Car(String brand) 
    {
      	// This 'super' keyword is new to you too. We will discuss this later.
        super("Car");
        this.brand = brand;
    }
    
    public void honk()
    {
        System.out.println("Beep Beep");
    }
}
```

This mechanism of ordering classes based on a hierarchy of specificity and functionality is called **Inheritance**. This is *really* useful, as you will come to find out.

One thing about inheritance is that it establishes an "is-a" relationship. In this example, a `Car` is a child of `Vehicle`, and thus a `Car` ***is a*** `Vehicle`. This is one way, as a `Vehicle` is not necessarily a `Car`. This "is-a" rule can help establish what should and shouldn't be a subclass. 

## The `super` Keyword

Because a child inherits from its parent, there are times when the two need to interact explicitly. We use the `super` keyword for this. `super` can do all kinds of things, but most commonly it is used to call the parent's constructor, as you can see in the above example. In actuality, `super` is literally a reference variable that points at the parent object.

When I said that the `super` keyword is used most often for calling the parent's constructor, it is actually more accurate to say that this is *required* behavior. A child *must* call its parent's constructor, but whether it's done explicitly is up to you. There are some rules related to `super`. 

- `super()` **must** be the very first line of code in the subclass's constructor.
- If `super()` is not explicitly used, Java automatically inserts a hidden call to the parent's no-argument constructor as the first line.
  - In this manner, `super()` is called whether you want to call it or not.

## Method Overriding

**Method Overriding** is a pretty cool and very useful thing that you can do in a subclass. Let's take a look at an example:

```java
public class Animal 
{
    public Animal(String name) { /* initializes name */ }
    
    public void makeSound() { System.out.println("Generic animal sound"); }
}

public class Dog extends Animal 
{
    public Dog(String name) 
    {
        super(name);
    }
    
  	// This is the overridden method
    public void makeSound() 
    {
        System.out.println("Bark!"); 
    }
}
```

So, as you can see, both `Animal` and `Dog` have the same method `makeSound()`. The point of overriding is to add child-specific functionality to an existing method without making an entirely new method.

### Why Do We Even Override Methods?

Before we get into the strict rules, you might be wondering: *why* do we even override methods? If the parent class already has `makeSound()`, why not just create a brand new method called `bark()` in the `Dog` class? 

The answer comes down to **uniformity and future-proofing**. When we override, we are keeping the exact same method name and parameters. This means later on, when we want to make a bunch of different animals make a sound, we don't need to know if it's a `Dog`, a `Cat`, or a `Bird`. We can just call `.makeSound()` on all of them, and Java will automatically figure out which specific version to run based on the actual object. 

It allows us to write general code that works for *any* subclass. Instead of writing a bunch of `if/else` statements checking what type of animal we have, we just call the method. This is the secret sauce behind a massive OOP concept called **Polymorphism**, which we will cover heavily in this module!

### Override Rules

To successfully override a method, you must follow strict rules:

1.  **Signature Match:** The method name and parameter list in the child class **must exactly match** the method in the parent class.
2.  **Access Modifier Restrictions:** The child's method **cannot be more restrictive** than the parent's method. 
    *   *Example:* If the parent method is `public`, the child method must be `public`. If the parent is `protected`, the child can be `protected` or `public`, but *not* `private`.
3.  **Return Type:** The return type must be the **exact same** as the parent's method, OR it must be a **subtype** (this is called a *covariant return type*). 
    *   *Example:* If the parent returns an `Animal`, the child can return a `Dog` (since `Dog` is a subtype of `Animal`). *Note: If the return type is a primitive (like `int`), it must match exactly.*
4.  **Exceptions:** The child's method cannot throw *broader* checked exceptions than the parent's method. It can throw fewer, narrower, or no checked exceptions.

### The `@Override` Annotation

You should always place `@Override` directly above a method you intend to override. Why? Because it acts as a safety check. If you accidentally misspell the method name or get the parameters wrong, the compiler will throw an error, telling you that you aren't actually overriding anything. Without it, you might accidentally create a brand-new method instead of overriding the parent's method.

```java
public class Child extends Parent {
    
    @Override // Tells compiler: "Check that I am correctly overriding a parent method"
    public void doSomething() { 
        // Child's specific implementation
    }
}
```

## The `Object` Class: The Root of All Classes

Now let's talk about the ultimate parent. In Java, the `Object` class is the absolute root of the class hierarchy. 

**Implicit Inheritance:** Every single class you write in Java automatically inherits from `Object`, whether you type `extends Object` or not. If your class does not explicitly extend another class, Java makes it a direct child of `Object`.

Because of this implicit inheritance, every object in Java has access to the default methods defined in the `Object` class. Two of the most important methods you will interact with are:

*   **`public String toString()`**: Returns a string representation of the object. 
    *   *Default behavior:* Returns the class name and a memory reference (e.g., `Car@1b6d3586`).
    *   *Best Practice:* You should almost always **override** this method in your classes to return a readable string of your object's fields (e.g., `"Car: Ford Mustang"`). This is what gets printed when you pass an object to `System.out.println()`.
*   **`public boolean equals(Object obj)`**: Compares two objects for equality.
    *   *Default behavior:* Uses the `==` operator, which only checks if two references point to the exact same memory location (Reference Equality).
    *   *Best Practice:* You must **override** this method if you want to compare the actual *data* inside the objects (Logical Equality). For example, two different `Car` objects with the same model name and year should be considered "equal" by the `equals()` method, even if they are in different memory locations.

## Code Reuse and Hierarchy Modeling

Now that we know *how* to set up a parent-child relationship, let's talk about *why* we do it, and more importantly, *when* we should do it. 

### The Magic of Code Reuse
The biggest, most immediate benefit of inheritance is **Code Reuse**. As a programmer, you will quickly become familiar with the acronym **DRY**, which stands for *Don't Repeat Yourself*. 

Imagine you are writing a program for a dealership, and you need to create classes for `Car`, `Truck`, and `Motorcycle`. All three of these need to start an engine, check tire pressure, and track a VIN number. If you don't use inheritance, you have to copy and paste those exact same fields and methods into all three classes. If you find a bug in how the tire pressure is checked, you now have to go fix it in three different places! 

By creating a parent `Vehicle` class and putting that shared code there, you write it exactly *once*. The `Car`, `Truck`, and `Motorcycle` classes automatically inherit it for free. If you need to fix a bug or update the tire pressure logic, you only update it in the parent class, and the change ripples down to all the children. That is the power of code reuse.

### Modeling Hierarchies (The "Is-A" vs "Has-A" Rule)
Because code reuse is so convenient, it can be tempting to force a parent-child relationship just to avoid typing the same code twice. **Don't do this.** This is where *Hierarchy Modeling* comes in. You have to make sure the relationship actually makes logical sense in the real world.

The golden rule for modeling a hierarchy is the **"Is-A" test**. 
*   A `Car` **is a** `Vehicle`. (Makes sense! Inheritance is perfect here).
*   A `Truck` **is a** `Vehicle`. (Makes sense!).

But what if you have a `House` and a `Room`? 
*   Is a `Room` a `House`? No. 
*   Is a `House` a `Room`? No. 
*   Instead, a `House` **has** `Room`s. This is called a **"Has-A" relationship** (which we handle by just making a `Room` object a field inside the `House` class, rather than using inheritance). 

If you force a `Room` to inherit from a `House` just because they both share some code (like a `paintColor` property), your program's logic will quickly become a tangled, confusing mess. Always model your hierarchies based on real-world logic and the "Is-A" rule, not just to save a few keystrokes!
