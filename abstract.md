# Abstract Classes & Methods

### The Pain Point
In the last exercise, I asked you to do something strange. You had to write a `calculatePay()` method in the `Employee` class that just returned `-1`. Then, in your payroll loop, you had to write an ugly `if/else` statement just to make sure you didn't add that fake `-1` to your total payroll. 

I asked you to do this both to demonstrate why we use abstract classes and methods and because that architectural pattern is how I used to code before I understood abstracts.It should have felt clunky, or  like a band-aid solution. This is called the "dummy code" problem. 

### The Problem
So, when you wrote the `Employee` class, it was well understood that it wasn't meant to be a true 'usable' class. I asked you to create two more classes that extend `Employee` which would hold the shared logic or template of the other two classes. But, as you've seen, the problem is that even these 'placeholder' classes need to have complete logic - you can't just define a method and leave it empty (actually sometimes you can, but you shouldn't). 

That meant you had to apply a less than desirable solution involving checking the returns and using an `if/else` structure. It should be clear that while this isn't *bad* necessarily, it also isn't a good solution.

I mean, think about it: does a "generic employee" have a specific pay calculation? No, of course not. Only Managers (salary + bonus) and Interns (hourly rate * hours) have real pay calculations. By forcing a generic `Employee` to have a `calculatePay()` method, Java was forcing us to lie to the compiler.



## The `Abstract` Keyword

It should be no surprise that Java has a built-in cure for this exact situation. It's called the `abstract` keyword.

When we know that a parent class has a method that *must* exist for all subclasses, but the parent class itself *cannot possibly provide the implementation*, we make that method **abstract**. And when a class has even one abstract method, the entire class must be declared **abstract**.

Let's look at how we fix the `Employee` class:

```java
public abstract class Employee {
    private String name;

    public Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    // THE FIX: No more dummy "-1" return!
    // We just declare the method signature and end it with a semicolon.
    public abstract double calculatePay();
}
```

Notice two massive changes here:
1. The class itself is prefixed with `abstract`.
2. The `calculatePay()` method has **no body** (no curly braces, no `return -1;`). It just ends with a semicolon. We are telling Java: *"Trust me, the subclasses will figure out the math."*

### Make The Compiler Do The Work
When you introduce `abstract` into your code, the Java compiler steps in and enforces two strict, unbreakable rules. This is where the magic happens.

**Rule #1: You can no longer instantiate an abstract class.**
Remember in the last exercise, we added a `new Employee("Generic Temp")` to our list just to see the problem? If you try to do that now, your code **will not compile**.
```java
// ERROR! Cannot instantiate the abstract type Employee
Employee temp = new Employee("Generic Temp"); 
```
*Why is this awesome?* Because it completely eliminates the "Dummy Data" problem at the source. You can't accidentally (or intentionally) put a fake employee in your payroll list anymore!

**Rule #2: Subclasses MUST implement all abstract methods.**
If `Manager` extends `Employee`, it inherits that abstract `calculatePay()` method. Because it's abstract, the compiler says: *"I won't let you use a Manager object unless you provide the real math for `calculatePay()`."*

If you forget to `@Override` and write the actual math in `Manager`, your code **will not compile**.
Why is this useful? The compiler is now acting as your bodyguard. It guarantees that every single object in your `ArrayList<Employee>` has a real, working `calculatePay()` method.

### Clean Up the Loop
Because the compiler now guarantees that *every* object in our `ArrayList<Employee>` is a concrete subclass (like a `Manager` or `Intern`) with a real `calculatePay()` method, look at how clean our `Main` loop becomes:

```java
double totalPayroll = 0;

for (Employee e : staff) {
    // No more if/else! No more checking for -1!
    // The compiler guarantees e.calculatePay() will return real money.
    double pay = e.calculatePay();
    totalPayroll += pay;
    System.out.println("Paying " + e.getName() + ": $" + pay);
}
```

No band-aids. No dummy data. Just clean, safe, Object-Oriented code.

### Summary: The Rules of `abstract`
*   An `abstract` method has no body (no `{ }`). It is just a promise that subclasses will do the work.
*   If a class contains even one `abstract` method, the whole class must be declared `abstract`.
*   You **cannot** use the `new` keyword to create an object of an `abstract` class.
*   Any concrete subclass that extends an `abstract` class **must** provide a body for (override) all of the parent's abstract methods. (If it doesn't, the subclass must also be declared `abstract`).
