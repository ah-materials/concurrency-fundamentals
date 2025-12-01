# Thread Safety

## What Is Thread Safety?

Code that works correctly when accessed by multiple threads simultaneously.

"Thread-safe" = No race conditions, no data corruption, predictable behavior.

## Thread-Safe vs Not Thread-Safe

### Not Thread-Safe
```
balance = 100

function withdraw(amount):
    if balance >= amount:
        balance = balance - amount  // UNSAFE!
        return true
    return false

// Two threads can both withdraw, overdraw account
```

### Thread-Safe
```
balance = 100
lock = Mutex()

function withdraw(amount):
    with lock:
        if balance >= amount:
            balance = balance - amount
            return true
        return false

// Only one thread at a time - SAFE!
```

## How to Make Code Thread-Safe

### Method 1: Synchronization (Locks)
Protect shared data with locks:
```
lock = Mutex()

with lock:
    shared_data.update()
```

### Method 2: Atomic Operations
Use atomic operations for simple updates:
```
atomic_counter.increment()
```

### Method 3: Immutability
Make data immutable - can't change = no races:
```
old_config = get_config()
new_config = old_config.copy()
new_config.update(changes)
atomic_swap(config_ref, new_config)
```

### Method 4: Thread-Local Storage
Each thread has its own copy:
```
thread_local_data = get_thread_local()
thread_local_data.update()  // No sharing, no problem
```

### Method 5: Thread Confinement
Only one thread ever accesses the data:
```
// UI thread only
ui_state.update()
```

## Thread-Safe Data Structures

### Instead of Regular Queue
```
// NOT thread-safe
queue.append(item)

// Thread-safe queue
thread_safe_queue.put(item)  // Locks internally
```

### Common Thread-Safe Structures
- **Java**: `ConcurrentHashMap`, `ConcurrentLinkedQueue`, `AtomicInteger`
- **Python**: `queue.Queue`, `threading.Lock`
- **C++**: `std::atomic`, lock-based containers
- **Go**: Channels, `sync.Mutex`, `sync.Map`

## Levels of Thread Safety

### 1. Immutable
Cannot be modified - always thread-safe:
```
final_value = 42  // Can't change, always safe
```

### 2. Thread-Safe
Safe for concurrent use:
```
thread_safe_counter.increment()
```

### 3. Conditionally Thread-Safe
Safe if used correctly:
```
// Safe if you use the lock
with lock:
    data.update()
```

### 4. Not Thread-Safe
Requires external synchronization:
```
// Document: "Not thread-safe, use locks"
unsafe_list.append(item)
```

## Common Patterns

### Pattern 1: Per-Object Lock
Each object has its own lock:
```
class BankAccount:
    def __init__(self):
        self.balance = 0
        self.lock = Mutex()
    
    def withdraw(self, amount):
        with self.lock:
            if self.balance >= amount:
                self.balance -= amount
```

### Pattern 2: Read-Write Lock
Multiple readers, one writer:
```
rw_lock = ReadWriteLock()

# Read operation
with rw_lock.read():
    value = data.read()

# Write operation
with rw_lock.write():
    data.update()
```

### Pattern 3: Copy-on-Write
Copy before modifying:
```
# Multiple threads can read
current_data = shared_data

# To modify: copy, update, replace
new_data = current_data.copy()
new_data.update()
atomic_replace(shared_data, new_data)
```

## Signs of Thread-Unsafe Code

⚠️ Shared mutable state without locks
⚠️ Check-then-act without synchronization
⚠️ Multiple related variables updated separately
⚠️ Using non-thread-safe data structures from multiple threads

## Making Classes Thread-Safe

### Bad: Shared State
```
class Counter:
    def __init__(self):
        self.count = 0  // UNSAFE!
    
    def increment(self):
        self.count += 1  // Race condition!
```

### Good: Synchronized
```
class Counter:
    def __init__(self):
        self.count = 0
        self.lock = Mutex()
    
    def increment(self):
        with self.lock:
            self.count += 1
```

### Better: Atomic
```
class Counter:
    def __init__(self):
        self.count = AtomicInteger(0)
    
    def increment(self):
        self.count.increment()  // Atomic, no lock needed
```

## Testing for Thread Safety

```
# Stress test
def test_thread_safety():
    counter = Counter()
    threads = []
    
    for i in range(100):
        t = Thread(target=lambda: counter.increment())
        threads.append(t)
        t.start()
    
    for t in threads:
        t.join()
    
    assert counter.value == 100  // If not 100, not thread-safe!
```

## Best Practices

✅ **Minimize shared state** - Less sharing = fewer problems

✅ **Use thread-safe data structures** - Let library handle it

✅ **Document thread-safety** - Tell users if class is thread-safe

✅ **Keep critical sections small** - Lock for minimal time

✅ **Use higher-level abstractions** - Queues, thread pools, actors

✅ **Prefer immutability** - Immutable = always thread-safe

## Common Pitfalls

⚠️ **Assuming operations are atomic**: `x++` is not atomic!

⚠️ **Partial synchronization**: Protecting some but not all access

⚠️ **Publishing object before fully initialized**: Other threads see partial state

⚠️ **Using mutable objects as keys**: Can change while in HashMap

## Quick Checklist

Is your code thread-safe?
- [ ] No shared mutable state? Or...
- [ ] All shared state protected by locks?
- [ ] Using thread-safe data structures?
- [ ] No check-then-act without synchronization?
- [ ] Tested with multiple threads?

## Related Topics

- [Race Conditions](race-conditions.md) - What thread-unsafe code causes
- [Locks and Mutexes](locks-and-mutexes.md) - How to protect shared data
- [Atomic Operations](atomic-operations.md) - Lock-free thread safety
- [Deadlocks](deadlocks.md) - Problem when using locks
