# Decorator Design Pattern

The **Decorator Design Pattern** is a structural design pattern that lets you attach new behaviors to objects dynamically by placing these objects inside special wrapper objects that contain the behaviors.

In simple terms: It is a pattern that allows you to add new features or responsibilities to an individual object without altering its structure or the code of its class. It achieves this by "wrapping" the original object.

## Analogy

Imagine you are ordering a coffee at a cafe. 

You start with a basic, simple coffee (the **base object**). 
Then, you decide you want to add milk. Instead of the barista brewing a completely new "CoffeeWithMilk" drink from scratch, they just take your basic coffee and add milk to it (they **wrap** it with a Milk Decorator). 
Next, you ask for sugar. They take your coffee-with-milk and add sugar to it (they wrap it again with a Sugar Decorator).

You haven't changed what the core "simple coffee" is. You just kept adding extra features (decorators) on top of it dynamically at runtime. The final price and description of your drink is a combination of the base coffee and all the decorators you added.

## The Problem

Imagine you are building an application that sends notifications to users. You start with a basic `Notifier` class that sends notifications via Email.

Later, you realize some users want to be notified via SMS as well. So, you create a new class `SMSNotifier` that extends `Notifier`. Then someone asks for Facebook notifications, so you create `FacebookNotifier`. 

But what if a user wants BOTH SMS and Email? You create `SMSAndEmailNotifier`. What if they want Email, SMS, and Facebook? You create `EmailSMSFacebookNotifier`. 

Very quickly, you will suffer from **Class Explosion**. You will have to create a new class for every single combination of notification methods. This is a nightmare to maintain.

## The Solution

The Decorator pattern solves this by using **Composition** instead of Inheritance. 

Instead of creating a giant hierarchy of subclasses for every combination, you create individual "wrapper" classes (Decorators) for each extra feature (SMS, Facebook, Slack). 

You start with a basic Email Notifier object. If the user wants SMS too, you wrap the Email Notifier inside an SMS Decorator. If they want Facebook too, you wrap that whole thing inside a Facebook Decorator. 

When you trigger the notification, the outermost wrapper does its job (e.g., sends Facebook message) and then delegates the work to the object it's wrapping, continuing down the chain until the base Email Notifier is reached.

## Key Components

1. **Component**: The common interface for both the basic objects and their decorators. (e.g., `Notifier`).
2. **Concrete Component**: The basic, default object that you will add features to. (e.g., `EmailNotifier`).
3. **Decorator (Base Wrapper)**: An abstract class that implements the Component interface and has a field to store a reference to a wrapped Component object. (e.g., `NotifierDecorator`).
4. **Concrete Decorators**: The specific wrappers that add extra behaviors or state to the component. (e.g., `SMSNotifier`, `FacebookNotifier`).

---

## Code Examples

We will use the **Notifier** scenario for the code examples. It perfectly demonstrates how decorators can wrap a base component to dynamically add new communication channels.

### Python Implementation

```python
from abc import ABC, abstractmethod

# 1. Component: The common interface
class Notifier(ABC):
    @abstractmethod
    def send(self, message: str) -> None:
        pass

# 2. Concrete Component: The basic, default object
class EmailNotifier(Notifier):
    def send(self, message: str) -> None:
        print(f"Email: {message}")

# 3. Decorator (Base Wrapper): Implements Component, holds reference to a Component
class NotifierDecorator(Notifier):
    def __init__(self, notifier: Notifier):
        self._notifier = notifier

    def send(self, message: str) -> None:
        self._notifier.send(message)

# 4. Concrete Decorators: Add specific extra behaviors
class SMSNotifier(NotifierDecorator):
    def send(self, message: str) -> None:
        super().send(message)
        print(f"SMS: {message}")

class FacebookNotifier(NotifierDecorator):
    def send(self, message: str) -> None:
        super().send(message)
        print(f"Facebook: {message}")

if __name__ == "__main__":
    # Base notifier
    notifier = EmailNotifier()

    # Decorated notifier
    sms_notifier = SMSNotifier(notifier)
    sms_facebook_notifier = FacebookNotifier(sms_notifier)
    
    sms_facebook_notifier.send("Hello World!")
```

### Java Implementation

```java
// 1. Component: The common interface
interface Notifier {
    void send(String message);
}

// 2. Concrete Component: The basic, default object
class EmailNotifier implements Notifier {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

// 3. Decorator (Base Wrapper): Implements Component, holds reference to a Component
abstract class NotifierDecorator implements Notifier {
    protected Notifier decoratedNotifier; 

    public NotifierDecorator(Notifier notifier) {
        this.decoratedNotifier = notifier;
    }

    @Override
    public void send(String message) {
        decoratedNotifier.send(message);
    }
}

// 4. Concrete Decorators: Add specific extra behaviors
class SMSNotifier extends NotifierDecorator {
    public SMSNotifier(Notifier notifier) {
        super(notifier);
    }

    @Override
    public void send(String message) {
        super.send(message);
        System.out.println("SMS: " + message);
    }
}

class FacebookNotifier extends NotifierDecorator {
    public FacebookNotifier(Notifier notifier) {
        super(notifier);
    }

    @Override
    public void send(String message) {
        super.send(message);
        System.out.println("Facebook: " + message);
    }
}

public class DecoratorPatternDemo {
    public static void main(String[] args) {
        // Base notifier
        Notifier notifier = new EmailNotifier();
        
        // Decorated notifier
        Notifier smsNotifier = new SMSNotifier(notifier);
        Notifier smsFacebookNotifier = new FacebookNotifier(smsNotifier);
        
        smsFacebookNotifier.send("Hello World!");
    }
}
```

---

## When to use the Decorator Pattern?

*   **Adding responsibilities dynamically:** When you need to be able to assign extra behaviors to objects at runtime without breaking the code that uses these objects.
*   **Preventing Class Explosion:** When extending a class using inheritance is awkward or impossible because you need to support many combinations of features. Instead of creating a subclass for every combination, you create decorators.
*   **Third-party code:** When you want to add functionality to a class that is declared `final` (in Java) or when you can't modify the source code of the base class.

## Pros and Cons

### Pros
*   **Flexibility:** You can extend an object's behavior without creating a new subclass. You can add or remove responsibilities from an object at runtime.
*   **Mix and Match:** You can combine several behaviors by wrapping an object into multiple decorators.
*   **Single Responsibility Principle:** You can divide a monolithic class that implements many possible variants of behavior into several smaller classes.

### Cons
*   **Complexity:** It can be hard to remove a specific wrapper from the decorators stack once it's added.
*   **Order dependency:** It's hard to implement a decorator in such a way that its behavior doesn't depend on the order in the decorators stack.
*   **Ugly initialization code:** The initial configuration code of layers might look pretty ugly (e.g., `new Sugar(new Milk(new VanillaSyrup(new SimpleCoffee())))`).
