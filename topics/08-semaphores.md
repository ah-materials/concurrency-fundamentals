# Semaphores

## What Is a Semaphore?

A counter that controls access to a shared resource by multiple threads.

Think of it as a bouncer at a club with a limited capacity.

## Mutex vs Semaphore

**Mutex**: Binary lock (locked/unlocked)
- Only allows 1 thread

**Semaphore**: Counter (can allow N threads)
- Allows up to N threads

## Types

### Binary Semaphore (0 or 1)
Same as mutex - only 1 thread can access:
```
semaphore(1)  // Max 1 thread
```

### Counting Semaphore (0 to N)
Multiple threads up to limit:
```
semaphore(5)  // Max 5 threads
```

## How It Works

```
Semaphore with count = 3

Thread 1: wait() → count = 2 → allowed in
Thread 2: wait() → count = 1 → allowed in  
Thread 3: wait() → count = 0 → allowed in
Thread 4: wait() → count = 0 → BLOCKED (waits)

Thread 1: signal() → count = 1
Thread 4: → unblocked, count = 0 → allowed in
```

## Operations

### wait() / P() / acquire()
Decrease counter, block if zero:
```
semaphore.wait()
// Access resource
```

### signal() / V() / release()
Increase counter, wake waiting thread:
```
// Done with resource
semaphore.signal()
```

## Example: Limiting Concurrent Access

### Database Connection Pool
```
// Only 10 database connections available
db_semaphore = Semaphore(10)

function query_database():
    db_semaphore.wait()  // Wait for available connection
    connection = get_connection()
    result = connection.query(sql)
    release_connection(connection)
    db_semaphore.signal()  // Release for others
    return result
```

### Rate Limiting
```
// Max 5 concurrent API requests
api_semaphore = Semaphore(5)

function call_api():
    api_semaphore.wait()
    response = http.get(url)
    api_semaphore.signal()
    return response
```

## Common Use Cases

1. **Resource pools**: Limit concurrent access (DB connections, file handles)
2. **Rate limiting**: Control request rate
3. **Producer-consumer**: Signal when items available
4. **Parking lot**: N parking spaces for cars

## Producer-Consumer with Semaphores

```
queue = []
empty_slots = Semaphore(10)  // Queue capacity
filled_slots = Semaphore(0)   // Items in queue

function producer():
    while true:
        item = produce()
        empty_slots.wait()  // Wait for empty slot
        queue.append(item)
        filled_slots.signal()  // Signal item available

function consumer():
    while true:
        filled_slots.wait()  // Wait for item
        item = queue.pop()
        empty_slots.signal()  // Signal slot freed
        consume(item)
```

## Best Practices

✅ **Match wait() with signal()** - Every wait needs corresponding signal

✅ **Initialize correctly** - Count = max concurrent access allowed

✅ **Use for resource limiting** - When you need to limit count, not just binary lock

## Common Pitfalls

⚠️ **Not calling signal()**: Counter never increases, threads blocked forever

⚠️ **Wrong initial count**: Too high = over-subscription, too low = under-utilization

⚠️ **Using when mutex sufficient**: If you only need 1 thread, use mutex (simpler)

## When to Use Semaphore vs Mutex

**Use Mutex when:**
- Need to protect shared data
- Only 1 thread should access at a time
- Simple binary lock

**Use Semaphore when:**
- Limiting concurrent access to resource pool
- Need to allow N threads (N > 1)
- Signaling between threads

## Related Topics

- [Locks and Mutexes](locks-and-mutexes.md) - Binary version of semaphore
- [Thread Pools](thread-pools.md) - Often use semaphores internally
- [Jobs and Workers](jobs-and-workers.md) - Semaphores for resource limiting
