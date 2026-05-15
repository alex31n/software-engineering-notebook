# Testing

Automated testing is the foundation of clean code. Without tests, every change is a risk. With tests, you can refactor confidently and add new features without fear of breaking existing ones.

However, having tests isn't enough. **Test code must be kept just as clean as production code.**

## The Anatomy of a Clean Test: Arrange, Act, Assert

A clean test is easy to read. It should be immediately obvious what is being set up, what action is taking place, and what the expected result is. The standard pattern for this is the **AAA pattern** (sometimes called Given-When-Then).

1. **Arrange:** Set up the initial state and create any data you need.
2. **Act:** Perform the action you are testing (call the function).
3. **Assert:** Check that the result matches your expectations.

### ✅ Good Example: AAA Pattern
```javascript
function test_UserCanBuyAlcoholIfOver21():
    // 1. ARRANGE
    variable user = new User(age: 25)
    
    // 2. ACT
    variable canBuy = user.canBuyAlcohol()
    
    // 3. ASSERT
    expect(canBuy).toBe(true)
```

## One Concept per Test

A test should verify one specific behavior. If a test fails, you should know exactly what went wrong without having to guess which part of the test broke.

### ❌ Bad Example: Testing everything at once
```javascript
function test_ShoppingCart():
    variable cart = new Cart()
    cart.addItem("Apple")
    expect(cart.totalItems).toBe(1)
    
    cart.removeItem("Apple")
    expect(cart.totalItems).toBe(0)
    
    cart.applyDiscount("SUMMER")
    expect(cart.totalPrice).toBe(0) // Too many things happening!
```

### ✅ Good Example: Split into separate tests
```javascript
function test_CartAddsItemCorrectly():
    // ... test adding

function test_CartRemovesItemCorrectly():
    // ... test removing

function test_CartAppliesDiscountCorrectly():
    // ... test discounting
```

## The F.I.R.S.T. Principles

Clean tests should follow these five rules:

1. **Fast:** Tests should run quickly. If they are slow, developers won't run them often.
2. **Independent:** Tests should not depend on each other. You should be able to run them in any order.
3. **Repeatable:** Tests should produce the same result every time, whether they are run locally, on a server, or without internet access.
4. **Self-Validating:** Tests should return a simple boolean output (Pass or Fail). You shouldn't have to read a log file to see if a test passed.
5. **Timely:** Tests should be written just before or at the same time as the production code (as in Test-Driven Development), not weeks later as an afterthought.