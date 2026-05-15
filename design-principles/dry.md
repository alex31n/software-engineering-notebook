# DRY (Don't Repeat Yourself) Principle

## What is DRY?
The **DRY** principle stands for **Don't Repeat Yourself**. It is one of the most fundamental concepts in software engineering. The core idea is simple: **Every piece of knowledge or logic should have a single, unambiguous representation within a system.**

In simpler terms: **Write your code once.** If you find yourself copying and pasting the exact same code in multiple places, you are violating the DRY principle (often called WET code: "Write Everything Twice" or "We Enjoy Typing").

## Why is DRY important?
1. **Easier Maintenance:** If a business rule changes, you only need to update the code in one place. If the code is duplicated, you have to remember to update it everywhere, which easily leads to bugs.
2. **Readability:** Reusing code by grouping it into functions or classes makes the main code much cleaner and easier to read.
3. **Fewer Bugs:** Less duplicated code generally means fewer places for bugs to hide.

## Example 1: Calculating Discounts

Imagine you are building an e-commerce application. You need to calculate the final price after a 10% discount.

### ❌ The WET Way (Violating DRY)

Notice how the formula `price - (price * 0.10)` is repeated in multiple places.

**Python:**
```python
def checkout_cart(cart_total):
    # Calculate total after 10% discount
    final_price = cart_total - (cart_total * 0.10)
    print(f"Please pay: {final_price}")

def apply_coupon_to_item(item_price):
    # Calculate item price after 10% discount
    discounted_price = item_price - (item_price * 0.10)
    print(f"Item price is now: {discounted_price}")
```

**Java:**
```java
public class ShoppingCart {
    public void checkoutCart(double cartTotal) {
        // Calculate total after 10% discount
        double finalPrice = cartTotal - (cartTotal * 0.10);
        System.out.println("Please pay: " + finalPrice);
    }

    public void applyCouponToItem(double itemPrice) {
        // Calculate item price after 10% discount
        double discountedPrice = itemPrice - (itemPrice * 0.10);
        System.out.println("Item price is now: " + discountedPrice);
    }
}
```

If the business decides to change the discount to 15%, you must find and update the formula in both functions. What if this formula was used in 50 different places?

### ✅ The DRY Way (Applying the Principle)

We can extract the calculation into its own separate function. Now, the logic lives in exactly one place.

**Python:**
```python
def calculate_discount(price, discount_rate=0.10):
    """Calculates the price after applying a discount."""
    return price - (price * discount_rate)

def checkout_cart(cart_total):
    final_price = calculate_discount(cart_total)
    print(f"Please pay: {final_price}")

def apply_coupon_to_item(item_price):
    discounted_price = calculate_discount(item_price)
    print(f"Item price is now: {discounted_price}")
```

**Java:**
```java
public class ShoppingCart {
    
    // Extracted logic into a single method
    private double calculateDiscount(double price) {
        double discountRate = 0.10;
        return price - (price * discountRate);
    }

    public void checkoutCart(double cartTotal) {
        double finalPrice = calculateDiscount(cartTotal);
        System.out.println("Please pay: " + finalPrice);
    }

    public void applyCouponToItem(double itemPrice) {
        double discountedPrice = calculateDiscount(itemPrice);
        System.out.println("Item price is now: " + discountedPrice);
    }
}
```
Now, if the discount rate changes, you only update the `calculateDiscount` method!

## Example 2: Validating User Input

Let's say we are registering users, and we need to check if the provided email and username are not empty.

### ❌ The WET Way (Violating DRY)

**Python:**
```python
def register_user(username, email):
    if username == "" or username is None:
        raise ValueError("Username cannot be empty")
    
    if email == "" or email is None:
        raise ValueError("Email cannot be empty")
        
    print("User registered!")
```

**Java:**
```java
public class UserRegistration {
    public void registerUser(String username, String email) {
        if (username == null || username.trim().isEmpty()) {
            throw new IllegalArgumentException("Username cannot be empty");
        }
        
        if (email == null || email.trim().isEmpty()) {
            throw new IllegalArgumentException("Email cannot be empty");
        }
        
        System.out.println("User registered!");
    }
}
```

### ✅ The DRY Way (Applying the Principle)

We extract the common validation logic into a helper function.

**Python:**
```python
def validate_not_empty(value, field_name):
    if not value: # In Python, empty strings and None evaluate to False
        raise ValueError(f"{field_name} cannot be empty")

def register_user(username, email):
    validate_not_empty(username, "Username")
    validate_not_empty(email, "Email")
    print("User registered!")
```

**Java:**
```java
public class UserRegistration {
    
    private void validateNotEmpty(String value, String fieldName) {
        if (value == null || value.trim().isEmpty()) {
            throw new IllegalArgumentException(fieldName + " cannot be empty");
        }
    }

    public void registerUser(String username, String email) {
        validateNotEmpty(username, "Username");
        validateNotEmpty(email, "Email");
        
        System.out.println("User registered!");
    }
}
```

## A Word of Caution: "Over-DRYing" (Premature Optimization)
While DRY is a fantastic rule, don't blindly combine code just because it looks similar right now. Sometimes two pieces of code look identical today, but they represent completely different business concepts that will change independently in the future. 

**Rule of thumb:** If the reasons for the code to change are the same, apply DRY. If the reasons for the code to change are different, it might be okay to let them be slightly repetitive to maintain flexibility.