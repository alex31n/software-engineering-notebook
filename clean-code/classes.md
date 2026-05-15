# Classes and Data Structures

Just as functions should be small and do one thing, classes should follow similar rules to ensure your software architecture remains clean, flexible, and easy to understand.

## 1. Classes Should be Small

The first rule of classes is that they should be small. The second rule of classes is that they should be smaller than that.

While we measure the size of a function by physical lines of code, we measure the size of a class by its **responsibilities**.

## 2. The Single Responsibility Principle (SRP)

A class should have only **one reason to change**. This means it should have only one core responsibility. 

If a class manages user data, connects to a database, and generates PDF reports, it has three responsibilities. This is a "God Class" and it is very hard to maintain.

### ❌ Bad Example: A God Class
```javascript
class UserManager:
    function calculateUserAge(user): // Logic responsibility
        // ...
        
    function saveUserToDatabase(user): // Database responsibility
        // ...
        
    function generateUserReport(user): // Presentation responsibility
        // ...
```

### ✅ Good Example: Splitting Responsibilities
```javascript
class UserProfile: // Handles user logic
    function calculateAge():
        // ...

class UserRepository: // Handles database
    function save(userProfile):
        // ...

class UserReportGenerator: // Handles presentation
    function generate(userProfile):
        // ...
```

## 3. Objects vs. Data Structures

It's important to understand the difference between true Objects and mere Data Structures. They have opposite purposes!

- **Data Structures** expose their data (public variables) and have no meaningful behavior (functions). Their job is just to hold information.
- **Objects** hide their data (private variables) behind abstractions and expose behavior (functions).

You should avoid creating "hybrids" that try to be both, as they usually fail at both.

### Data Structure Example (Good for holding data)
```javascript
class Point:
    variable x
    variable y
```

### Object Example (Good for hiding data and exposing behavior)
```javascript
class BankAccount:
    variable balance = 0
    
    function deposit(amount):
        if amount > 0:
            balance = balance + amount
            
    function getBalance():
        return balance
```

## 4. Encapsulation (Hiding Data)

You should almost never make the variables inside an Object public. By keeping variables private, you protect the internal state of the object. Other parts of the code must use the public functions (like `deposit` above) to interact with it. 

If you make variables public, anyone can change them at any time, leading to unpredictable bugs.

## 5. The Law of Demeter

The Law of Demeter is a design guideline that says: **"Don't talk to strangers."** 

An object should only communicate with its immediate friends, not navigate deeply through other objects. Navigating deeply creates "train wrecks" of code that are highly coupled and fragile.

### ❌ Bad Example: A Train Wreck
The code is reaching through the user, to get the address, to get the city, to get the zip code. If the address structure changes, this code breaks.
```javascript
function getZipCode(user):
    return user.getAddress().getCity().getZipCode()
```

### ✅ Good Example: Asking instead of reaching
Instead of reaching inside, ask the object to do the work for you.
```javascript
function getZipCode(user):
    return user.getZipCode() // The User object figures it out internally
```