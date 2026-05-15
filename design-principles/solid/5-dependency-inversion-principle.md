# Dependency Inversion Principle (DIP)

## What is it?
The **Dependency Inversion Principle (DIP)** is the "D" in the SOLID principles.

In simple terms, it states:
> **1. High-level modules should not depend on low-level modules. Both should depend on abstractions (interfaces).**
> **2. Abstractions should not depend on details. Details should depend on abstractions.**

This sounds complicated, but it simply means your important, core code shouldn't be glued to minor, specific details. Instead, they should both connect through a common rulebook (an interface).

## Real-World Analogy
Think about a **wall socket** in your house. 

When you want to plug in a lamp, you don't wire the lamp directly into the electrical wires inside the wall. If you did, you could never move the lamp or plug in a TV instead!

The wall socket acts as an **interface**. 
- The house's electrical system (high-level) provides power to the socket.
- Your lamp or TV (low-level) has a plug that fits the socket.

Because they both depend on the socket (the abstraction) instead of each other directly, you can easily swap the lamp for a TV, a laptop charger, or a fan.

## The Problem (Bad Example)

Let's write a program where a `Store` needs to process payments. If the store decides to stop using Stripe and switch to PayPal, we have to rewrite the `Store` class. The high-level `Store` is heavily dependent on the low-level detail (`StripePayment`).

### Python
```python
# A low-level class for a specific payment method
class StripePayment:
    def pay(self, amount):
        print(f"Paid ${amount} using Stripe.")

# The high-level class (our core business logic)
class Store:
    def __init__(self):
        # ❌ VIOLATION: The Store is directly glued to Stripe
        self.stripe = StripePayment()

    def checkout(self, amount):
        self.stripe.pay(amount)
```

### Java
```java
// A low-level class for a specific payment method
class StripePayment {
    public void pay(int amount) {
        System.out.println("Paid $" + amount + " using Stripe.");
    }
}

// The high-level class (our core business logic)
class Store {
    // ❌ VIOLATION: The Store is directly glued to Stripe
    private StripePayment stripe;

    public Store() {
        this.stripe = new StripePayment();
    }

    public void checkout(int amount) {
        stripe.pay(amount);
    }
}
```

## The Solution (Good Example)

Let's introduce an "interface" (like the wall socket) so the `Store` doesn't care *how* the payment is made, as long as it gets made. Now, the `Store` just says, "I need something that can pay." We can easily pass it a `StripePayment` or a `PayPalPayment` without ever changing the code inside the `Store` class!

### Python
```python
from abc import ABC, abstractmethod

# 1. Create the abstraction (the wall socket)
class PaymentMethod(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

# 2. The low-level details depend on the abstraction
class StripePayment(PaymentMethod):
    def pay(self, amount):
        print(f"Paid ${amount} using Stripe.")

class PayPalPayment(PaymentMethod):
    def pay(self, amount):
        print(f"Paid ${amount} using PayPal.")

# 3. The high-level class also depends on the abstraction
class Store:
    # We "inject" the dependency. The Store doesn't create it.
    def __init__(self, payment_method: PaymentMethod):
        self.payment_method = payment_method

    def checkout(self, amount):
        self.payment_method.pay(amount)
```

### Java
```java
// 1. Create the abstraction (the wall socket)
interface PaymentMethod {
    void pay(int amount);
}

// 2. The low-level details depend on the abstraction
class StripePayment implements PaymentMethod {
    public void pay(int amount) {
        System.out.println("Paid $" + amount + " using Stripe.");
    }
}

class PayPalPayment implements PaymentMethod {
    public void pay(int amount) {
        System.out.println("Paid $" + amount + " using PayPal.");
    }
}

// 3. The high-level class also depends on the abstraction
class Store {
    private PaymentMethod paymentMethod;

    // We "inject" the dependency. The Store doesn't create it.
    public Store(PaymentMethod paymentMethod) {
        this.paymentMethod = paymentMethod;
    }

    public void checkout(int amount) {
        paymentMethod.pay(amount);
    }
}
```

## Why is this important?
1. **Easy to Change:** You can swap out old systems for new ones (like changing databases or payment providers) without breaking your core application.
2. **Reusability:** The high-level code isn't tied to specific details, so you can use it in other projects.
3. **Easier to Test:** You can easily pass "fake" or "mock" versions of low-level classes into your high-level classes when writing automated tests.
