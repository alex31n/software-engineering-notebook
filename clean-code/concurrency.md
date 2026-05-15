# Concurrency

Concurrency (doing multiple things at the same time) is one of the hardest topics in programming. It is necessary for performance—especially in modern applications handling thousands of users or complex calculations—but it introduces entirely new categories of bugs that are incredibly difficult to find and fix.

Clean concurrent code requires extreme discipline.

## 1. Concurrency is Hard

Before writing concurrent code, remember these facts:
- **It usually adds overhead:** It takes time and resources to manage multiple threads or processes. It doesn't always make things faster.
- **It is complex:** Even simple problems become complex when multiple threads are involved.
- **Bugs are unpredictable:** Concurrency bugs aren't easily repeatable. They happen occasionally under very specific timing conditions (Race Conditions).

## 2. Separate Concurrent Logic from Business Logic

The Single Responsibility Principle (SRP) applies here! Concurrency design is complex enough to be its own responsibility.

You should separate the code that manages the threads/processes from the code that actually does the work.

### ❌ Bad Example: Mixed Logic
The function manages the threading AND the business logic.
```javascript
function processDataConcurrently(dataList):
    for data in dataList:
        Thread.startNew(function(): 
            // 50 lines of complex business logic to process 'data'
            saveToDatabase(data)
        )
```

### ✅ Good Example: Separated Logic
Keep the business logic clean and isolated, and let a manager handle the concurrency.
```javascript
function processDataConcurrently(dataList):
    for data in dataList:
        Thread.startNew(function(): processSingleData(data))

function processSingleData(data):
    // 50 lines of complex business logic
    saveToDatabase(data)
```

## 3. Limit Access to Shared Data

The most common concurrency bug is a **Race Condition**. This happens when two threads try to read and write to the same shared variable at the exact same time.

Imagine Thread A and Thread B both want to add 1 to a counter (which is currently at 5).
- Thread A reads the counter (5).
- Thread B reads the counter (5).
- Thread A adds 1 and writes it back (6).
- Thread B adds 1 and writes it back (6).
*Result:* The counter is 6, but it should be 7!

**How to fix:**
- **Encapsulation:** Severely limit the number of places where shared data can be accessed.
- **Locking:** Use mechanisms (like Mutexes or Locks) to ensure only one thread can access the shared data at a time.

## 4. The Best Shared Data is NO Shared Data

Locking data is slow and can lead to Deadlocks (where Thread A is waiting for Thread B, and Thread B is waiting for Thread A, so the whole program freezes).

The absolute best way to handle concurrency is to avoid sharing data completely.
- Can you give each thread its own independent copy of the data?
- Can threads do their calculations independently and merge the results at the very end?

Whenever possible, design your system so threads do not need to talk to each other or share variables.

## 5. Keep Synchronized Sections Small

If you *must* use locks to protect shared data, keep the locked section of code as small and fast as absolutely possible.

If you lock a large section of code, you destroy the benefits of concurrency because all other threads have to wait in a long line for the lock to be released.

### ❌ Bad Example: Locking too much
```javascript
function updateInventory(item):
    lock():
        variable data = readFromDatabase(item) // Slow!
        data.count = data.count - 1
        writeToDatabase(data)                  // Slow!
    unlock()
```

### ✅ Good Example: Locking only what is necessary
```javascript
function updateInventory(item):
    variable data = readFromDatabase(item) // Can be done concurrently
    
    variable newCount
    lock(): // Only lock the memory update
        data.count = data.count - 1
        newCount = data.count
    unlock()
    
    writeToDatabase(data) // Can be done concurrently
```