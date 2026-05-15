# Composition Over Inheritance

## What does it mean?
In object-oriented programming (OOP), there are two main ways to reuse code and build complex objects: **Inheritance** and **Composition**.

*   **Inheritance** represents an **"IS-A"** relationship. (e.g., A Car *is a* Vehicle. A Dog *is an* Animal).
*   **Composition** represents a **"HAS-A"** relationship. (e.g., A Car *has an* Engine. A Dog *has a* Tail).

The **Composition over Inheritance** principle states that you should prefer building objects by putting together smaller, interchangeable parts (Composition) rather than building deep family trees of classes (Inheritance).

## The Problem with Inheritance (The "Gorilla and Banana" Problem)
Inheritance seems great at first, but it can quickly become a trap. 

1.  **Deep, confusing family trees:** As your program grows, inheritance trees get very deep. Changing a class at the top of the tree can break classes at the bottom.
2.  **Inflexible:** You are forced to inherit *everything* from a parent class, even things you don't need. 
    > *"You wanted a banana but what you got was a gorilla holding the banana and the entire jungle."* - Joe Armstrong (Creator of Erlang)
3.  **The "Multiple Roles" problem:** What if an object needs to be two things at once? Many languages don't allow inheriting from multiple classes to prevent messiness.

## Example: Designing Video Game Characters

Let's imagine we are building a video game with different types of characters.

### ❌ The Inheritance Way (Violating the Principle)

We start with a base `Character` class, and we make a `Fighter` that can attack, and a `Healer` that can heal.

**Python:**
```python
class Character:
    def walk(self):
        print("Walking...")

class Fighter(Character):
    def attack(self):
        print("Swinging sword!")

class Healer(Character):
    def heal(self):
        print("Casting heal spell!")
```

**Java:**
```java
class Character {
    public void walk() {
        System.out.println("Walking...");
    }
}

class Fighter extends Character {
    public void attack() {
        System.out.println("Swinging sword!");
    }
}

class Healer extends Character {
    public void heal() {
        System.out.println("Casting heal spell!");
    }
}
```

**The Trap:** Now, the game designer says: *"I want a **Paladin**! A character that can both attack AND heal!"*

With inheritance, we are stuck. We can't inherit from both `Fighter` and `Healer` cleanly (Java doesn't allow it, and in Python, multiple inheritance gets very confusing quickly). If we put `attack` and `heal` into the base `Character` class, then *every* character (even a peasant) gets those abilities, which we don't want!

### ✅ The Composition Way (Applying the Principle)

Instead of saying "A Paladin IS A Fighter and IS A Healer", we change our thinking to: "A Paladin HAS An attack behavior and HAS A heal behavior". We build our character out of parts.

**Python:**
```python
# 1. Define the small parts (Behaviors)
class AttackBehavior:
    def execute(self):
        print("Swinging sword!")

class HealBehavior:
    def execute(self):
        print("Casting heal spell!")

# 2. Build the Character by giving it the parts it needs
class Character:
    def __init__(self, name, attack_behavior=None, heal_behavior=None):
        self.name = name
        self.attack_behavior = attack_behavior
        self.heal_behavior = heal_behavior
        
    def walk(self):
        print(f"{self.name} is walking...")
        
    def attack(self):
        if self.attack_behavior:
            self.attack_behavior.execute()
        else:
            print(f"{self.name} cannot attack.")

    def heal(self):
        if self.heal_behavior:
            self.heal_behavior.execute()
        else:
            print(f"{self.name} cannot heal.")

# 3. Now we can easily create ANY combination!
fighter = Character("Gimli", attack_behavior=AttackBehavior())
healer = Character("Gandalf", heal_behavior=HealBehavior())

# Creating a Paladin is easy! Just give them both behaviors.
paladin = Character("Arthur", attack_behavior=AttackBehavior(), heal_behavior=HealBehavior())

paladin.attack() # Output: Swinging sword!
paladin.heal()   # Output: Casting heal spell!
```

**Java:**
```java
// 1. Define the small parts (Interfaces and Behaviors)
interface AttackBehavior {
    void execute();
}

class SwordAttack implements AttackBehavior {
    public void execute() { System.out.println("Swinging sword!"); }
}

interface HealBehavior {
    void execute();
}

class MagicHeal implements HealBehavior {
    public void execute() { System.out.println("Casting heal spell!"); }
}

// 2. Build the Character by giving it the parts it needs
class Character {
    private String name;
    private AttackBehavior attackBehavior;
    private HealBehavior healBehavior;

    // We pass the behaviors through the constructor (Composition)
    public Character(String name, AttackBehavior attackBehavior, HealBehavior healBehavior) {
        this.name = name;
        this.attackBehavior = attackBehavior;
        this.healBehavior = healBehavior;
    }

    public void attack() {
        if (attackBehavior != null) {
            attackBehavior.execute();
        } else {
            System.out.println(name + " cannot attack.");
        }
    }

    public void heal() {
        if (healBehavior != null) {
            healBehavior.execute();
        } else {
            System.out.println(name + " cannot heal.");
        }
    }
}

// 3. Now we can easily create ANY combination!
public class Main {
    public static void main(String[] args) {
        // A Paladin gets both behaviors!
        Character paladin = new Character("Arthur", new SwordAttack(), new MagicHeal());
        
        paladin.attack(); // Output: Swinging sword!
        paladin.heal();   // Output: Casting heal spell!
        
        // A simple peasant gets neither
        Character peasant = new Character("Bob", null, null);
        peasant.attack(); // Output: Bob cannot attack.
    }
}
```

## Summary
When designing systems, start with **Composition**. Build your classes by plugging in small, focused components. Reserve **Inheritance** only for true "IS-A" relationships where the child class is genuinely a specialized version of the exact same thing as the parent, and you are absolutely sure the hierarchy won't need to drastically change later.