# Lesson 2 - Classes and Encapsulation
### What Classes Really Are
A class is a blueprint that defines two things: the data the object stores and the behavior the object performs. The stored data is held in *fields*, which live inside each object instance. The behavior is expressed through *methods*, which operate on that data using the object’s internal state.

```java
public class Counter {

    private int value;

    public Counter(int startValue) {
        value = startValue;
    }

    public void increment() {
        value = value + 1;
    }

    public int getValue() {
        return value;
    }
}
```

This short example introduces the basic components of a class. The field *value* holds the internal state. The constructor initializes that state when the object is first created. The methods then use and update that state.  

Having a clear mental model of “data inside, behavior around it” prepares you for deeper concepts such as invariants and information hiding.

---

### Fields And Methods As Class Anatomy
A class’s structure is nearly always built around the same pattern:

- Fields store internal state.  
- Constructors establish an object’s initial valid state.  
- Methods provide controlled ways to observe or modify that state.  

```java
public class BankAccount {

    private double balance;

    public BankAccount(double initialAmount) {
        balance = initialAmount;
    }

    public void deposit(double amount) {
        balance = balance + amount;
    }

    public void withdraw(double amount) {
        balance = balance - amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

You should view methods as the “public surface” of the class. All legitimate interaction should flow through them. This way, you can protect the internal data from becoming inconsistent, while still giving users meaningful ways to work with the object.

---

### Access Control
Access modifiers define what outside code is allowed to touch. In Java, the most common modifiers are:

- *private* - only code inside the class may access the field or method.
- *public* - any code may access the method.
- *protected* and *package-private* exist, but are not needed yet.

Take these two simple classes as an example:

```java
public class Temperature {
  
    private double degreesF;

    public Temperature(double degreesF) {
      	this.degreesF = degreesF;
    }

    public double getFahrenheit() {
      	return degreesF;
    }
}

public class Count {
  
  	public int num;
  
  	public Count(int startingNum) {
    		this.num = startingNum;
  	}
}
```

In this example you can see two standard class definitions, Temperature and Count. Temperature has a private field degreesF while count has a public field num. Here are some things you can and cannot do with public and private fields.

```java
public class App 
{
    public static void main( String[] args )
    {
      	// I can initialize a new Count object with a starting value of 10
      	Count c1 = new Count(10);
      
      	// But because the number of field is public, I can both directly retrieve and 		
      	// directly set the value of num without working through any class methods.
      
      	System.out.println(c1.num); // This will print 10
      	c1.num = 12;
      	System.out.println(c1.num); // This will print 12
      
      	// Public fields are flexible and easy to work with directly, but are insecure and 
      	// provide little structure in terms of rules.
      
      	// On the flip side, you CANNOT do the same thing with Temperature
      	// Because it has private fields
      	Temperature temp = new Temperature(98.6);
      
      	System.out.println(temp.degreesF); 
      	// Trying to access a private field causes a compile-time error, which means the 
      	// program cannot even be built, preventing the faulty code from running at all.
      	
        // This will print 98.6; This is why we write getter methods
        System.out.println(temp.getFahrenheit()); 
      
      	// This is also invalid and wont compile
      	// We would have to write a setter to be able to change degreesF after construction
      	temp.degreesF = 100.2
    }
}
```

Public fields let outside code bypass your rules and can force the object into illegal or inconsistent states. They remove your ability to enforce invariants.

The purpose of *private* fields is to prevent outside code from setting them to invalid values. This design choice forces all interaction through the class’s own methods, which is the foundation of encapsulation.

---

### Invariants
An *invariant* is a rule that defines what valid objects look like. Once established, the invariant must hold true for the entire lifetime of the object. Breaking the invariant means the object is in an illegal state.

Examples of invariants:

- A rectangle’s width must never be negative.  
- A bank account’s balance must not dip below zero unless the class explicitly supports overdrafts.  
- A percentage must always be between 0 and 100.  

```java
public class Percentage {

    private int value;

    public Percentage(int value) {
        if (value < 0 || value > 100) {
            throw new IllegalArgumentException("Percentage must be between 0 and 100.");
        }
        this.value = value;
    }
  
