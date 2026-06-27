# Access Modifiers

In the last reading, we solved the "Dumb Data Bag" problem by moving our logic into instance methods. Now, our `Student` object can check its own standing and answer questions about itself. 

But there is a massive, hidden trap in our current design. Take a look at how we interact with our `Student` object from the `Main` class:

```java
Student alice = new Student("Alice", 101, 3.8, "Computer Science");

// Main can directly reach in and change the data
alice.gpa = -5.0; 
alice.name = null;
```

Right now, our fields are wide open to the world. Any code, anywhere, can reach into the `Student` object and change its data to absolute garbage. If `alice.gpa` becomes `-5.0`, what happens when we call `alice.checkStanding()`? The logic breaks. The object has lost control of its own state. 

In Object-Oriented Programming, an object must be the sole guardian of its data. To achieve this, we use **Access Modifiers**.

## Access Modifiers

Access modifiers are keywords you attach to your classes, fields, and methods to dictate **who** is allowed to see and interact with them. Java has three primary access modifiers. Let's look at them from most restrictive to least restrictive:

### `private`

When you mark a field or method as `private`, it becomes completely invisible to the outside world. **Only the class itself can see and interact with it.** 

*   *Rule of thumb:* Almost all instance variables (fields) should be `private`. The object should guard its own data.

### `public`

When you mark something as `public`, it is accessible to every other class in your entire program. 

*   *Rule of thumb:* Constructors and instance methods (the behaviors we *want* the outside world to use) should usually be `public`.

### `protected`

`protected` is the middle child. It allows access within the same class, within the same "package" (folder), and—most importantly—by **subclasses** (child classes). 

*   *Rule of thumb:* You won't use this much right now, but it is crucial for **Inheritance**, which we will cover in a future module.

## Locking Down the Fields

Let's apply these modifiers to our `Student` class. We want to lock down the data (`private`) but leave the behavior open (`public`).

```java
public class Student {
    // FIELDS: Private. The outside world cannot touch these directly.
    private int id;
    private String name;
    private double gpa;
    private String major;

    // CONSTRUCTOR: Public. The outside world needs to be able to create Students.
    public Student(int id, String name, double gpa, String major) {
        this.id = id;
        this.name = name;
        this.gpa = gpa;
        this.major = major;
    }

    // METHODS: Public. The outside world can ask the object to do things.
    public void checkStanding() {
        // Because this method is INSIDE the Student class, 
        // it is perfectly allowed to access the private 'gpa' field!
        if (this.gpa < 2.0) {
            System.out.println(this.name + " is on Academic Probation");
        } else {
            System.out.println(this.name + " is in Good Standing");
        }
    }
}
```

Notice what happened here. By making the fields `private`, we broke the outside world's ability to mess with them:

```java
public class Main {
    public static void main(String[] args) {
        Student alice = new Student("Alice", 101, 3.8, "CS");
        
        alice.checkStanding(); // THIS WORKS! The method is public.
        
        alice.gpa = -5.0;      // COMPILER ERROR! 'gpa' is private. Main cannot see it.
    }
}
```

We have successfully protected our data. But now we have a new problem: if `Main` can't see the `gpa`, how do we print it to the screen? How do we update it if the student takes a new class?

## The Bridge: Getters and Setters

If we lock the door, we need to provide a window. In Java, we provide controlled access to `private` fields using **Getters** (accessors) and **Setters** (mutators).

### Getters (Reading Data)

A getter is simply a `public` method that returns the value of a `private` field. It allows the outside world to *look* at the data without being able to *change* it.

```java
// Inside the Student class
public double getGpa() {
    return this.gpa;
}

public String getName() {
    return this.name;
}
```

Now, `Main` can safely read the data: `System.out.println(alice.getGpa());`

### Setters (Writing Data)

A setter is a `public` method that allows the outside world to update a `private` field. But here is the superpower of the setter: **We can add validation logic.**

Remember how we wanted to prevent `alice.gpa = -5.0`? We can do that in a setter!

```java
// Inside the Student class
public void setGpa(double newGpa) {
    // Validate the data before accepting it!
    if (newGpa >= 0.0 && newGpa <= 4.0) {
        this.gpa = newGpa;
    } else {
        System.out.println("Error: Invalid GPA. Must be between 0.0 and 4.0.");
    }
}
```

Now, if `Main` tries to do `alice.setGpa(-5.0);`, the object rejects it. The object remains perfectly valid, and its internal logic is protected. 

## Protect From What?

Okay, so if you are anything like me when I was first learning to program, you might have heard this document discuss 'protecting' your code and immediately think, "Protect it from what? From me? No one else is using my code...."

That's not a bad point for the moment. Currently, you aren't sharing your code, or a workspace, or working collaboratively. If you feel like protecting your class data is overkill right now, I wouldn't blame you. For right now, we are mostly doing it to instill good habits. However, there is another reason you might not immediately pick up on:

Knowing access modifiers *informs* ***you*** on how to use code you might be working on. Imagine you are given a class that's a little bit dense and complicated:

```java
public class BankAccount {
    private final String accountNumber;
    private final String ownerName;

    private double balance;
    private boolean frozen;

    private final List<String> transactionLog;

    public BankAccount(String accountNumber, String ownerName, double startingBalance) {
        if (startingBalance < 0) {
            throw new IllegalArgumentException("Starting balance cannot be negative.");
        }

        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = startingBalance;
        this.frozen = false;
        this.transactionLog = new ArrayList<>();

        log("Account opened with balance: $" + startingBalance);
    }

    public String getAccountNumber() { return accountNumber; }
    public String getOwnerName() { return ownerName; }
    public double getBalance() { return balance; }
    public boolean isFrozen() { return frozen; }

  	// These methods aren't finished, but imagine they do something complicated.
  	// (I didn't want to write the complicated parts)
    public void deposit(double amount) { /* ... */ }
    public boolean withdraw(double amount) { /* ... */ }
    public void freeze() { /* ... */ }
    public void unfreeze() { /* ... */ }
    
    public List<String> getTransactionLog() {
        return Collections.unmodifiableList(transactionLog);
    }

    private void requireActiveAccount() { /* ... */ }
    private void log(String message) { /* ... */ }
}
```

Notice how the fields are protected? Do you notice how some of the fields don't have standard setters? That means you aren't supposed to try and set those fields manually. The access modifiers are *telling* you that the class handles that kind of work. (Assuming that the class does its job properly). 

So yes, while access modifiers might feel a little overkill for your current situation, it is never too early to learn to use them properly—they reveal much more information about a class's design than you would assume on first glance.

## Summary

*   **The Wild West Problem:** If fields are left open, external code can corrupt an object's state (e.g., setting a GPA to a negative number).
*   **`private`**: Restricts access to *only* the inside of the class. Used to protect instance variables (fields).
*   **`public`**: Allows access from anywhere. Used for constructors and instance methods so the outside world can interact with the object.
*   **`protected`**: Allows access within the same package and by subclasses. Used primarily for Inheritance.
*   **Getters and Setters**: `public` methods that act as controlled gateways to read and write `private` fields. Setters are especially powerful because they allow you to validate data before it enters the object.
*   **Communication:** Access modifiers don't just protect data; they communicate the intended API of a class to other developers, showing them exactly how the object is meant to be used.
