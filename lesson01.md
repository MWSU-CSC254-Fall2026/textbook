# Lesson 1 - What Objects Are
### Objects as Entities with Identity, State, and Behavior
A Java program is built out of many pieces that interact. Some pieces are simple, like numbers and true/false values. Others are more complex, like a student in a course, a file on disk, or a moving character in a game. Objects exist to represent these more complex pieces in a structured way.

An **object** combines three ideas:

- Identity  
  The object exists as a distinct thing in the running program. Two different objects may store the same data but still be separate because they occupy different locations in memory.

- State  
  The information the object currently holds. In Java, this information is stored in fields, which are variables that belong to the object. For example, a *Person* object might have fields for a name and an age.

- Behavior  
  The actions the object can perform. In Java, behavior is expressed through methods, which are functions defined inside the class that operate on the object’s fields.

A simple example:

```java
// Class Definition
public class Person {
  
  	// Fields, sometimes called instance variables
    private String name;
    private int age;

  	// Methods
    public void haveBirthday() {
        age = age + 1;
    }

    public String getName() {
        return name;
    }
}
```

This *Person* class describes state (the fields *name* and *age*) and behavior (the methods *haveBirthday* and *getName*). Identity appears when specific *Person* objects are created while the program runs.

Even in this small example, grouping related data and behavior makes it easier to think about “a person” as one coherent unit instead of as separate, unrelated variables.

---

### Basic Class and Object Syntax in Java
Before going deeper into concepts, it helps to briefly review the basic syntax used to define a class and create objects in Java.

A minimal class definition:

```java
public class Point {
    private int x;
    private int y;

    public void move(int dx, int dy) {
        x = x + dx;
        y = y + dy;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }
}
```

Key parts:

- *public class Point*  
  Declares a new class named *Point*. The class is a blueprint for creating *Point* objects.

- *private int x; private int y;*  
  These are fields. They store the state for each individual *Point* object.

- Methods (*move*, *getX*, *getY*)  
  These define what a *Point* can do. Methods can change state (like *move*) or provide information (like *getX* and *getY*).

Creating and using objects:

```java
public class Example {
    public static void main(String[] args) {
        Point p = new Point();
        p.move(3, 4);
        int currentX = p.getX();
    }
}
```

Here, the *main* method creates a new *Point* object using *new Point()*, stores a reference to it in the variable *p*, calls a method on it, and reads one of its fields through a getter method.

This is the basic pattern: define a class, then create and use objects of that class.

---

### Reference Semantics: Variables as Handles to Objects
Java treats objects differently from simple values like *int* or *boolean*. For simple values, the variable directly holds the data. For objects, the variable holds a reference, which acts as a handle that directs Java to where the actual object lives.

Example:

```java
Person a = new Person();
Person b = a;
```

In this code:

- *a* holds a reference to a new *Person* object.
- *b = a;* copies the reference stored in *a* into *b*.
- After that line, *a* and *b* both refer to the same *Person* object.

No second object is created. The important detail is that the assignment *b = a;* copies the reference, not the underlying object. This behavior is known as reference semantics.

Consequences of reference semantics:

- Assigning one variable to another does not create a new object.
- If a method changes the object through one reference, the change is visible through all references that point to that object.
- Two variables can have different names and scopes but still share one object identity.

Understanding that variables store references, not the actual objects, is one of the key shifts when moving from simple values to object-oriented programming.

---

### Aliasing: When Two Variables Share the Same Object
Aliasing occurs when two or more references point to the same object. This is a direct result of reference semantics.

```java
Point p1 = new Point();
p1.move(2, 3);

Point p2 = p1;   // p2 now refers to the same Point as p1
p2.move(10, 10);
```

Because *p1* and *p2* refer to the same *Point*:

- After the first move, the point’s coordinates change by (2, 3).
- The second move call then changes the same object again by (10, 10).
- If code later checks *p1.getX()* and *p2.getX()*, both give the same result.

Aliasing is not automatically a problem. Sometimes, it is exactly what is needed, such as when multiple parts of a program must coordinate using the same shared object. However, aliasing can lead to confusing bugs when programmers expect separate copies but actually have one shared object.

Knowing that aliasing exists and being able to recognize when it might be happening is an important skill in understanding and debugging object-oriented code.

---

### Heap and Stack: A Simple Memory Mental Model
---

Java uses two main regions of memory that are useful to know at a high level:

- Stack  
  The stack keeps track of active methods and their local variables. It holds things like method parameters, local primitive values (such as *int* and *double*), and local references to objects. When a method finishes, its part of the stack is removed.

- Heap  
  The heap holds the actual objects created by the program. Whenever `new` is used to create an object, space is reserved for that object on the heap. The object stays there as long as it is still reachable through some reference.

A simplified picture:

```text
Stack                                 Heap
-----                                 -----------------------------------
p1  → (reference)  -----------------> Point object at some location
p2  → (reference)  -----------------> (same Point object as p1)
count → 5
```

In this diagram, the stack has two references, *p1* and *p2*, both pointing to one *Point* object on the heap. The integer *count* is stored directly on the stack because it is a simple value.

The goal of this model is not to teach actual memory addresses but to explain that:

- Objects live on the heap.
- References and simple values live on the stack.
- Objects outlive the methods that created them, as long as references to them still exist.

This mental picture makes it easier to understand object lifetimes and aliasing.

---

### Constructors and Object Initialization
When an object is created, a special method called a constructor runs to set up its initial state. A constructor has the same name as the class and does not specify a return type.

Example:

```java
public class Point {
    private int x;
    private int y;

  	// Constructor
    public Point(int startX, int startY) {
        x = startX;
        y = startY;
    }

    public void move(int dx, int dy) {
        x = x + dx;
        y = y + dy;
    }
}
```