  	public void setValue(int value) {
      if (value < 0 || value > 100) {
          throw new IllegalArgumentException("Percentage must be between 0 and 100.");
      }
    	this.value = value;
		}

    public int getValue() {
        return value;
    }
}
```

The constructor and setter enforce the invariant. Because the field is private, no outside code can force the value into an invalid state later.

### Defensive Design
Defensive design is the practice of preventing invalid objects from existing. This is made possible through encapsulation. If you restrict how fields are changed, you can enforce rules that keep the object meaningful and safe.

```java
public class StudentID {

    private String id;

    public StudentID(String id) {
        if (id == null || id.isEmpty()) {
            throw new IllegalArgumentException("ID cannot be empty.");
        }
        this.id = id;
    }

    public String getID() {
        return id;
    }
}
```

The class prevents the creation of nonsensical student IDs. Without encapsulation, any part of the program could overwrite the field and break the system.

---

### Information Hiding
Information hiding is the strategy of intentionally keeping details private so that users only interact with the conceptual features of the class. This separation allows you to change how the class works internally without breaking the rest of the program.

```java
public class Timer {

    private long startTime;

    public void start() {
        startTime = System.currentTimeMillis();
    }

    public long elapsedSeconds() {
        long now = System.currentTimeMillis();
        return (now - startTime) / 1000;
    }
}
```

The outside world has no idea how time is stored or computed. It only knows that a timer can be started and queried. The internal representation stays hidden.

---

### Why Default Setters Are Bad
A setter is a method whose only job is to update a single field. Setters often lead to invalid states because they allow piecemeal updates that ignore invariants. A setter like *setWidth* easily permits negative widths if unchecked.

```java
public class Rectangle {

    private int width;
    private int height;

    public Rectangle(int w, int h) {
        if (w < 0 || h < 0) {
            throw new IllegalArgumentException();
        }
        width = w;
        height = h;
    }

    public void setWidth(int w) {
        width = w;   // no checks here
    }
}
```

Setters also encourage the user to treat an object like a bag of variables instead of a coherent unit with rules. Meaningful methods that preserve invariants are nearly always better than raw setters.

---

### Meaningful Getters And Behaviors
A getter is acceptable when it supports a real use case. A getter becomes harmful when it exposes too much internal structure or encourages external code to manipulate the class’s data directly.

Instead of providing many raw getters, aim to provide behavior that accomplishes something meaningful.

```java
public class Range {

    private int low;
    private int high;

    public Range(int low, int high) {
        if (low > high) {
            throw new IllegalArgumentException();
        }
        this.low = low;
        this.high = high;
    }

    public boolean contains(int value) {
        return value >= low && value <= high;
    }
}
```

The class hides the specifics of its internal representation. Outside code does not need to know the exact low and high values to use the object correctly.

---

### Encapsulation As A Design Tool
Encapsulation is more than simply putting *private* on fields. It is a way of shaping your class so that the rest of the program is forced to use the class correctly. This design technique allows you to:

- Restrict what data can be changed.  
- Expose only useful, meaningful interactions.  
- Protect invariants and prevent invalid states.  
- Allow internal changes later without breaking public behavior.

Viewed this way, encapsulation becomes a design discipline rather than a keyword.

---

### Factory Methods
A factory method is a static method that creates and returns an object. It is useful when construction rules become complicated or when multiple named ways of creating an object improve clarity.

```java
public class RGB {

    private int r;
    private int g;
    private int b;

    private RGB(int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }

    public static RGB fromHex(String hex) {
        int value = Integer.parseInt(hex, 16);
        int r = (value >> 16) & 255;
        int g = (value >> 8) & 255;
        int b = value & 255;
        return new RGB(r, g, b);
    }
}
```

The constructor is private so only factory methods can create instances. This ensures all construction paths follow the rules defined by the class.

---

### Immutable Classes And Value Objects
An immutable class is a class whose instances cannot change after construction. All fields are private and final, and no setters exist.

```java
public class Point {

    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public Point movedBy(int dx, int dy) {
        return new Point(x + dx, y + dy);
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }
}
```

Immutable objects are safe to share because they cannot be changed unexpectedly. They also simplify reasoning about program behavior. Value objects like dates, points, colors, and percentages are excellent candidates for immutability.
