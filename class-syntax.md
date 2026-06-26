# Class Syntax, Fields, and Object Instantiation

In Module 1, we relied heavily on parallel arrays to keep track of related data. If we had a list of student names, we had to ensure their IDs and GPAs were stored at the exact same index in separate arrays. As you likely noticed, this is fragile and clunky. 

It is time to stop treating data as scattered variables and start bundling it. Welcome to the world of Object-Oriented Programming (OOP).

## Classes: The Blueprint

In Java, a **class** is a blueprint or a template. It does not hold any actual data itself; rather, it describes what data an object *will* have and what behaviors it *will* be able to perform once it is created.

Think of a class like the architectural blueprint for a house. The blueprint itself isn't a house—you can't live in it. But it dictates where the doors go, how many rooms there are, and what the address will be. 

The syntax for a class is straightforward:

```java
public class ClassName {
    // Fields (data) go here
    
    // Constructors go here
    
    // Methods (behavior) go here
}
```

## Fields: Bundling the State

The variables declared inside a class, but outside of any method, are called **fields** (or instance variables). This is where we bundle our state. 

Let's create a blueprint for a `Student`. Instead of parallel arrays, we want a single entity that holds a student's name, ID, and GPA.

```java
public class Student {
    // Fields representing the state of a Student
    String name;
    int id;
    double gpa;
}
```

By placing these variables inside the `Student` class, we are declaring that *every* Student object created from this blueprint will have its own unique name, id, and gpa. They are bundled together in one place.

## Constructors: The Birth of an Object

Before we can use our blueprint, we need to actually build the house. In Java, we create objects using a special type of method called a **constructor**. 

A constructor's job is to initialize the object's fields when the object is first born. 
*   It **must** have the exact same name as the class.
*   It **does not** have a return type (not even `void`).

```java
public class Student {
    String name;
    int id;
    double gpa;

    // The Constructor
    public Student(String studentName, int studentId, double studentGpa) {
        name = studentName;
        id = studentId;
        gpa = studentGpa;
    }
}
```
*Notice how we named the parameters slightly differently (`studentName` instead of `name`) to avoid confusion with the fields. We will explore a cleaner way to write this using a special keyword in a later reading!*

## Object Instantiation: Building the House

**Instantiation** is the formal term for creating an object from a class blueprint. To instantiate an object, we use the `new` keyword followed by a call to the constructor.

When you use `new`, three things happen behind the scenes:
1.  Java allocates memory for the new object.
2.  The constructor runs to initialize the fields.
3.  A **reference** (the memory address) to this new object is returned.

Let's create our first `Student` objects in a `main` method:

```java
public class Main {
    public static void main(String[] args) {
        
        // Instantiating the first Student object
        Student student1 = new Student("Alice", 101, 3.8);
        
        // Instantiating the second Student object
        Student student2 = new Student("Bob", 102, 3.5);
        
    }
}
```

Notice how `student1` and `student2` are variables, but their type is `Student` (a custom reference type, not a primitive). They hold the *references* to the actual objects living in memory. Because they are separate objects, changing `student1.gpa` will not affect `student2.gpa`.

## Using the Dot Operator

Now that we have instantiated our objects, how do we interact with them? We use the **dot (`.`) operator**. The dot operator allows us to reach inside a specific object and access its fields or call its methods.

Let's add a simple method to our `Student` class to print out their information, and then call it using the dot operator.

```java
public class Student {
    String name;
    int id;
    double gpa;

    public Student(String studentName, int studentId, double studentGpa) {
        name = studentName;
        id = studentId;
        gpa = studentGpa;
    }

    // An instance method (behavior)
    public void printStudentInfo() {
        System.out.println("Name: " + name + " | ID: " + id + " | GPA: " + gpa);
    }
}
```

Now, back in our `Main` class:

```java
public class Main {
    public static void main(String[] args) {
        Student student1 = new Student("Alice", 101, 3.8);
        Student student2 = new Student("Bob", 102, 3.5);

        // Using the dot operator to call an instance method
        student1.printStudentInfo(); // Outputs: Name: Alice | ID: 101 | GPA: 3.8
        student2.printStudentInfo(); // Outputs: Name: Bob | ID: 102 | GPA: 3.5
        
        // We can also access fields directly using the dot operator
        student1.gpa = 3.9; 
        student1.printStudentInfo(); // Outputs: Name: Alice | ID: 101 | GPA: 3.9
    }
}
```

## Summary

*   **Classes** are the blueprints; they don't hold actual data until they are instantiated.
*   **Fields** are instance variables that hold the specific state (data) for an individual object.
*   **Constructors** are special methods that initialize the fields when an object is created.
*   **Instantiation** uses the `new` keyword to create an object in memory and returns a reference to it.
*   **The Dot Operator (`.`)** is how we access the fields and methods of a specific object instance.

*Looking ahead: Right now, our `Student` class is just a dumb bucket of data. In the next reading, we'll look at how to manipulate the text inside our objects using String methods, and soon after, we'll learn how to make our objects represent themselves cleanly using `toString()`!*