This constructor ensures that every *Point* starts with a defined position:

```java
Point p = new Point(10, 20);   // x = 10, y = 20
```

Using constructors has several benefits:

- Objects cannot be accidentally created in a “half initialized” state.
- All setup logic for the object is kept in one place.
- It is easier to change the rules for valid construction later.

Multiple constructors can be defined if there are different ways to create a valid object, such as a default constructor with no parameters and another that requires specific starting values.

---

### Object State: Mutability and Immutability
Once an object is created and initialized, its state may or may not be allowed to change. This idea is captured by the terms mutable and immutable.

- Mutable object  
  An object whose fields can change after the object is created. For example, a *BankAccount* might allow deposits and withdrawals.

- Immutable object  
  An object whose fields never change after construction. All of its state is fixed when the constructor finishes, and later operations do not modify it.

Example of mutation:

```java
public class BankAccount {
    private int balance;

    public BankAccount(int startingBalance) {
        balance = startingBalance;
    }

    public void deposit(int amount) {
        balance = balance + amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

Here, calling *deposit* changes the internal state of the *BankAccount* object by updating the *balance* field.

Example of immutability using *String*:

```java
String s = "hi";
String t = s + " there";
```

In this code, *String* objects are immutable. The expression *s + " there"* creates a new *String* object. The variable *s* still refers to the original string, and *t* refers to the new one.

Immutability offers advantages:

- Immutable objects are safe to share between different parts of a program because they cannot be changed unexpectedly.
- They are easier to reason about because once created, their state is fixed.

Mutability is also necessary. Many real-world concepts naturally change over time, and modeling those requires mutable objects. Choosing between mutable and immutable designs is a design decision that affects how easy a program is to maintain.

---

### Shallow and Deep Copying of Objects
Copying an object is not as simple as copying an integer. Because references are involved, it matters whether you copy just the reference or the entire structure under it.

Two important ideas:

- Shallow copy  
  A new object or container is created, but it still refers to the same internal objects as the original.

- Deep copy  
  A completely separate structure is created, including all internal objects, so that changes to one copy do not affect the other.

Example with an array of *Point* objects:

```java
Point[] arr1 = new Point[1];
arr1[0] = new Point(1, 2);

// Shallow copy of the array
Point[] arr2 = arr1.clone();
```

Here:

- *arr2* is a new array object, so changing *arr2.length* or replacing elements affects only *arr2*.
- However, *arr1[0]* and *arr2[0]* still refer to the same *Point* object, so changes to that *Point* are seen through both arrays.

This is a shallow copy. A deep copy would require creating a new *Point* as well:

```java
Point[] arr3 = new Point[1];
arr3[0] = new Point(arr1[0].getX(), arr1[0].getY());
```

Now *arr3[0]* refers to an independent *Point*. Changing the point in *arr1* does not affect *arr3*.

Deep copying is more computationally expensive, but sometimes it is necessary when truly independent copies are required. Understanding the difference between shallow and deep copying helps avoid surprising shared changes.

---

### Object Lifecycle and Garbage Collection
Every object goes through a lifecycle while a program runs:

1. Allocation  
   Space on the heap is reserved for the object when *new* is used.

2. Initialization  
   The constructor runs and sets the fields to their initial values.

3. Use  
   Methods are called on the object, and its state may be read or updated.

4. Becoming unreachable  
   At some point, there may no longer be any references that point to the object. This can happen if variables go out of scope or are reassigned.

5. Garbage collection  
   When an object is unreachable, Java’s garbage collector eventually frees its memory. The exact moment is not under direct programmer control.

A simple illustration:

```java
public void createPerson() {
    Person temp = new Person();
    temp.haveBirthday();
}
```

Inside *createPerson*, the variable *temp* refers to a *Person* object while the method runs. When the method returns, *temp* goes away because it is a local variable on the stack. If no other reference to that *Person* exists, the object becomes unreachable and is eventually collected.

Setting a variable to *null* does not destroy the object. It only removes one reference. The object remains as long as any other reference still exists. Objects that are accidentally kept reachable for too long can cause memory to be used longer than necessary, even in a language with automatic garbage collection.

---

### Why Object Oriented Programming Exists
Object oriented programming is not just about learning new syntax. It was created to help manage the growing complexity of large programs.

Some benefits:

- Encapsulation  
  Each object controls access to its own data. This reduces unintended interactions between different parts of a program.

- Modularity  
  A program can be divided into classes that each handle a single concept. This makes it easier to understand and update parts of the code without needing to grasp the whole system at once.

- Real world modeling  
  Many problems involve interacting entities, such as users, accounts, orders, and messages. Objects map naturally to these entities.

- Reuse  
  Once a class is well designed, it can often be reused in multiple contexts, reducing duplicated work and making programs more consistent.

The core purpose of object orientation is to provide structure. It allows programmers to build complex systems from smaller pieces that can be understood, tested, and improved independently.

---

### Common Misconceptions to Avoid
Several misunderstandings frequently appear when students first start working with objects:

- “Objects live inside variables.”  
  In reality, variables store references, and objects live on the heap.

- “Assigning one variable to another copies the object.”  
  Assignment copies the reference, so both variables point to the same object.

- “Objects disappear when the method that created them ends.”  
  Objects remain as long as they are reachable through some reference, even if that reference lives in a different part of the program.

- “Setting a variable to null destroys the object.”  
  It only removes one reference. The object is collected only when no references remain.

- “Cloning an array or using simple copy methods always creates independent copies.”  
  Many operations create shallow copies that still share internal objects.

Recognizing and correcting these misconceptions early creates a solid foundation for later topics such as class relationships, inheritance, interfaces, and the use of collections.

---
