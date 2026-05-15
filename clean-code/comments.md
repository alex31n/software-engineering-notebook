# Comments

In an ideal world, code should be so clear and expressive that it doesn't need comments. However, we don't live in an ideal world. Comments are sometimes necessary, but they should be used sparingly and carefully.

A common saying in clean code is: **"Don't comment bad code—rewrite it."**

## The Problem with Comments

Code changes, but comments often don't. When developers update logic, they frequently forget to update the comments attached to it. This leads to the worst kind of comment: **a lie**. A misleading comment is worse than no comment at all because it sends the reader down the wrong path.

## Good Comments

When are comments justified? 

- **Legal Comments:** Copyrights and licenses at the top of a file.
- **Explaining "Why" (Intent):** Sometimes the code tells you *what* it is doing, but it is impossible to know *why* it is doing it without context.
- **Warnings:** Warning other developers about dangerous consequences.

### ✅ Good Example: Explaining Intent
```javascript
// We are adding a 2-second delay here because the external
// payment gateway requires a cooling-off period between requests.
sleep(2000)
```

## Bad Comments

Most comments fall into the "bad" category.

- **Redundant Comments:** Comments that just repeat what the code already says clearly.
- **Noise Comments:** Comments that add no value.
- **Commented-Out Code:** The absolute worst offender. If you don't need the code, delete it! Version control systems (like Git) remember it for you if you ever need it back.

### ❌ Bad Example: Redundant Comments
```javascript
// Create a new user
variable newUser = create(User)

// Set the user's age to 25
newUser.age = 25
```

### ❌ Bad Example: Commented-Out Code
```javascript
function processOrder(order):
    validate(order)
    // calculateTax(order) <-- Why is this here? Is it broken? Should I uncomment it?
    save(order)
```

## How to Avoid Comments

The best way to write fewer comments is to use **clear naming** and **extract variables or functions**.

### ❌ Before (Relying on a comment)
```javascript
// Check if the user is eligible for a senior discount
if user.age > 65 and user.hasMembership == true:
    applyDiscount()
```

### ✅ After (Code explains itself)
```javascript
variable isEligibleForSeniorDiscount = (user.age > 65 and user.hasMembership == true)

if isEligibleForSeniorDiscount:
    applyDiscount()
```