# Linked Lists & Dynamic Memory

In the previous documents, we looked at how to sort and search through data. But we always assumed we were using an Array or an `ArrayList`. 

There is another fundamental way to store a list of data in computer science: the **Linked List**. 

We aren't going to write one from scratch in this course, but you need to understand what they are, how they use memory, and when to use them.

## The Array Bottleneck

Let's look at the hidden cost of the `ArrayList`. 

Because an `ArrayList` is backed by a standard array, its items must be stored in **contiguous memory** (right next to each other in the computer's RAM). 

This makes reading data incredibly fast. If you want `list.get(500)`, the computer does some quick math and jumps exactly to that spot in memory. 

But what happens if you want to insert a new item at index `0`?
1. The computer has to move the current item at index 0 to index 1.
2. It has to move the item at index 1 to index 2.
3. It has to do this for *every single item* in the list to make room.

If you have 1,000,000 items in your list, inserting one item at the beginning requires 1,000,000 shifts. That is an **$O(n)$** operation. It's slow.

## The Linked List Solution

What if the data *didn't* have to be stored right next to each other in memory? 

Imagine a scavenger hunt. You don't know where the next clue is, but the current clue has a note that says: *"The next clue is hidden under the blue chair."*

This is a **Linked List**. Instead of one giant array, a Linked List is a chain of individual objects called **Nodes**. 

Every `Node` has two jobs:
1. Hold your data (e.g., a `Student` object).
2. Hold a *reference* (a pointer) to the next `Node` in the chain.

```java
// Conceptual idea of a Node
class Node {
    Student data;
    Node next; // The "scavenger hunt clue" to the next node
}
```

If you want to insert a new item at the beginning of a Linked List, you don't shift anything. You just create a new `Node`, point its `next` arrow to the old first `Node`, and update your starting point. **Done.** It takes one step. It is an **$O(1)$** operation.

## Dynamic Memory Allocation

This brings up a core computer science concept: **Dynamic Memory Allocation**.

In an array, you have to declare how much memory you need up front (or rely on `ArrayList` to secretly copy everything to a bigger array when it gets full). 

With a Linked List, memory is allocated *on the fly*. Every time we add a new item, we use the `new` keyword:

```java
Node newNode = new Node(myStudent);
```

When you type `new` in Java, the JVM (Java Virtual Machine) goes to the computer's RAM, finds a free chunk of memory exactly big enough to hold that `Node`, and claims it. This is **dynamic memory allocation**—getting memory at runtime exactly when you need it.

In older languages like C or C++, programmers had to manually request this memory and manually delete it when they were done, or the computer would run out of RAM (a "memory leak"). 

Java saves you from this. Java has a background process called the **Garbage Collector**. If your Linked List deletes a `Node`, or if your program finishes running, Java's Garbage Collector automatically notices that the `Node` is no longer being used, and quietly reclaims that memory for the system. 

## The Trade-Off

Linked Lists sound amazing. Inserting is $O(1)$! No shifting! Why don't we just use them for everything and throw `ArrayList` in the trash? 

Because of the scavenger hunt.

Remember how fast `ArrayList.get(500)` was? The computer just did math and jumped there. You **cannot** do that with a Linked List. The nodes are scattered randomly across the computer's RAM. If you want the 500th item, the computer *must* start at the first node, read its `next` clue, go to the second node, read its `next` clue, and physically follow the chain 500 times. 

| Action                  | `ArrayList` (Array)                | `LinkedList` (Nodes)               |
| :---------------------- | :--------------------------------- | :--------------------------------- |
| **Get item at index**   | **$O(1)$** (Instant math)          | **$O(n)$** (Must follow the chain) |
| **Insert at beginning** | **$O(n)$** (Must shift everything) | **$O(1)$** (Just change a pointer) |
| **Search for an item**  | **$O(n)$** (Must check one by one) | **$O(n)$** (Must check one by one) |

## The Real-World Reality Check

Here is the most important thing to learn about Linked Lists in Java:

**You will almost never write one from scratch.**

In the real world, if you need a Linked List, you just import Java's built-in one:
```java
import java.util.LinkedList;

LinkedList<Student> roster = new LinkedList<>();
```
It already has all the `add()`, `remove()`, and `get()` methods written for you. 

Most Java developers rarely even use `java.util.LinkedList`. Because modern computer processors are so heavily optimized for reading contiguous blocks of memory (Arrays), an `ArrayList` is actually *faster* than a `LinkedList` in 95% of real-world scenarios, even with the shifting penalty. 

Unless you are building a highly specialized system where you are constantly inserting millions of items at the very front of a massive list, just stick to `ArrayList`.

## One Last Thing

Data structures are all about trade-offs. There is no "perfect" way to store data. Arrays are fast to read, but slow to modify. Linked Lists are fast to modify, but slow to read. 

As an Object-Oriented programmer, your job isn't to memorize the code to build these structures. Your job is to understand their **behavior** (their Big-O costs) so you can choose the right tool for the job, and then design your custom objects to live inside them efficiently.