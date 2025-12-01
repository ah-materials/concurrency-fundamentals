# Race Conditions

## What Is a Race Condition?

When the behavior of your program depends on the **timing or ordering** of uncontrolled events (like thread scheduling).

The result is unpredictable - sometimes it works, sometimes it doesn't.

## Simple Example

```
balance = 100

Thread 1:                Thread 2:
read balance (100)       read balance (100)
add 50                   add 30
write balance (150)      write balance (130)

Final balance: 130 (should be 180!)
```

The threads "race" to update the balance - whoever writes last wins, and the other update is lost.

## Why It Happens

### The Problem: Non-Atomic Operations

What looks like one operation in code is actually multiple steps:

```
balance = balance + 50

Actually:
1. Read balance from memory
2. Add 50 to it
3. Write result back to memory
```

If another thread runs between these steps, data gets corrupted.

## Visual Timeline

```
Time →
Thread 1: [Read:100] -------- [Add 50] -- [Write:150]
Thread 2: ---------- [Read:100] [Add 30] [Write:130] --

Result: Last write wins, 130 (wrong!)
```

## Real-World Examples

### Example 1: Counter
```
hits = 0

function record_hit():
    hits = hits + 1  // Race condition!

// 100 threads each call record_hit()
// Expected: hits = 100
// Actual: hits = 73 (some updates lost)
```

### Example 2: Check-Then-Act
```
if balance >= amount:
    // What if another thread withdraws here?
    balance = balance - amount
```

Timeline:
```
Thread 1: Check balance (100 >= 80) ✓
Thread 2: Check balance (100 >= 60) ✓
Thread 1: Withdraw 80 (balance = 20)
Thread 2: Withdraw 60 (balance = -40) ← Overdraft!
```

### Example 3: Lazy Initialization
```
if instance == null:
    // What if another thread creates it here?
    instance = new Object()
```

Result: Two instances created (should be singleton).

## Types of Race Conditions

### 1. Read-Modify-Write
```
x = x + 1
```
Read → Modify → Write sequence can be interrupted.

### 2. Check-Then-Act
```
if condition:
    act_on_condition()
```
Condition might change between check and act.

### 3. Lost Update
```
Thread 1: Write x = 5
Thread 2: Write x = 10
```
First update is lost.

### 4. Dirty Read
```
Thread 1: Partially update structure
Thread 2: Read partially updated structure
```
Thread 2 sees inconsistent data.

## How to Prevent Race Conditions

### Solution 1: Locks (Mutual Exclusion)
```
lock = Mutex()

lock.acquire()
balance = balance + 50
lock.release()
```

Only one thread can execute the critical section at a time.

### Solution 2: Atomic Operations
```
atomic_balance.add(50)  // Single, uninterruptible operation
```

Hardware-level operation that can't be interrupted.

### Solution 3: Thread-Safe Data Structures
```
thread_safe_counter.increment()
```

Data structure handles synchronization internally.

### Solution 4: Avoid Sharing
```
thread_local_balance = get_thread_local_storage()
thread_local_balance += 50
```

Each thread has its own copy - no sharing, no race.

### Solution 5: Immutable Data
```
new_balance = old_balance + 50
atomic_swap(balance_ref, old_balance, new_balance)
```

Never modify data, create new versions.

## Fixed Examples

### Counter (Fixed)
```
hits = 0
lock = Mutex()

function record_hit():
    with lock:
        hits = hits + 1
```

### Check-Then-Act (Fixed)
```
with lock:
    if balance >= amount:
        balance = balance - amount
    else:
        raise InsufficientFunds
```

### Lazy Initialization (Fixed)
```
lock = Mutex()

function get_instance():
    with lock:
        if instance == null:
            instance = new Object()
    return instance
```

## Detecting Race Conditions

### Symptoms
- Works in testing, fails in production
- Works with fewer threads, fails with more
- Fails intermittently, hard to reproduce
- Different results on different runs
- Debugger seems to "fix" the problem (changes timing)

### Tools
- Thread sanitizers (TSan, Valgrind)
- Static analysis tools
- Stress testing with many threads
- Logging with timestamps

## Best Practices

✅ **Identify shared mutable state** - That's where races happen

✅ **Protect shared data** - Use locks or atomic operations

✅ **Keep critical sections small** - Less time in lock = fewer waits

✅ **Document synchronization** - Explain what protects what

✅ **Test with many threads** - Increase likelihood of race manifesting

✅ **Use higher-level constructs** - Thread-safe queues, concurrent collections

## Common Pitfalls

⚠️ **Assuming operations are atomic**: `x++` is not atomic!

⚠️ **Forgetting to lock** all access paths to shared data

⚠️ **Checking condition without lock**: Then acting on it with lock

⚠️ **Trusting "it works in testing"**: Races are timing-dependent

⚠️ **Using print debugging**: Changes timing, hides the race

## Example: The Classic Bug

```
// WRONG: Check outside lock
if not queue.is_empty():
    with lock:
        item = queue.remove()  // Queue might be empty now!

// CORRECT: Check inside lock
with lock:
    if not queue.is_empty():
        item = queue.remove()
```

## Testing for Race Conditions

```
// Stress test
function test_counter():
    counter = Counter()
    threads = []

    for i in range(100):
        thread = create_thread(lambda:
            for j in range(1000):
                counter.increment()
        )
        threads.append(thread)

    for thread in threads:
        thread.join()

    assert counter.value == 100000  // Should be 100*1000
```

Run this many times - if it ever fails, you have a race condition.

## Key Takeaways

1. Race conditions happen when threads access shared data without coordination
2. They're non-deterministic - hard to reproduce and debug
3. Prevention: locks, atomic operations, or avoid sharing
4. Always test concurrent code with multiple threads under load
5. Tools exist to help detect races (use them!)

## Related Topics

- [Locks and Mutexes](07-locks-and-mutexes.md) - Main solution to races
- [Atomic Operations](09-atomic-operations.md) - Alternative to locks
- [Thread Safety](06-thread-safety.md) - Writing race-free code
- [Multithreading](03-multithreading.md) - Context for race conditions
