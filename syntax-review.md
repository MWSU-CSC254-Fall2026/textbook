# Syntax Review

This document is intended as a quick review of some basic java syntax that you will need for this course. If you already know this, great, but if not please review it. 

## Loops

### Standard `for` Loop
For loops are driven by a numerical iterator.  They are best used when you need the counter, know the bounds, or must traverse in a specific order.
```java
for (int i = 0; i < data.length; i++) {
    // process data[i]
}
```
These loops have 3 major components:

- Initialization (first clause): Executes once before the first iteration.
- Condition (second clause): Checked before each iteration. Loop terminates when `false`.
- Update (third clause): Executes after the loop body completes.

What you see above is the most basic and most common kind of for loop, but they can be used in a variety of ways. Like so:

```java
for (int alpha = 25; alpha >= 0; alpha -= 5) {
  	// do thing
}
```

This is a loop that does....something, 5 times. You could write this much simpler if you wanted, but this is just an example of the flexibility of for loops.

### Enhanced `for` Loop (`for-each`)
The purpose of a for-each loop is in the name. Best when you want to do something `for-each` item in a collection. Its also great because you don't need to keep track of any indexes. The collection can be almost anything you can imagine would rationally work, and specifically its anything that implements `Iterable`, but we will get to what that means later.
```java
for (String item : collection) {
    // process item
}
```
### `while` & `do-while`
While (haha, get it?) most of you have probably seen a while loop and understand its uses, I would like to introduce the `do-while` loop. These are best for when you want to do something ***at least*** one time and possibly more depending on the condition. 
```java
while (condition) {
  // runs as long as the condition is true
}

do { 
	// runs at least once, and maybe more
} while (condition);
```

In other words, the body of the do runs once no matter what the condition is, and then repeats as long as the condition is true. It's not something I often find myself using, but it is something that exists.

## Methods

### Structure

Here is the structure of a java method. 

```java
[access] [static] returnType methodName(paramList) {
    // logic
    return value; // required if returnType != void
}
```
And here are a few different examples.
```java
public void doNothingUseful() {
  System.out.println("nothing useful");
}

private static int add(int x, int y) {
  return x + y;
}

public String doSomethingUseful(String firstName, String lastName) {
  String newString = firstName + " the `useful` " + lastName;
  return newString;
}
```

And here are a few details about method in general

- You might hear people talk about method or function **Signatures**. A signature is how the compiler identifies a method, and in java a method signature looks like this: `methodName(paramTypes)`
- Variables defined inside a function are inaccessible and invisible to the rest of the program. `newString` can't be addressed by the `add` method, for example. This idea as a whole is called **Scope**, as you might recall.
- `static` methods are methods that can be called without creating an instance, and can only call other `static` members. I understand that this might be nonsense to you right now, but we will come back to this.

## Parameters & Return Types

### Parameter Passing
When you pass a parameter to a function or method in java, it is either passed by value or by reference, depending on the type of the parameter. 

- 
- **Primitives (`int`, `double`, `char`, `boolean`):** are passed by value. The method receives a copy. Modifications inside the method do not affect the caller's variable.
- **Object References (`String`, arrays, custom objects):** are passed by *value of the reference*. Think of it like handing someone a copy of your house key. They can rearrange the furniture (mutate the object), but if they get a new key for a different house (`param = new Type()`), your original key still opens your original house.

### Return Flow

Remember these things about the `return` keyword:

- `return` immediately terminates execution and passes a value back to the caller.
- `void` methods omit a return value or use `return;` for early exit.
- The returned value's type must be assignment-compatible with the declared `returnType`.

### Method Overloading
Multiple methods can share the same name if their parameter lists differ in count, type, or order. Return type alone does not create an overload. This will be useful in the future.
```java
int process(int a, int b) { ... }
int process(int a, int b, int c) { ... } // Valid overload
// int process(int a, int b) { ... }     // Error: duplicate signature
```