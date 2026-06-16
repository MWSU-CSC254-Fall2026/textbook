# Collections & Data Alignment

This document introduces `ArrayList<T>` and the core idea behind it: grouping related data so *you* don't have to manage the alignment yourself. 

## The Problem We're Solving

Take a look at a primitive solution to a problem you might have come across:

```java
String[] names = new String[5];
int[] ids = new int[5];
double[] grades = new double[5];

// To add a student, you have to update ALL THREE arrays:
names[0] = "Alice";
ids[0] = 101;
grades[0] = 94.5;

// And if you sort grades? You better manually swap names and ids too...
```

It works. But it's fragile. One index gets out of sync, and your whole program is quietly wrong. That's the parallel array trap.

The fix isn't fancier loops or better index checking. It's a different mental model: **group the data that belongs together**.

## Enter `ArrayList<T>`

An `ArrayList` is a resizable container that holds objects. Think of it like a dynamic array that grows for you, with built-in methods for common tasks.

```java
// Import at the top of your file
import java.util.ArrayList;

// Create an ArrayList of Strings
ArrayList<String> names = new ArrayList<>();

// Add items (no index tracking needed)
names.add("Alice");
names.add("Bob");
names.add("Charlie");

// Access items
String first = names.get(0); // "Alice"

// Check size
int count = names.size(); // 3

// Remove an item
names.remove(1); // Removes "Bob", shifts the rest automatically
```

A few quick notes:
- `ArrayList` lives in `java.util`, so you need that `import` statement.
- The `<String>` part is called a *generic type*. It tells the ArrayList: "Only hold Strings." We'll unpack generics more later. For now, just match the type you want.
- `add()`, `get()`, `remove()`, `size()` are methods. Yes, they have parentheses. No, `length` is not a method on ArrayList—use `size()`.

## Grouping Related Data

Here's the key insight: instead of three separate arrays, what if we used three `ArrayList`s that we *always* update together?

```java
ArrayList<String> names = new ArrayList<>();
ArrayList<Integer> ids = new ArrayList<>(); // Note: Integer, not int
ArrayList<Double> grades = new ArrayList<>(); // Note: Double, not double

// Add a student: one call per list, but the intent is clearer
names.add("Alice");
ids.add(101);
grades.add(94.5);

// Access: same index across all three
String studentName = names.get(0);
int studentId = ids.get(0);
double studentGrade = grades.get(0);
```

Better? Yes. Perfect? Not yet.

Why? Because we're *still* managing index alignment manually. We just moved the friction, not eliminated it. But this is a stepping stone. In Module 2, we'll replace these three lists with a single `ArrayList<Student>` where `Student` is a class that holds name, id, and grade together. That's the real win.

For now, just notice: `ArrayList` gives us flexibility (dynamic sizing, built-in methods) while we practice the *idea* of grouping data.

## Wrapper Classes: A Quick Reminder

You might have noticed: `ArrayList<Integer>`, not `ArrayList<int>`. That's because generics in Java only work with *objects*, not primitives.

| Primitive | Wrapper Class |
| --------- | ------------- |
| `int`     | `Integer`     |
| `double`  | `Double`      |
| `boolean` | `Boolean`     |
| `char`    | `Character`   |

Java usually handles the conversion automatically (called *autoboxing*), so you can write:
```java
ArrayList<Integer> scores = new ArrayList<>();
scores.add(95); // Java quietly converts int → Integer
int first = scores.get(0); // And back again
```

But if you see a weird error about "int cannot be converted to Integer", now you know why.

## Common `ArrayList` Methods (Cheat Sheet)

| Method                | What It Does                  | Example                               |
| --------------------- | ----------------------------- | ------------------------------------- |
| `add(T t)`            | Appends to end                | `list.add("item")`                    |
| `add(int index, T t)` | Inserts at position           | `list.add(0, "first")`                |
| `get(int index)`      | Returns element at index      | `String x = list.get(2)`              |
| `set(int index, T t)` | Replaces element at index     | `list.set(1, "new")`                  |
| `remove(int index)`   | Removes element, shifts rest  | `list.remove(0)`                      |
| `size()`              | Returns number of elements    | `int n = list.size()`                 |
| `isEmpty()`           | Checks if list has 0 elements | `if (list.isEmpty()) { ... }`         |
| `contains(Object o)`  | Checks if element exists      | `if (list.contains("Alice")) { ... }` |

You don't need to memorize these. Just know they exist, and you can look them up when you need them.

## Arrays vs. `ArrayList`: When to Use Which

| Use an Array When...                                    | Use an `ArrayList` When...                              |
| ------------------------------------------------------- | ------------------------------------------------------- |
| You know the size won't change                          | You need to add/remove items dynamically                |
| You're working with primitives for performance          | You want built-in methods (`add`, `remove`, `contains`) |
| You're doing low-level operations (rare in this course) | You want cleaner, more readable code                    |

Rule of thumb for Module 1: if you're tempted to manually resize or shift an array, switch to `ArrayList`.

## Check Your Understanding

```java
ArrayList<String> tasks = new ArrayList<>();
tasks.add("Review arrays");
tasks.add("Try ArrayList");
tasks.add("Build something");

// What does this print?
System.out.println(tasks.get(1));

// What happens if we run this?
tasks.remove(0);
System.out.println(tasks.size());
System.out.println(tasks.get(0));
```

<details>
  <summary>Click here to reveal the answers</summary>
  `tasks.get(1)` prints: `"Try ArrayList"` (index 1 is the second item) <br> <br>
  After `tasks.remove(0)`: <br>
  `tasks.size()` is `2` (one item removed) <br>
  `tasks.get(0)` is now `"Try ArrayList"` (items shift left automatically) <br>
</details>

If you got those right, nice. If not, no worries—paste the code into your IDE and watch it run. That's how it clicks.

## One Last Thing

`ArrayList` isn't magic. Under the hood, it still uses an array. The difference is: it handles resizing, shifting, and bounds checking *for you*. That frees you to focus on *what* the data means, not *where* it lives.

We're not done with this idea. In the next module, we'll take the next step: replacing multiple parallel `ArrayList`s with a single list of custom objects. But for now, just practice: when you feel that index-tracking friction, ask: "Could grouping this data help?"

If any of this feels fuzzy, that's okay. The assignment will help it land. And if you get stuck, come back to this doc—or ask. That's what I'm here for.