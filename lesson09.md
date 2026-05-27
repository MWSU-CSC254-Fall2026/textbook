# Lesson 9 - Generics

### Why Generics Exist

Generics exist to make programs safer, clearer, and easier to reason about. Before generics, Java collections stored objects as plain `Object`, which meant every retrieval required casting. This introduced two major problems:

- Errors appeared at runtime instead of compile time
- Code became cluttered with casts that obscured intent

Generics solve this by allowing you to express the *intended type* of data up front. The compiler can then enforce correct usage and prevent entire classes of errors.

At a conceptual level, generics answer this question:

“What *kind* of thing does this class or method work with?”

---

### Type Safety and Compile-Time Guarantees

Type safety means errors are caught as early as possible, ideally before the program ever runs. Generics move many mistakes from runtime failures to compile-time errors.

Without generics:

```java
ArrayList items = new ArrayList();
items.add("hello");
items.add(42);

String value = (String) items.get(1);
```

This code compiles but fails at runtime with a `ClassCastException`.

With generics:

```java
ArrayList<String> items = new ArrayList<>();
items.add("hello");
// items.add(42); // compile-time error

String value = items.get(0);
```

The compiler prevents invalid inserts and guarantees safe retrieval. The cast disappears entirely.

This is the primary value of generics: correctness enforced by the type system.

---

### Generic Classes

A generic class defines a type parameter that represents an unknown type chosen by the user of the class.

```java
public class Box<T>
{
    private T value;

    public void set(T value)
    {
        this.value = value;
    }

    public T get()
    {
        return value;
    }
}
```

`T` is a *type parameter*. It does not represent a specific class, only a placeholder.

Usage:

```java
Box<String> box = new Box<>();
box.set("apple");

String value = box.get();
```

The compiler substitutes `T` with `String` everywhere inside the class. No casting is required.

A generic class is written once but safely reused for many types.

---

### Generic Methods

Methods can be generic even if their enclosing class is not.

```java
public static <T> void print(T value)
{
    System.out.println(value);
}
```

The type parameter appears before the return type.

Usage:

```java
print("hello");
print(42);
print(3.14);
```

The compiler infers the type automatically in most cases. Generic methods are especially useful for utility logic that operates on many kinds of data.

---

### Type Parameters and Naming Conventions

Type parameters are commonly named using single uppercase letters by convention:

- `T` for type
- `E` for element
- `K` for key
- `V` for value
- `N` for number

These names are placeholders, not magic keywords. Their meaning comes entirely from how they are used.

Good naming and documentation are important because generic code tends to be abstract by nature.

---

### Bounded Generics

Sometimes a type parameter must satisfy a constraint so that certain methods or behaviors are guaranteed to exist. Bounded generics allow you to restrict what kinds of types are allowed.

A simple example uses numbers.

```java
public class NumberBox<T extends Number>
{
    private T value;

    public NumberBox(T value)
    {
        this.value = value;
    }

    public double toDouble()
    {
        return value.doubleValue();
    }
}
```

Here, `T` is restricted to `Number` or one of its subclasses, such as `Integer`, `Double`, or `Long`.

This bound is important because it guarantees that `doubleValue()` exists. Every subclass of `Number` defines this method.

Usage:

```java
NumberBox<Integer> intBox = new NumberBox<>(10);
NumberBox<Double> doubleBox = new NumberBox<>(3.5);
```

The following would be rejected at compile time:

```java
NumberBox<String> box = new NumberBox<>("hello"); // compile-time error
```

Without the `extends Number` bound, the compiler would not allow calls to `doubleValue()` because there would be no guarantee that `T` supports that operation.

---

### Upper and Lower Bounds

Bounds describe how a generic type relates to a class hierarchy. They answer different design questions depending on direction.

An upper bound restricts what a type parameter *can be*.

```java
public static <T extends Number> double sum(T a, T b)
{
    return a.doubleValue() + b.doubleValue();
}
```

This means `T` must be `Number` or a subclass. The method is allowed to call methods defined on `Number` because the bound guarantees they exist. An upper bound is primarily about *reading behavior*. You are saying that the value can safely be treated as at least a certain type.

A lower bound is about what a structure can safely *accept*.

Consider a method that adds integers into a list:

```java
public static void addValues(List<Number> list)
{
    list.add(10);
    list.add(20);
}
```

This method works for a `List<Number>`, but it would not accept a `List<Object>`, even though `Object` is more general.

Lower bounds exist to allow more general containers while still permitting insertion of specific types. Conceptually, they describe what kinds of values a structure is allowed to consume.

At this stage, the most important takeaway is intent rather than syntax:

- Upper bounds protect what you read
- Lower bounds protect what you write

The exact syntax becomes clearer once wildcards are introduced.

---

### Eliminating Casting in Collections

One of the most practical impacts of generics is their use in the Java Collections Framework.

```java
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");

for (String name : names)
{
    System.out.println(name);
}
```

No casts appear, and incorrect inserts are rejected at compile time.

This improves readability, reduces bugs, and communicates intent clearly to both the compiler and human readers.

---

### Wildcards in Practice

Wildcards represent unknown generic types.

```java
List<?> values;
```

This means “a list of something, but the exact type is unknown.”

Wildcards are commonly used when a method only needs to *read* data:

```java
public static void printAll(List<?> list)
{
    for (Object value : list)
    {
        System.out.println(value);
    }
}
```

When writing data, bounds become necessary to preserve type safety.

---

### Generic Interfaces

Interfaces can be generic in the same way as classes.

```java
public interface Repository<T>
{
    void save(T item);
    T load();
}
```

Implementations specify the concrete type:

```java
public class StringRepository implements Repository<String>
{
    public void save(String item) { }
    public String load() { return ""; }
}
```

This allows consistent APIs while supporting different data types.

---

### Type Erasure in Java

Java generics use *type erasure*. Generic type information exists only at compile time.

At runtime:

- `List<String>` and `List<Integer>` are both just `List`
- Type parameters are removed
- Casting is inserted by the compiler where needed

This explains why certain operations are illegal:

```java
if (obj instanceof List<String>) // not allowed
```

Understanding erasure helps explain error messages and design limitations encountered when working with generics.

---

### Writing Generic Data Structures

Generics allow you to write reusable data structures without sacrificing safety.

```java
public class Pair<A, B>
{
    private A first;
    private B second;

    public Pair(A first, B second)
    {
        this.first = first;
        this.second = second;
    }

    public A getFirst() { return first; }
    public B getSecond() { return second; }
}
```

This structure works for any combination of types and communicates intent clearly.

---

### When to Use Generics

Generics are appropriate when:

- A class or method operates on values conceptually, not on a specific type
- The same logic applies across many types
- Type safety matters more than convenience

They should be avoided when abstraction adds confusion without meaningful benefit.

Used well, generics express design intent directly in the type system and significantly reduce error-prone code patterns.

---