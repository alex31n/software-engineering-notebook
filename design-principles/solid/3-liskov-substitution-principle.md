# Liskov Substitution Principle (LSP)

## What is it?
The Liskov Substitution Principle (named after Barbara Liskov) states: **If you have a parent class and a child class, you should be able to replace the parent with the child without breaking your program.**

In simpler terms: a subclass (child) should behave like its superclass (parent). If the parent class promises to do something, the child class must also keep that promise. 

## Real-World Analogy
Imagine you ask a friend for a "duck". If they give you a "rubber duck", it might look like a duck, but if you expect it to lay an egg, you're going to have a problem! The rubber duck cannot be a true substitute for a real duck in this scenario.

## The Problem (Bad Example)

Let's say we have a general `Bird` class, and since birds fly, we add a `fly` method.

If a part of your program expects a `Bird` and calls `fly()`, it will work perfectly for a `Sparrow`. But if we substitute the `Bird` with a `Penguin` (which is technically a child of `Bird`), the program will crash! We failed the substitution test.

### Python
```python
class Bird:
    def fly(self):
        return "I am flapping my wings and flying!"

class Sparrow(Bird):
    # Sparrows can fly, so this is fine.
    pass

class Penguin(Bird):
    def fly(self):
        # ❌ VIOLATION: Penguins can't fly! 
        # If a program expects all Birds to fly, this will cause a crash or error.
        raise Exception("Help! I cannot fly!")
```

### Java
```java
class Bird {
    public String fly() {
        return "I am flapping my wings and flying!";
    }
}

class Sparrow extends Bird {
    // Sparrows can fly, so this is fine.
}

class Penguin extends Bird {
    @Override
    public String fly() {
        // ❌ VIOLATION: Penguins can't fly! 
        throw new RuntimeException("Help! I cannot fly!");
    }
}
```

## The Solution (Good Example)

To fix this, we need to rethink our classes. Not all birds can fly, so `fly()` shouldn't be in the base `Bird` class. We should create a separate class for birds that *can* fly.

### Python
```python
# Our base class just has things ALL birds can do.
class Bird:
    def eat(self):
        return "I am eating food."

# We create a specific subclass for birds that fly.
class FlyingBird(Bird):
    def fly(self):
        return "I am flapping my wings and flying!"

# Sparrow is a FlyingBird
class Sparrow(FlyingBird):
    pass

# Penguin is just a Bird, NOT a FlyingBird
class Penguin(Bird):
    def swim(self):
        return "I am swimming in the cold water!"
```

### Java
```java
// Our base class just has things ALL birds can do.
class Bird {
    public String eat() {
        return "I am eating food.";
    }
}

// We create a specific subclass for birds that fly.
class FlyingBird extends Bird {
    public String fly() {
        return "I am flapping my wings and flying!";
    }
}

// Sparrow is a FlyingBird
class Sparrow extends FlyingBird {
}

// Penguin is just a Bird, NOT a FlyingBird
class Penguin extends Bird {
    public String swim() {
        return "I am swimming in the cold water!";
    }
}
```

## Why is this important?
If you break this rule, you can't trust your own code. You'll end up writing messy `if` statements everywhere to check exactly what type of object you are dealing with before you use it. Following LSP keeps your inheritance relationships logical, your code predictable, and ensures any child class can safely substitute its parent class.
