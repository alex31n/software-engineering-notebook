# Strategy Design Pattern

The **Strategy** is a behavioral design pattern that lets you define a family of algorithms (or "strategies"), put each of them into a separate class, and make their objects interchangeable. 

In simple terms, it allows an object to choose how to do a specific task at runtime, rather than having the method hardcoded into the object itself. Instead of one massive class full of `if...else` statements checking "how" to perform an action, you extract each "how" into its own class.

## Real-World Analogy

Imagine you are trying to **get to the airport** from your house. You have a few different strategies for traveling:

1. **Take a Taxi:** Fast, but expensive.
2. **Ride the Bus:** Cheap, but slower.
3. **Ride a Bike:** Free and healthy, but requires you to pack light and takes the longest.

Your goal (getting to the airport) is the same, but the **strategy** you choose depends on your current situation (budget, time, amount of luggage). You can change your travel strategy right before you leave the house without changing the destination.

## Why use the Strategy Pattern?

1. **Eliminates complex `if/else` statements:** If you have many ways to perform a task, you avoid a giant method full of conditional statements.
2. **Open/Closed Principle:** You can introduce new strategies without changing the code of the main object (the "context") or the existing strategies.
3. **Interchangeable at Runtime:** You can switch the algorithm or behavior of an object while the program is running based on user input or other conditions.

## Code Examples

Let's build a simple **Shopping Cart Checkout System**. A customer can choose to pay using different methods: a Credit Card or PayPal. The checkout system doesn't need to know the complex details of how each payment works; it just needs a strategy to execute the payment.

### Python Implementation

```python
from abc import ABC, abstractmethod

# --- Strategy Interface ---
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount: float):
        pass

# --- Concrete Strategies ---
class CreditCardPayment(PaymentStrategy):
    def __init__(self, name: str, card_number: str):
        self.name = name
        self.card_number = card_number

    def pay(self, amount: float):
        # In a real app, complex credit card processing logic goes here
        print(f"Paid ${amount:.2f} using Credit Card ending in {self.card_number[-4:]}")

class PayPalPayment(PaymentStrategy):
    def __init__(self, email: str):
        self.email = email

    def pay(self, amount: float):
        # In a real app, complex PayPal API logic goes here
        print(f"Paid ${amount:.2f} using PayPal account: {self.email}")

# --- Context Class ---
class ShoppingCart:
    def __init__(self):
        self.items = []

    def add_item(self, item_name: str, price: float):
        self.items.append({"name": item_name, "price": price})

    def calculate_total(self) -> float:
        return sum(item["price"] for item in self.items)

    def checkout(self, payment_strategy: PaymentStrategy):
        total = self.calculate_total()
        # The cart delegates the payment task to the selected strategy
        payment_strategy.pay(total)

# --- Example Usage ---
if __name__ == '__main__':
    cart = ShoppingCart()
    cart.add_item("Headphones", 50.00)
    cart.add_item("Keyboard", 30.00)

    # User chooses to pay with Credit Card
    cc_strategy = CreditCardPayment("John Doe", "1234567890123456")
    cart.checkout(cc_strategy)
    
    print("-" * 20)

    # Another user chooses to pay with PayPal
    paypal_strategy = PayPalPayment("jane.doe@example.com")
    cart.checkout(paypal_strategy)
```

### Java Implementation

```java
import java.util.ArrayList;
import java.util.List;

// --- Strategy Interface ---
interface PaymentStrategy {
    void pay(double amount);
}

// --- Concrete Strategies ---
class CreditCardPayment implements PaymentStrategy {
    private String name;
    private String cardNumber;

    public CreditCardPayment(String name, String cardNumber) {
        this.name = name;
        this.cardNumber = cardNumber;
    }

    @Override
    public void pay(double amount) {
        // Complex credit card processing logic goes here
        String lastFour = cardNumber.substring(cardNumber.length() - 4);
        System.out.printf("Paid $%.2f using Credit Card ending in %s%n", amount, lastFour);
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void pay(double amount) {
        // Complex PayPal API logic goes here
        System.out.printf("Paid $%.2f using PayPal account: %s%n", amount, email);
    }
}

// --- Context Class ---
class Item {
    String name;
    double price;

    public Item(String name, double price) {
        this.name = name;
        this.price = price;
    }
}

class ShoppingCart {
    private List<Item> items;

    public ShoppingCart() {
        this.items = new ArrayList<>();
    }

    public void addItem(Item item) {
        this.items.add(item);
    }

    public double calculateTotal() {
        double sum = 0;
        for (Item item : items) {
            sum += item.price;
        }
        return sum;
    }

    public void checkout(PaymentStrategy paymentStrategy) {
        double total = calculateTotal();
        // The cart delegates the payment task to the selected strategy
        paymentStrategy.pay(total);
    }
}

// --- Example Usage ---
public class StrategyPatternDemo {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(new Item("Headphones", 50.00));
        cart.addItem(new Item("Keyboard", 30.00));

        // User chooses to pay with Credit Card
        PaymentStrategy ccStrategy = new CreditCardPayment("John Doe", "1234567890123456");
        cart.checkout(ccStrategy);
        
        System.out.println("--------------------");

        // Another user chooses to pay with PayPal
        PaymentStrategy paypalStrategy = new PayPalPayment("jane.doe@example.com");
        cart.checkout(paypalStrategy);
    }
}
```

## Summary

The Strategy pattern is incredibly useful when you have a specific task that can be accomplished in several different ways. By extracting these "ways" into separate classes, your main code becomes much cleaner, easier to read, and immune to giant blocks of conditional logic. It also makes adding new strategies in the future as simple as creating a new class.