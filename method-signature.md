# Reading: Method Signatures & Scope

Welcome to Module 2! Before we start bundling data and behavior together into objects, we need to master the rules of engagement for our methods. Methods are the verbs of our programs, but they require a strict contract to work correctly. This contract is defined by the **method signature** and governed by **scope**.

## 1. The Method Signature: A Strict Contract

Think of a method signature as a vending machine interface. You need to know exactly what buttons to press (inputs) and what snack will drop (output). In Java, the signature dictates how a method is called and how it interacts with the rest of your code.

Anatomy of a method:
```java
public int calculateArea(int length, int width) {
    return length * width;
}
```
*   **Access Modifier:** (`public`) Who can see and call this method?
*   **Return Type:** (`int`) What type of data does this method hand back?
*   **Method Name:** (`calculateArea`) The identifier used to call it.
*   **Parameter List:** (`int length, int width`) The types and names of the inputs.

*Note: In Java, the formal "method signature" used by the compiler for things like method overloading consists strictly of the **method name** and the **parameter types**. However, when we talk about the "signature" conceptually, we usually mean the entire declaration line.*

## 2. Parameter Passing and Return Types

### Parameter Passing (Inputs)
When you call a method, you pass **arguments** into its **parameters**. Java uses *pass-by-value*. This means a copy of the value is passed into the method.
*   If you pass a primitive (like an `int`), the method gets a copy of the number. Changing it inside the method doesn't affect the original variable.
*   If you pass a reference (like an array or an object), the method gets a copy of the *reference* (the memory address). It can modify the contents of the object/array, but it cannot reassign the original reference to point to a completely new object.

### Return Types (Outputs)

The return type must exactly match the type declared in the signature.

*   If the signature says `int`, the method **must** use the `return` keyword to hand back an integer.
*   If the method doesn't need to return anything, the signature must use the `void` keyword. A `void` method can just use `return;` to exit early, or simply reach the closing brace `}`.

Here is a quick visualization of parameters vs arguments:

```java
// Here, x and y are parameters
private int add(int x, int y) {
  	int sum = x + y;
  	return sum;
}

// Here, 5 and 10 are arguments. You are giving arguments to the function, hence "passing arguments into parameters."
int newValue = add(5, 10);
```

And here is a snippit to demonstrate reference vs value in relation to functions:

```java
// Lets start with a couple functions to demonstrate
public int add(int x, int y) {
  return x + y;
}

public void removeFirst(ArrayList<int> list) {
  list.remove(0)
}

// Then lets use some values
int num = 10;
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(5);
numbers.add(15);
numbers.add(25);

// Then lets mutate them and see the results
add(num, 5);

System.out.println(num); // Here, num is still 10. The method made a copy of the variable, and so the original was never changed

removeFirst(numbers);

System.out.println(numbers.size()); // Here, however, numbers now only has two Integers stored within it. While the method did technically make a copy, that copy pointed to the same memory address, and thus the original was changed in some ways.
```

## 3. Scope: Where Do Variables Live?

Scope determines the visibility and lifetime of a variable. Understanding scope is critical to avoiding bugs where variables are unexpectedly `null` or out of bounds. We primarily deal with two types of scope in Java: **Local** and **Instance**.

### Local Scope
Local variables are declared *inside* a method (or a block of code like a `for` loop).
*   **Lifetime:** They are born when the execution reaches their declaration and die the moment the block finishes executing.
*   **Visibility:** They can only be seen and used within the block they are declared in.
*   **Initialization:** You *must* initialize local variables before using them, or the compiler will throw an error.

```java
public void doMath() {
    int localNumber = 10; // Born here
    System.out.println(localNumber);
} // localNumber dies here. It no longer exists in memory.
```

### Instance Scope (Fields)
Instance variables (often called **fields**) are declared *inside the class, but outside any method*.
*   **Lifetime:** They are born when an object is created (instantiated) and die when the object is destroyed (garbage collected). They persist across multiple method calls.
*   **Visibility:** They are visible to all methods within the class.
*   **Initialization:** They are automatically initialized to default values (e.g., `0` for numbers, `null` for objects) if you don't explicitly initialize them.

```java
public class Calculator {
    int instanceNumber = 5; // Born when a Calculator object is created

    public void add(int value) {
        instanceNumber += value; // Can be accessed and modified by any method
    }

    public void print() {
        System.out.println(instanceNumber); // Retains its value between calls
    }
} // instanceNumber dies when the Calculator object is garbage collected.
```

## Summary
*   **Signatures** define the contract: what goes in (parameters) and what comes out (return type).
*   **Pass-by-value** means methods get copies of variables, protecting your original data (mostly).
*   **Local scope** is temporary and tied to a method's execution.
*   **Instance scope** is persistent and tied to the lifespan of an object.

*Looking ahead: Notice how instance variables belong to an object, while local variables belong to a method call. In the next reading, we will dive deep into how to create these objects and use the `this` keyword to navigate between local and instance scope!*