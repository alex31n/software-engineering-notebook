# Boundaries

No software project exists in a vacuum. We constantly use external resources: third-party libraries, open-source packages, databases, and APIs. 

Clean boundaries are about how you integrate this "foreign" code into your own "core" code. If you aren't careful, external code will bleed into every corner of your application, making it almost impossible to update or replace later.

## 1. Don't Let External Code Pollute Your Core

Third-party providers often design their tools to be very broad so they can serve many different users. However, your application usually only needs a very specific subset of that functionality.

If you scatter third-party code directly throughout your project, you become tightly coupled to it. If the third-party library updates and changes its functions, you have to find and fix every single place you used it!

### ❌ Bad Example: Tightly Coupled to a Third-Party Logger
Imagine using a third-party library called `MegaLogger`.

```javascript
// In UserModule
MegaLogger.logEvent("user_created", user.id, MegaLogger.INFO_LEVEL)

// In PaymentModule
MegaLogger.logEvent("payment_sent", amount, MegaLogger.WARN_LEVEL)

// In EmailModule
MegaLogger.logEvent("email_failed", error, MegaLogger.ERROR_LEVEL)
```
*Problem:* What happens if `MegaLogger` changes `logEvent` to `writeLog` in version 2.0? You have to change it in three (or three hundred) files!

## 2. Use Wrappers (Adapters)

To protect your code, you should build a **Wrapper** (or Adapter) around the third-party code. 

Your application only talks to your Wrapper. Your Wrapper is the *only* thing that talks to the third-party code.

### ✅ Good Example: Using a Wrapper

**Step 1: Create the Wrapper**
```javascript
class MyCustomLogger:
    variable thirdPartyLogger = new MegaLogger()

    function logInfo(message, id):
        thirdPartyLogger.logEvent(message, id, MegaLogger.INFO_LEVEL)
        
    function logError(message, id):
        thirdPartyLogger.logEvent(message, id, MegaLogger.ERROR_LEVEL)
```

**Step 2: Use YOUR Wrapper, not the third-party code**
```javascript
// In UserModule
MyCustomLogger.logInfo("user_created", user.id)

// In PaymentModule
MyCustomLogger.logInfo("payment_sent", amount)
```
*Solution:* Now, if `MegaLogger` updates its syntax to `writeLog`, you only have to change it in **one** file: `MyCustomLogger.js`. The rest of your application doesn't even know `MegaLogger` exists!

## 3. Learning Tests

When you start using a new third-party API or library, it can take days to read the documentation and figure out how it works. 

Instead of experimenting directly inside your production code, write **Learning Tests**. 

Write simple, automated tests against the third-party API to figure out how it behaves. 
- "Does it return null or throw an error if the user isn't found?" Write a test to find out!
- "What format does the date come back in?" Write a test to check!

### Benefits of Learning Tests:
1. They help you learn the API quickly and safely.
2. When the third-party library releases a new version, you can just run your Learning Tests! If they pass, you know the update is safe. If they fail, you know exactly what the third-party changed before it breaks your production code.

## 4. Code That Doesn't Exist Yet

Sometimes you have to work with another team, and the API they are building isn't finished yet. You shouldn't stop working just because they aren't done.

Define the interface *you* want. Write an interface that provides the exact functions you wish the other team's API had. You can write a "Dummy" implementation of this interface to keep your code compiling and testing. 

When the other team finally finishes their API, you simply write an Adapter that translates your interface into their real API.