# Interfaces: Programming to a Contract

In the last exercise, we used abstract classes and polymorphism to completely clean up our game loop. By pushing the `update()` and `render()` logic down into the subclasses, our main loop became beautifully simple. 

But as your game world grows, you are going to run into a new architectural wall. Abstract classes are great for vertical hierarchies (a `Player` *is an* `Entity`), but they fall apart when you need to share behavior *across* different branches of your family tree.

### The Problem: Cross-Cutting Behavior

Let’s say you want to add a combat system to your game. You want to write a method that applies damage to game objects. 

Naturally, both `Player` and `Enemy` need a `takeDamage(int amount)` method. But what about your `NPC` (Non-Player Character) classes? Maybe your villagers are immortal and shouldn't take damage at all. What about a `BreakableBarrel`? It’s not an enemy, and it’s not a player, but if you shoot it, it should take damage and explode.

If we try to model this using only the class inheritance we've learned so far, we immediately hit a massive roadblock. 

Where do we put `takeDamage()`? 
*   If we put it in the parent `Entity` class, then our immortal `NPC`s inherit it. We are back to the "dummy code" problem from Module 4, where we have to write fake methods just to satisfy the compiler.
*   If we put it in an `Enemy` class, then `Player` can't use it (because `Player` extends `Entity`, not `Enemy`). 
*   What if we create a `DamageableEntity` class? Great! But wait... Java only allows **single inheritance**. A class can only `extends` *one* parent. If `Player` extends `Entity`, it cannot also extend `DamageableEntity`. 

### The Single Inheritance Wall

This is the fundamental limitation of relying solely on class hierarchies. Inheritance hierarchies get deep, awkward, and rigid. 

Imagine you want to add flying behavior. You have a `FlyingEnemy` and a `FlyingPlayer`. If behavior is tied to *what something is* (its class identity), you end up duplicating your "flying" logic in two completely different places, or you create bizarre, deeply nested classes just to share a single trait.

We are trying to tie behavior to an object's *identity* (its place in the family tree), when we really just care about *what it can do* (its capabilities). 

### Interfaces

Java has a built-in solution for this exact problem. It’s called an **interface**.

If a class is an *identity* ("I am a Player"), an interface is a *role* or a *contract* ("I can take damage"). 

An interface looks a lot like an abstract class, but with a crucial difference: it contains **zero implementation**. It is purely a list of method signatures. It is a contract that says: *"If you sign this contract, you promise to provide the code for these specific methods."*

Let's define a contract for things that can take damage:

```java
// We use the 'interface' keyword instead of 'class'
public interface Damageable {
    // No curly braces, no code! Just the signature.
    // This is the contract.
    void takeDamage(int amount);
    
    // We can also include getters if the system needs to know health
    int getCurrentHealth(); 
}
```

Now, any class in our game—regardless of what it extends—can "sign" this contract using the `implements` keyword. 

```java
// A Player is an Entity, but it also signs the Damageable contract
public class Player extends Entity implements Damageable {
    private int health = 100;

    @Override
    public void takeDamage(int amount) {
        health -= amount;
        System.out.println("Player took " + amount + " damage!");
    }
    
    @Override
    public int getCurrentHealth() { return health; }
}

// A BreakableBarrel is ALSO an Entity, and it ALSO signs the contract!
public class BreakableBarrel extends Entity implements Damageable {
    private int health = 50;

    @Override
    public void takeDamage(int amount) {
        health -= amount;
        if (health <= 0) {
            System.out.println("Barrel exploded!");
        }
    }
    
    @Override
    public int getCurrentHealth() { return health; }
}
```

Notice what just happened. `Player` and `BreakableBarrel` are completely different types of objects. They don't share a close family tree. But because they both signed the `Damageable` contract, they share a capability.

And the best part? A class can implement **as many interfaces as it wants**. 

```java
// A class can extend ONE superclass, but implement MULTIPLE interfaces!
public class FlyingEnemy extends Enemy implements Damageable, Renderable, Collidable {
    // ... implements all the required methods for all three contracts ...
}
```

### The Magic: Programming to an Interface

Here is where the true power of interfaces reveals itself. Because `Player`, `Enemy`, and `BreakableBarrel` all implement `Damageable`, we can group them together based on their *capabilities*, not their *identities*.

Imagine we want to write a `DamageSystem` that processes environmental hazards (like a lava pit) and damages everything inside it. 

Because we are using interfaces, we don't need to know if the object is a Player, an Enemy, or a Barrel. We just need to know that it signed the contract.

```java
public class DamageSystem {
    
    // Look at this parameter! It's not a List<Entity>. 
    // It's a List of anything that signed the Damageable contract!
    public void applyLavaDamage(List<Damageable> thingsInLava) {
        
        for (Damageable d : thingsInLava) {
            // We don't need instanceof checks! 
            // The compiler guarantees 'd' has a takeDamage method.
            d.takeDamage(10); 
        }
    }
}
```

This is a massive paradigm shift called **"Programming to an interface, not an implementation."** 

When we programmed to the `Entity` class, we were tied to the vertical hierarchy. When we program to the `Damageable` interface, we are tying our code to a *capability*. Our `DamageSystem` is now completely decoupled from the specific classes in our game. You could add a `PetDog`, a `GlassWindow`, or a `WoodenShield` to the game, have them implement `Damageable`, and the `DamageSystem` will process them perfectly without changing a single line of its code.

### Summary: Interfaces vs. Abstract Classes

*   **The Problem:** Single inheritance limits how we share behavior. Tying behavior to a class hierarchy creates awkward, deep trees and code duplication.
*   **The Solution:** Interfaces define a *contract* of behavior (what an object can do) without dictating its identity (what an object is).
*   **The Rules:** 
    *   A class can only `extends` **one** superclass, but it can `implements` **many** interfaces.
    *   Interfaces traditionally contain only abstract method signatures (though modern Java allows some default methods, we will stick to pure contracts for now).
    *   If a class `implements` an interface, it **must** provide the code for every method in that contract, or the code will not compile.
*   **The Golden Rule:** "Program to an interface, not an implementation." Write your systems to accept interfaces (like `Damageable`) rather than concrete classes (like `Player`).
