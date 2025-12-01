# Locks and Mutexes

## What Are They?

**Mutex** (Mutual Exclusion): A lock that ensures only one thread can access a resource at a time.

Think of it like a bathroom key - only one person can use the bathroom at a time.

## Why Do We Need Them?

When multiple threads access shared data, you get **race conditions**:

```
Thread 1: Read balance = $100
Thread 2: Read balance = $100
Thread 1: Add $50, write balance = $150
Thread 2: Add $30, write balance = $130

Result: $130 (should be $180!)
```

**Solution**: Use a lock to protect the shared data.

## How Locks Work

### Visual
```
Thread 1              Lock              Thread 2
   |                   |                   |
   |-- acquire() ----->|                   |
   |                 [LOCKED]              |
   | (access data)     |                   |
   |                   |    <-- acquire()--|
   |                   |                (blocked)
   | (modify data)     |                (waiting...)
   |                   |                (waiting...)
   |-- release() ----->|                   |
   |                [UNLOCKED]             |
   |                   |-- acquired ------>|
   |                 [LOCKED]              |
   |                   |    (access data)  |
```

### Basic Pattern
```
lock = Mutex()

lock.acquire()
// Critical section - only one thread at a time
shared_data = shared_data + 1
lock.release()
```

### Better Pattern (Auto-Release)
```
lock = Mutex()

with lock:  // Automatically releases even if error occurs
    shared_data = shared_data + 1
```

## Example: Bank Account

### Without Lock (BROKEN)
```
balance = 1000

function withdraw(amount):
    temp = balance
    sleep(0.001)  // Simulates delay
    balance = temp - amount

Thread 1: withdraw(100)  // balance = 900
Thread 2: withdraw(50)   // balance = 950 (wrong!)
```

### With Lock (CORRECT)
```
balance = 1000
lock = Mutex()

function withdraw(amount):
    lock.acquire()
    temp = balance
    sleep(0.001)
    balance = temp - amount
    lock.release()

Thread 1: withdraw(100)
Thread 2: withdraw(50)
// Result: balance = 850 (correct!)
```

## Types of Locks

### 1. Mutex (Basic Lock)
- Binary: Either locked or unlocked
- Only one thread can hold it
- Most common type

### 2. Reentrant Lock (Recursive Mutex)
Same thread can acquire it multiple times:
```
lock.acquire()
    function_that_also_acquires_lock()
    lock.acquire()  // OK! Same thread
    lock.release()
lock.release()
```

### 3. Read-Write Lock
- Multiple readers OR one writer
- Readers don't block each other
- Writer blocks everyone

```
rw_lock = ReadWriteLock()

// Multiple readers can read simultaneously
rw_lock.read_lock()
data = read_shared_data()
rw_lock.read_unlock()

// Writer has exclusive access
rw_lock.write_lock()
write_shared_data(new_data)
rw_lock.write_unlock()
```

### 4. Spin Lock
Thread actively waits (spins) instead of sleeping:
```
while not try_acquire_lock():
    // Keep trying (wastes CPU)
```
Use only for very short critical sections.

## Critical Section

The code between `acquire()` and `release()`:

```
lock.acquire()
// ← Critical section starts
x = shared_data
x = x + 1
shared_data = x
// ← Critical section ends
lock.release()
```

**Rules**:
- Keep it **short** - other threads are waiting
- No **blocking operations** (I/O, network) inside
- Always **release** the lock

## Common Patterns

### Pattern 1: Protecting Shared Data
```
class Counter:
    def __init__(self):
        self.value = 0
        self.lock = Mutex()

    def increment(self):
        with self.lock:
            self.value += 1

    def get(self):
        with self.lock:
            return self.value
```

### Pattern 2: Protecting Multiple Related Operations
```
with lock:
    if queue.is_empty():
        queue.add(item)
        notify_workers()
```

### Pattern 3: Lock Ordering (Prevent Deadlock)
```
// Always acquire locks in same order
lock_a.acquire()
lock_b.acquire()
// Do work
lock_b.release()
lock_a.release()
```

## Best Practices

✅ **Keep critical sections short** - Less time holding lock = better performance

✅ **Always release locks** - Use try/finally or with-statement

✅ **Consistent lock ordering** - Prevents deadlocks

✅ **One lock per data structure** - Simple and clear

✅ **Document what lock protects** - Help future readers

## Common Pitfalls

⚠️ **Forgetting to release**: Lock stays locked forever
```
lock.acquire()
if error:
    return  // Oops! Didn't release
lock.release()
```
**Solution**: Use try/finally or with-statement

⚠️ **Deadlock**: Two threads wait for each other's locks
```
Thread 1: lock_A.acquire(), then lock_B.acquire()
Thread 2: lock_B.acquire(), then lock_A.acquire()
// Both stuck!
```
**Solution**: Always acquire locks in same order

⚠️ **Too much locking**: Performance suffers
```
with lock:
    result = slow_database_query()  // Don't do this!
```
**Solution**: Do slow operations outside critical section

⚠️ **Lock too coarse**: Everything uses one lock
```
with global_lock:  // Blocks all operations!
    do_anything()
```
**Solution**: Use finer-grained locks for independent data

⚠️ **Lock too fine**: Too many locks, complexity increases
```
// Too much!
lock_for_each_variable.acquire()
```
**Solution**: Balance between too coarse and too fine

## Deadlock Example

```
Thread 1:
    lock_A.acquire()
    lock_B.acquire()  // Waits for Thread 2

Thread 2:
    lock_B.acquire()
    lock_A.acquire()  // Waits for Thread 1

Both threads stuck forever!
```

**Fix**: Lock ordering
```
// Both threads acquire in same order
Thread 1 & 2:
    lock_A.acquire()  // Always A first
    lock_B.acquire()  // Then B
```

## Performance Considerations

**Lock contention**: Many threads waiting for same lock
- Symptom: Threads spend more time waiting than working
- Solutions:
  - Reduce critical section size
  - Use finer-grained locks
  - Lock-free data structures
  - Different algorithm

## Alternatives to Locks

1. **Lock-free algorithms** - Use atomic operations
2. **Immutable data** - No mutation = no locks needed
3. **Thread-local storage** - Each thread has own copy
4. **Actor model** - Message passing instead of sharing

## Related Topics

- [Race Conditions](race-conditions.md) - What locks prevent
- [Deadlocks](deadlocks.md) - Common lock problem
- [Thread Safety](thread-safety.md) - Making code safe for threads
- [Atomic Operations](atomic-operations.md) - Lock-free alternative
- [Semaphores](semaphores.md) - Generalization of locks
