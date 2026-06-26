# Data Abstraction & Bundling Behavior

Assuming you are reading this in order (after completing the previous exercise), then congratulations! You've successfully solved the parallel arrays problem. By grouping your data into a `Student` class and using an `ArrayList<Student>`, you eliminated the nightmare of index tracking. 

But take a step back and look at your code now. While you fixed the data storage, your program still *feels* procedural. The logic is still scattered, and if you try to make your `Student` class do any actual work, you immediately hit a frustrating syntactic wall. 



## Scattered Logic: The "Dumb Data Bag" Problem

Look closely at your `Student.java` file from the last exercise. What is actually inside it? 
Right now, it probably just contains fields (variables) and a constructor. It is essentially a "dumb data bag." It holds data, but it doesn't *do* anything.

Now look at your `Main.java` file. Where is the logic that checks if a student's GPA is below 2.0? Where is the logic that formats and prints their academic standing? **It is still trapped inside `Main`.**

Even though we bundled the *data* into a `Student` object, we left the *behavior* scattered in the `Main` class. We are still treating the `Student` like a passive bucket of variables that external methods have to poke and prod. 

In true Object-Oriented Programming, an object shouldn't just hold data; it should be responsible for its own behavior. A `Student` object should know how to check its own standing and print its own info. This is the foundation of **Encapsulation**, which enables **Data Abstraction**—the idea that data should hide its internal complexities and only expose what it needs to expose. We will touch more on the "hiding" part when we discuss visibility in the following lessons.



## Class Functionality: Methods Belong to Objects

So, how do you move functionality inside of a class so that it doesn't have to be in `Main`? Well, for a start, classes can have methods of their own. The methods inside of classes look exactly like the standard methods you have been writing in `Main`, but they behave slightly differently regarding their parameters. 

Imagine you wanted to write a method to check the standing of a student. You might be tempted to write something like this:

```java
public class Student {
    String name;
    int id;
    double gpa;
    String major;
  
    public void checkStanding(double gpa) {
        if (gpa < 2.0) {
            System.out.println(name + " is on Academic Probation");
        } else {
            System.out.println(name + " is in Good Standing");
        }
    }
}
```

Look closely at that method signature: `public void checkStanding(double gpa)`. 

We actually don't need to pass `gpa` as a parameter here! Methods that belong to a class (called **instance methods**) have complete and total access to the fields of that class. This is important. You can just do this instead:

```java
public class Student {
    String name;
    int id;
    double gpa;
    String major;
  
    // Notice the lack of parameters
    public void checkStanding() {
        if (this.gpa < 2.0) {
            System.out.println(this.name + " is on Academic Probation");
        } else {
            System.out.println(this.name + " is in Good Standing");
        }
    }
}
```

Now that the logic lives inside the `Student` class, you can call it in the same way you would access class fields - the `dot` modifier:

```java
// Imagine we are inside Main

Student testStudent = new Student("Alice", 100, 1.9, "Computer Science");

// This will call the method and print to the console
// In this case it will print "Alice is on Academic Probation" because her gpa is < 2.0
testStudent.checkStanding();
```



## The `this` Keyword

Speaking of the example above, here is a cool thing you can do. You can directly and explicitly access a class property inside a class by using the keyword `this`. 

```java
public class Student {
    String name;
    int id;
    double gpa;
    String major;
  
    public void checkStanding() {
        // Check that out, isn't that cool?
        if (this.gpa < 2.0) {
            System.out.println(this.name + " is on Academic Probation");
        } else {
            System.out.println(this.name + " is in Good Standing");
        }
    }
}
```

Obviously, I am being a little sarcastic, and I am sure you are thinking, "Well, that's pointless—why would I ever do that if I can just type `gpa`?"

Well, besides just wanting to be explicit (which is sometimes very valuable to a programmer at their wits' end), there is a very common, practical spot to use the `this` keyword. Let's take a look at the `Student` constructor:

```java
public class Student {
    public int id;
    public String name;
    public double gpa;
    public String major;

    public Student(int studentId, String studentName, double studentGpa, String studentMajor) {
        id = studentId;
        name = studentName;
        gpa = studentGpa;
        major = studentMajor;
    }
}
```

There's nothing particularly wrong with this constructor, but it is slightly clunky. Having to name the parameters something *slightly* different from the properties of the class is annoying. The `this` keyword offers an elegant solution to this naming collision. 

```java
public class Student {
    public int id;
    public String name;
    public double gpa;
    public String major;

    public Student(int id, String name, double gpa, String major) {
        // The left side (this.id) refers to the class property
        // The right side (id) refers to the local parameter
        this.id = id;
        this.name = name;
        this.gpa = gpa;
        this.major = major;
    }
}
```

This is much cleaner and is standard practice in modern Java. `this.id = id` means "assign the value of the parameter called `id` to *this* object's property called `id`". If we don't use the keyword and attempt to do `id = id;`, Java gets confused and just assigns the parameter to itself, leaving the class property untouched. 



## Objects Answering Questions (Returning Data)

Before we wrap up, it's worth noting that instance methods don't *just* have to print things to the console. Because they have access to the object's state, they can perform calculations and **return** data back to `Main`. 

Instead of forcing the `Student` to print its own status, we can let the `Student` answer a question about itself:

```java
public boolean isOnProbation() {
    return this.gpa < 2.0;
}
```

Now, `Main` can simply ask the object:
```java
if (student1.isOnProbation()) {
    // do something
}
```
This is true Data Abstraction in action. `Main` doesn't need to know *how* the student calculates probation (maybe in the future it factors in attendance or major). `Main` just asks the question, and the `Student` object handles the internal logic.



## Summary & Looking Ahead

*   **Dumb Data Bags:** Classes shouldn't just hold data; they should be responsible for their own behavior.
*   **Instance Methods:** Methods inside a class automatically have access to that class's fields. You don't need to pass the object's own data into its methods as parameters.
*   **The `this` Keyword:** Used to explicitly refer to the current object. It is essential for resolving naming collisions between class fields and method/constructor parameters.
*   **Returning Data:** Objects can calculate and return information about themselves, allowing external code to interact with them cleanly without needing to know their internal math.

**Looking Ahead:** Right now, our fields are wide open. Anyone from the outside can write `student1.gpa = 5.0;` and break our logic. In the next lesson, we will discuss **Visibility and Encapsulation** (using the `private` keyword, getters, and setters) to truly protect our data. 
