# Lesson 5 - Collections

### Why Arrays Are Insufficient

Arrays are the first tool you learn for storing multiple values, but they come with several limitations that quickly become painful in real programs.

An array has a fixed size. Once it is created, it cannot grow or shrink. If you guess wrong about how many elements you need, you must create a new array and copy the old values over. Arrays also do not provide meaningful behavior. They store data, but they do not help you manage it.

Arrays work best when:
- The size is known and fixed
- The data is simple
- The structure will not change

Most object oriented programs do not meet those conditions. You often do not know how many objects you will have, and that number can change over time. Collections exist to solve these problems.

---

### The List Interface

A List represents an ordered collection of elements that can grow and shrink dynamically. Unlike arrays, lists provide behavior in addition to storage.

In Java, List is an interface. An interface defines what operations are available, not how they are implemented. This is important because it allows different implementations to be used interchangeably.

Common List behaviors include:
- Adding elements
- Removing elements
- Accessing elements by index
- Iterating over elements
- Asking for the size

You almost always write code against the List interface, not a specific implementation. This keeps your code flexible.

---

### ArrayList Basics

ArrayList is the most commonly used implementation of List. Internally, it uses a resizable array, but that detail is hidden from you.

Here is a basic example:

```java
import java.util.ArrayList;
import java.util.List;

List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
names.add("Charlie");
```

This code creates a list of strings and adds three elements to it.

You do not specify a size. The list grows as needed. You also do not use square brackets to access elements. Instead, you use methods:

```java
String first = names.get(0);
int count = names.size();
```

The ArrayList handles resizing, indexing, and bounds checking for you. This reduces errors and simplifies your code.

---

### Using Collections Of Objects, Not Primitives

Java collections cannot store primitive types directly. You cannot create a List<int> or List<double>.

Instead, you use wrapper classes:
- int becomes Integer
- double becomes Double
- boolean becomes Boolean

For example:

```java
List<Integer> scores = new ArrayList<>();
scores.add(90);
scores.add(85);
scores.add(100);
```

Java automatically converts between primitives and their wrapper types. This is called autoboxing and unboxing.

In practice, collections are most valuable when storing objects you define yourself. This reinforces object oriented thinking and design.

```java
List<Student> roster = new ArrayList<>();
roster.add(new Student("Alice", 12345));
roster.add(new Student("Bob", 67890));
```

This allows behavior and data to travel together, which is the core idea behind objects.

---

### Iteration Over Collections

Collections are designed to be iterated over in multiple ways. Each approach has different use cases.

Traditional for loop:

```java
for (int i = 0; i < names.size(); i++)
{
    System.out.println(names.get(i));
}
```

This style is useful when you need the index or need to modify elements by position.

Enhanced for loop:

```java
for (String name : names)
{
    System.out.println(name);
}
```

This is the most common iteration style. It is clean, readable, and safe. Use it when you only need to read elements.

Iterator:

```java
Iterator<String> it = names.iterator();

while (it.hasNext())
{
    String name = it.next();
    System.out.println(name);
}
```

Iterators are necessary when you need to remove elements during iteration. Removing elements directly from a list while looping with other methods can cause runtime errors.

---

### Introduction To Set

A Set represents a collection of unique elements. Unlike a List, a Set does not allow duplicates.

If you add the same value multiple times, it will only appear once.

```java
import java.util.HashSet;
import java.util.Set;

Set<String> emails = new HashSet<>();
emails.add("a@example.com");
emails.add("b@example.com");
emails.add("a@example.com");
```

The set still contains only two values.

Sets are useful when:
- Uniqueness matters
- Order does not matter
- You need fast membership checks

---

### Introduction To Map

A Map stores key value pairs. Instead of indexing by position, you index by a key.

```java
import java.util.HashMap;
import java.util.Map;

Map<Integer, Student> students = new HashMap<>();
students.put(12345, new Student("Alice", 12345));
students.put(67890, new Student("Bob", 67890));
```

You retrieve values by key:

```java
Student s = students.get(12345);
```

Maps are ideal when you need fast lookup based on an identifier. They are extremely common in real systems.

---

### When Collections Reduce Complexity

Collections exist to remove complexity, not add it. You should not reimplement features that collections already provide.

Avoid:
- Manually resizing arrays
- Writing custom search loops unnecessarily
- Creating parallel arrays to track related data

Use collections to express intent clearly. A List says order matters. A Set says uniqueness matters. A Map says lookup matters.

Good design often comes from choosing the right collection.

---

### Performance And Design Notes

ArrayList provides fast access by index but slower insertions in the middle. LinkedList provides faster insertions but slower access. In most cases, ArrayList is the correct default choice.

Sorting can be done easily:

```java
Collections.sort(names);
```

When returning collections from a class, be careful not to expose internal data structures directly. Returning a copy instead of the original collection protects your class from unintended modification.

Collections are foundational. They appear everywhere in Java, and understanding them well will dramatically improve your ability to design clean, flexible programs.

---