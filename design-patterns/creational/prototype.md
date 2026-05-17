# Prototype Design Pattern

The Prototype Pattern is a creational design pattern that lets you copy existing objects without making your code dependent on their classes. Instead of creating new objects using the `new` keyword (which can be slow and complicated if the object has a lot of setup), you take an existing object and say, "Make a clone of yourself."

Imagine you are playing a video game where you need to spawn thousands of identical enemies, like zombies. Creating each zombie from scratch (setting its health, speed, weapons, armor, etc.) takes a lot of time and computer memory.

What if, instead of creating each one from scratch, you create just **one** perfect zombie and then just make **copies** (or clones) of it?

That is exactly what the **Prototype Pattern** does!

## Real-World Analogy

Think of a **photocopier**.
If you have a complex document with lots of text, images, and formatting, you don't type the whole document again to get a second copy. You just put it in the photocopier and press "copy". The original document is the **prototype**, and the new copies are the **clones**.

## Why use the Prototype Pattern?

1.  **Saves Time and Resources:** Creating complex objects from scratch can be expensive (like fetching data from a database to set up an object). Cloning an existing object is usually much faster.
2.  **Hides Complexity:** The code that needs the copy doesn't need to know *how* the object is built. It just asks the object to clone itself.
3.  **Flexibility:** You can create copies of objects even if you only know their general type (an interface), not their exact specific class.

## Code Examples

Let's look at how we can implement the Prototype Pattern using a simple example: cloning different types of `Monster` in a game.

### Python Implementation

In Python, the `copy` module makes implementing the Prototype pattern incredibly easy using `copy.deepcopy()`.

```python
import copy

class Monster:
    """The base Prototype class."""
    def __init__(self, name, health, damage):
        self.name = name
        self.health = health
        self.damage = damage

    def clone(self):
        """Creates a deep copy of the monster."""
        # deepcopy ensures that if the object contains other objects (like a list of weapons),
        # those inner objects are copied too, not just referenced.
        return copy.deepcopy(self)

    def __str__(self):
        return f"Monster: {self.name}, Health: {self.health}, Damage: {self.damage}"

# 1. Create our initial "Prototypes" (the originals)
zombie_prototype = Monster("Basic Zombie", health=100, damage=10)
vampire_prototype = Monster("Fast Vampire", health=80, damage=25)

print("--- Originals ---")
print(zombie_prototype)
print(vampire_prototype)

# 2. We need a new zombie! Instead of creating from scratch, we clone the prototype.
zombie_clone_1 = zombie_prototype.clone()
# We can change the clone without affecting the original
zombie_clone_1.name = "Zombie Clone #1"

zombie_clone_2 = zombie_prototype.clone()
zombie_clone_2.name = "Zombie Clone #2"

print("\n--- Clones ---")
print(zombie_clone_1)
print(zombie_clone_2)

# Verify the original is unchanged
print("\n--- Original is still unchanged ---")
print(zombie_prototype)
```

### Java Implementation

In Java, we typically implement the `Cloneable` interface and override the `clone()` method.

```java
// 1. Create a common interface or abstract class for our prototypes
abstract class Monster implements Cloneable {
    protected String name;
    protected int health;
    protected int damage;

    public Monster(String name, int health, int damage) {
        this.name = name;
        this.health = health;
        this.damage = damage;
    }

    // The magical clone method
    @Override
    public Monster clone() {
        try {
            // This does a "shallow copy". For simple types (int, String), it's fine.
            // If the class had complex objects (like a List), we'd need to copy those manually here for a "deep copy".
            return (Monster) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
            return null;
        }
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Monster: " + name + ", Health: " + health + ", Damage: " + damage;
    }
}

// 2. Concrete Prototype classes
class Zombie extends Monster {
    public Zombie(String name, int health, int damage) {
        super(name, health, damage);
    }
}

class Vampire extends Monster {
    public Vampire(String name, int health, int damage) {
        super(name, health, damage);
    }
}

public class PrototypeDemo {
    public static void main(String[] args) {
        // 1. Create our prototypes
        Monster zombiePrototype = new Zombie("Basic Zombie", 100, 10);
        Monster vampirePrototype = new Vampire("Fast Vampire", 80, 25);

        System.out.println("--- Originals ---");
        System.out.println(zombiePrototype);
        System.out.println(vampirePrototype);

        // 2. Clone them!
        Monster zombieClone1 = zombiePrototype.clone();
        zombieClone1.setName("Zombie Clone #1");

        Monster vampireClone1 = vampirePrototype.clone();
        vampireClone1.setName("Vampire Clone #1");

        System.out.println("\n--- Clones ---");
        System.out.println(zombieClone1);
        System.out.println(vampireClone1);
    }
}
```

## Summary: When should you use it?

*   When the cost of creating a new object is very high (e.g., requires database calls, reading files, or complex calculations).
*   When your system needs to be independent of how its objects are created, composed, and represented.
*   When you have a lot of classes that differ only in their starting values. Instead of creating a new class for every tiny variation, you create one prototype and clone it, tweaking the values on the clone.

## Pros and Cons

**Pros:**
*   **Faster object creation:** Cloning is usually faster than using `new`.
*   **Less complex code:** Avoids running complicated initialization code over and over.
*   **Alternative to subclassing:** You don't need a huge hierarchy of "Creator" classes.

**Cons:**
*   **Deep Cloning can be tricky:** If your prototype object contains other objects (like lists or custom objects), you must make sure you copy those inner objects too (a "deep copy"). If you don't, your clone might accidentally share parts with the original prototype, leading to weird bugs! (In Python, `copy.deepcopy()` handles this automatically, but in Java, you often have to write custom code to handle it).
