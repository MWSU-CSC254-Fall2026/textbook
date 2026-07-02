# Polymorphic Sorting in Java

In the last document, we looked at the theory of sorting and searching. We learned *why* speed matters and *how* algorithms scale. 

But we left off with a massive problem: those highly optimized, blazing-fast algorithms (like Merge Sort) were designed for simple numbers. How do we sort a list of custom `Student` objects? How does the computer know if Alice is "greater than" Bob? 

## The Problem with Naive Sorting

Let's say you have an array of integers. Sorting it is trivial:

```java
int[] numbers = {5, 2, 9, 1, 5};
Arrays.sort(numbers); // Works perfectly!
```

But what happens when you try to sort an `ArrayList` of custom objects?

```java
ArrayList<Student> roster = new ArrayList<>();
roster.add(new Student("Alice", 3.8));
roster.add(new Student("Bob", 3.2));

Collections.sort(roster); // 🛑 COMPILER ERROR!
```

Java throws an error. Why? Because the `sort()` method looks at your `Student` objects and asks: *"What does it mean for one Student to be 'less than' another? Do you want me to sort them alphabetically by name? By GPA? By ID number?"*

Java doesn't know your business logic. You have to tell it.

## Natural Ordering: The `Comparable<T>` Interface

If there is one obvious, default way to sort an object (like sorting Students by GPA, or Songs by Track Number), we use the `Comparable<T>` interface. 

By implementing this interface, your class signs a contract that says: *"I promise I know how to compare myself to another object of my type."*

To fulfill the contract, you must write exactly one method: `compareTo()`.

```java
public class Student implements Comparable<Student> {
    private String name;
    private double gpa;

    public Student(String name, double gpa) {
        this.name = name;
        this.gpa = gpa;
    }

    @Override
    public int compareTo(Student other) {
        // We want to sort by GPA. 
        // We use Double.compare() to safely compare decimal numbers.
        return Double.compare(this.gpa, other.gpa);
    }
}
```

### How `compareTo` works:
The method must return an integer:
*   **Negative number:** this object comes *before* the other object.
*   **Zero:** They are equal.
*   **Positive number:** this object comes *after* the other object.

Now that `Student` implements `Comparable`, the compiler is happy.

```java
ArrayList<Student> roster = new ArrayList<>();
roster.add(new Student("Bob", 3.2));
roster.add(new Student("Alice", 3.8));

Collections.sort(roster); // Works! Bob is now at index 0, Alice at index 1.
```

## Alternate Ordering: The `Comparator<T>` Interface & Lambdas

Here is the limitation of `Comparable`: You can only write *one* `compareTo` method per class. 

What if the user clicks a button in your app and wants to sort the students by **Name** instead of GPA? You can't rewrite the `Student` class on the fly, and you can't have two `compareTo` methods with the exact same signature. 

This is where `Comparator<T>` comes in. A `Comparator` is an *external* set of sorting rules. It allows you to define as many different ways to sort a class as you want, without ever touching the class itself.

In modern Java, we write Comparators using **Lambda expressions**. If lambdas look like alien syntax to you, don't worry. They are just a shortcut for passing a "mini-method" into another method.

Let's demystify this line of code:

```java
// Sort by Name (Alphabetical)
roster.sort((s1, s2) -> s1.getName().compareTo(s2.getName()));
```

### How this actually works:

1. **The Request:** `roster.sort(...)` is the sorting algorithm. It loops through your list, but every time it needs to know "Does Student A come before Student B?", it needs a rule. It is asking you to provide that rule.
2. **The Parameters `(s1, s2)`:** These represent the *two specific Student objects* the sorting algorithm is currently looking at. (You could name them `studentA` and `studentB`, but `s1` and `s2` is standard shorthand).
3. **The Arrow `->`:** This just means "takes these inputs and does this." 
4. **The Logic `s1.getName().compareTo(...)`:** This is the exact same `compareTo()` logic we used in the `Comparable` interface! It compares the two names and returns a negative number, zero, or a positive number.

The sorting algorithm is basically saying: *"Hey, I have two students right now. I'm going to hand them to your lambda as `s1` and `s2`. You run your code, and give me back a number telling me which one is smaller."* Because the lambda returns that negative/zero/positive number, the `sort()` algorithm knows whether to swap them or leave them alone.

### Writing your own Comparators

Because `Comparator` is external, you can write as many of these lambdas as you want:

```java
// Sort by Name (Alphabetical)
roster.sort((s1, s2) -> s1.getName().compareTo(s2.getName()));

// Sort by Name (Reverse Alphabetical)
// Notice we just flipped s1 and s2!
roster.sort((s1, s2) -> s2.getName().compareTo(s1.getName()));

// Sort by GPA (Highest to Lowest)
roster.sort((s1, s2) -> Double.compare(s2.getGpa(), s1.getGpa()));
```

### Summary: Internal vs. External

*   **`Comparable` is internal.** It defines the *natural, default* order of the object. You build it directly into the class. (Called via `Collections.sort(list)` or `list.sort(null)`).
*   **`Comparator` is external.** It defines a *custom, on-the-fly* order using a lambda. You leave the class alone and just pass in a custom rule. (Called via `list.sort(lambda)`).

## Tying it Back to Polymorphism

Take a step back and look at what you just did. You sorted a complex, custom-built object using a highly optimized $O(n \log n)$ algorithm provided by Java. 

**And you didn't write a single `for` loop.**

The engineers at Oracle who wrote the `Collections.sort()` method didn't know you were going to invent a `Student` class. They didn't need to. They just wrote the algorithm to accept *any* list of objects, as long as those objects implement the `Comparable` interface. 

*   **The Algorithm** handles the heavy lifting (the splitting, the merging, the Big-O complexity).
*   **Your Object** handles the business logic (the `compareTo` rules).

Because they are decoupled, you can use Java's world-class sorting algorithms on *anything* you build, instantly.
