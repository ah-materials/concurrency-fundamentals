# Deadlocks

## What Is a Deadlock?

When two or more threads are stuck waiting for each other forever, and none can proceed.

## Classic Example: Dining Philosophers

Five philosophers sit at a table. Between each pair is one fork. Each philosopher needs two forks to eat.

```
   P1
  /  \
F1    F2
|      |
P5    P2
  \  /
  F5─F3
  /  \
P4    P3
```

If all philosophers pick up their left fork simultaneously, all wait forever for the right fork = deadlock!

## Simple Code Example

```
Thread 1:
    lock_A.acquire()
    lock_B.acquire()  // Waits for Thread 2
    // Do work
    lock_B.release()
    lock_A.acquire()

Thread 2:
    lock_B.acquire()
    lock_A.acquire()  // Waits for Thread 1
    // Do work
    lock_A.release()
    lock_B.release()

Both threads stuck forever!
```

## Four Conditions for Deadlock

ALL four must be true for deadlock to occur:

1. **Mutual Exclusion**: Resources can only be used by one thread at a time
2. **Hold and Wait**: Thread holds resources while waiting for others
3. **No Preemption**: Resources can't be forcibly taken away
4. **Circular Wait**: Chain of threads each waiting for a resource held by the next

Break any one condition → no deadlock!

## Visual Timeline

```
Time →

Thread 1: [Lock A] ──────── waiting for B ──→ (stuck)
                    ↘
Thread 2: ──────── [Lock B] waiting for A ──→ (stuck)
                              ↗
            Circular dependency = Deadlock
```

## Real-World Examples

### Example 1: Database Locks
```
Transaction 1:
    UPDATE accounts SET balance=... WHERE id=1;
    UPDATE accounts SET balance=... WHERE id=2;

Transaction 2:
    UPDATE accounts SET balance=... WHERE id=2;
    UPDATE accounts SET balance=... WHERE id=1;

Deadlock! Each holds a lock the other needs.
```

### Example 2: File Resources
```
Thread 1:
    file_a = open("a.txt", "write")
    file_b = open("b.txt", "write")  // Waits

Thread 2:
    file_b = open("b.txt", "write")
    file_a = open("a.txt", "write")  // Waits

Deadlock!
```

## How to Prevent Deadlocks

### Prevention 1: Lock Ordering
Always acquire locks in the same order:

```
// WRONG: Different order
Thread 1: lock_A → lock_B
Thread 2: lock_B → lock_A  // Deadlock!

// CORRECT: Same order
Thread 1: lock_A → lock_B
Thread 2: lock_A → lock_B  // No deadlock
```

### Prevention 2: Lock Timeout
Try to acquire lock with timeout:

```
if lock.try_acquire(timeout=1_second):
    // Got the lock
    do_work()
    lock.release()
else:
    // Couldn't get lock, back off
    retry_later()
```

### Prevention 3: Lock Hierarchy
Assign numbers to locks, always acquire in ascending order:

```
lock_1 (priority=1)
lock_2 (priority=2)
lock_3 (priority=3)

// Always acquire lower number first
acquire(lock_1)
acquire(lock_2)
acquire(lock_3)
```

### Prevention 4: Single Lock
Use one global lock for related operations:

```
with global_lock:
    // Access any resources
    // No deadlock possible (only one lock)
```

Downside: Less concurrency.

### Prevention 5: Try-Lock and Backoff
```
while true:
    if lock_A.try_acquire():
        if lock_B.try_acquire():
            // Got both locks
            do_work()
            lock_B.release()
            lock_A.release()
            break
        else:
            lock_A.release()  // Back off
            sleep(random_time())  // Wait and retry
    else:
        sleep(random_time())
```

### Prevention 6: Avoid Nested Locks
Don't acquire multiple locks if possible:

```
// Redesign to use only one lock at a time
with lock_A:
    work_on_A()

with lock_B:
    work_on_B()
```

## How to Detect Deadlocks

### Detection 1: Resource Allocation Graph
Track what locks each thread holds and wants:

```
T1 → holds → L1
T1 → wants → L2
T2 → holds → L2
T2 → wants → L1

Cycle detected! Deadlock!
```

### Detection 2: Timeouts
If operation takes too long, assume deadlock:

```
result = operation_with_timeout(10_seconds)
if result == TIMEOUT:
    log("Possible deadlock detected")
    handle_deadlock()
```

### Detection 3: Thread Dumps
Look at thread states:

```
Thread 1: BLOCKED on lock_B (held by Thread 2)
Thread 2: BLOCKED on lock_A (held by Thread 1)

Deadlock!
```

## How to Recover from Deadlocks

### Recovery 1: Kill One Thread
```
if deadlock_detected():
    victim = choose_victim_thread()
    victim.abort()  // Frees its locks
    victim.retry_later()
```

### Recovery 2: Rollback
```
if deadlock_detected():
    rollback_transaction()
    release_all_locks()
    retry_after_delay()
```

### Recovery 3: Preemption
Forcibly take lock from one thread:

```
if deadlock_detected():
    force_release(some_lock)
    // One thread can proceed
```

Rarely possible - usually can't safely take locks away.

## Deadlock vs Livelock

**Deadlock**: Threads stuck, not doing anything
```
T1: Waiting...
T2: Waiting...
(Both stuck forever)
```

**Livelock**: Threads active but making no progress
```
T1: Try lock → fail → retry
T2: Try lock → fail → retry
(Both keep trying, never succeeding)
```

## Best Practices

✅ **Acquire locks in consistent order** - Most important!

✅ **Hold locks for minimal time** - Less chance of conflict

✅ **Use timeouts** - Detect and recover from deadlocks

✅ **Avoid nested locking** when possible

✅ **Document lock ordering** - Help other developers

✅ **Use higher-level constructs** - Let libraries handle locking

## Common Pitfalls

⚠️ **Inconsistent lock ordering** across different code paths

⚠️ **Hidden locks**: Calling functions that acquire locks

⚠️ **Dynamic lock ordering**: Can't guarantee same order

⚠️ **Not releasing locks**: On error paths

## Testing for Deadlocks

```
// Stress test with intentional contention
threads = []
for i in range(100):
    thread = create_thread(contention_heavy_work)
    threads.append(thread)

for thread in threads:
    thread.join(timeout=30_seconds)
    if thread.is_alive():
        print("Deadlock detected!")
```

## Tools

- **Thread analyzers**: Helgrind, ThreadSanitizer
- **Debuggers**: Show thread states and lock ownership
- **Profilers**: Detect threads waiting unusually long
- **Logging**: Track lock acquisition order

## Real Fix for Dining Philosophers

```
// Solution 1: Lock ordering
philosophers[0..3]: pick_up(left), then pick_up(right)
philosophers[4]:    pick_up(right), then pick_up(left)

// Solution 2: Resource hierarchy
Always pick up lower-numbered fork first

// Solution 3: Limit diners
Only allow 4 of 5 to dine at once
(Guarantees at least one can get both forks)
```

## Related Topics

- [Locks and Mutexes](locks-and-mutexes.md) - Locks can cause deadlocks
- [Race Conditions](race-conditions.md) - Different concurrency problem
- [Thread Safety](thread-safety.md) - Writing safe concurrent code
