# Atomic Operations

## What Is an Atomic Operation?

An operation that completes in a single, uninterruptible step.

"Atomic" = indivisible - cannot be broken down or interrupted.

## Why Do We Need Them?

Normal operations aren't atomic:
```
x = x + 1

Actually 3 steps:
1. Read x
2. Add 1
3. Write result

Thread can be interrupted between steps!
```

## Atomic Operations

Cannot be interrupted - happen "all at once":
```
atomic_add(x, 1)  // Single uninterruptible operation
```

## Common Atomic Operations

### Atomic Read/Write
```
value = atomic_load(variable)
atomic_store(variable, new_value)
```

### Atomic Arithmetic
```
atomic_add(counter, 1)      // counter += 1
atomic_sub(counter, 1)      // counter -= 1
atomic_increment(counter)   // counter++
atomic_decrement(counter)   // counter--
```

### Compare-and-Swap (CAS)
```
atomic_compare_and_swap(variable, expected, new_value)

// Only updates if variable == expected
// Returns true if successful
```

### Test-and-Set
```
old_value = atomic_test_and_set(flag)
// Sets flag to true, returns old value
```

## Example: Thread-Safe Counter

### Without Atomic (BROKEN)
```
counter = 0

function increment():
    counter = counter + 1  // Race condition!

// 100 threads each call increment()
// Result: counter < 100 (some updates lost)
```

### With Atomic (CORRECT)
```
counter = AtomicInteger(0)

function increment():
    atomic_add(counter, 1)  // Safe!

// 100 threads each call increment()
// Result: counter == 100 (all updates counted)
```

## Compare-and-Swap Example

### Lock-Free Increment
```
function atomic_increment(atomic_var):
    while true:
        current = atomic_load(atomic_var)
        new_value = current + 1
        if atomic_compare_and_swap(atomic_var, current, new_value):
            return new_value  // Success!
        // CAS failed, retry (someone else changed it)
```

## Benefits

✅ **No locks needed** - Avoid lock overhead

✅ **No deadlocks** - No locks = no deadlocks

✅ **Better performance** - Faster than locks for simple operations

✅ **Wait-free** - Threads never block

## Limitations

⚠️ **Simple operations only** - Works for counters, flags, simple updates

⚠️ **Not for complex data** - Can't atomically update multiple variables

⚠️ **Hardware-dependent** - Not all types supported on all platforms

⚠️ **ABA problem** - Value changes A→B→A, looks unchanged

## When to Use

**Use atomic operations when:**
- Simple counter/flag
- Single variable updates
- Performance critical
- Want lock-free code

**Use locks when:**
- Protecting complex data structures
- Multiple variables must update together
- Simpler code is more important than max performance

## Language Support

### C++ (C++11+)
```cpp
std::atomic<int> counter(0);
counter.fetch_add(1);
counter.store(10);
int value = counter.load();
```

### Java
```java
AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet();
counter.addAndGet(5);
int value = counter.get();
```

### Python
```python
# No built-in atomics, use locks or:
import threading
counter = 0
lock = threading.Lock()

with lock:
    counter += 1
```

### Go
```go
import "sync/atomic"

var counter int64
atomic.AddInt64(&counter, 1)
atomic.LoadInt64(&counter)
```

## Memory Ordering

Atomics also control memory ordering:
- **Relaxed**: No ordering guarantees
- **Acquire/Release**: Synchronize with other threads
- **Sequential**: Strongest guarantees

Most of the time, use default (sequential consistency).

## Related Topics

- [Locks and Mutexes](07-locks-and-mutexes.md) - Traditional synchronization
- [Race Conditions](04-race-conditions.md) - What atomics prevent
- [Thread Safety](06-thread-safety.md) - Making code thread-safe
