# Queues and Stacks

## What Are They?

Data structures for organizing tasks/items to be processed.

**Queue (FIFO)**: First-In-First-Out - like a line at a store
**Stack (LIFO)**: Last-In-First-Out - like a stack of plates

## Queue

### Visual
```
  Add →                        → Remove
┌─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  3  │  4  │  5  │
└─────┴─────┴─────┴─────┴─────┘
Front                      Back

First item added is first item removed
```

### When to Use
- Task processing (job queues)
- Message passing between threads
- Request handling
- Breadth-first algorithms

### Common in Concurrency
```
Producer Thread:    Add jobs to queue →  [Queue]
Consumer Threads:   ← Take jobs from queue
```

## Stack

### Visual
```
     Add/Remove
          ↕
      ┌─────┐
      │  5  │ ← Top (most recent)
      ├─────┤
      │  4  │
      ├─────┤
      │  3  │
      ├─────┤
      │  2  │
      ├─────┤
      │  1  │ ← Bottom (oldest)
      └─────┘

Last item added is first item removed
```

### When to Use
- Function call stack
- Undo functionality
- Depth-first algorithms
- Backtracking problems

### Less Common in Concurrency
Queues are more common for task processing because FIFO is usually more fair.

## Queue Types

### 1. Simple Queue (FIFO)
```
add(A) → add(B) → add(C)
Queue: [A, B, C]
remove() → A
remove() → B
```

### 2. Priority Queue
Items with higher priority come out first:
```
add(task, priority=5)
add(task, priority=10)  ← Higher priority
add(task, priority=1)

remove() → returns priority=10 task
```

### 3. Circular/Ring Buffer
Fixed-size queue that wraps around:
```
┌─────┬─────┬─────┬─────┬─────┐
│  A  │  B  │     │     │     │  Capacity: 5
└─────┴─────┴─────┴─────┴─────┘
  ↑                           ↑
Front                        Back

When full, adding new item overwrites oldest
```

### 4. Blocking Queue
Thread-safe queue that blocks when empty/full:
```
Consumer thread:
    item = queue.take()  // Blocks if queue empty

Producer thread:
    queue.put(item)  // Blocks if queue full
```

## Common Operations

### Queue
- `enqueue(item)` / `add(item)` - Add to back
- `dequeue()` / `remove()` - Remove from front
- `peek()` - Look at front without removing
- `is_empty()` - Check if empty
- `size()` - Number of items

### Stack
- `push(item)` - Add to top
- `pop()` - Remove from top
- `peek()` / `top()` - Look at top without removing
- `is_empty()` - Check if empty
- `size()` - Number of items

## In Practice

### Job Queue Example
```
job_queue = BlockingQueue(max_size=100)

// Producer
function submit_job(job):
    job_queue.add(job)  // Blocks if queue full

// Worker
function worker_loop():
    while running:
        job = job_queue.take()  // Blocks if queue empty
        process(job)
```

### Benefits of Blocking Queue
- Producers automatically slow down if queue is full
- Workers automatically wait if no work available
- No need for manual synchronization

## Thread-Safe vs Not Thread-Safe

### Not Thread-Safe
Standard queue/stack - unsafe for multiple threads:
```
Thread 1: queue.add(item1)
Thread 2: queue.add(item2)
// Can corrupt internal state!
```

### Thread-Safe
Uses locks internally to protect operations:
```
Thread 1: queue.add(item1)  // Locks internally
Thread 2: queue.add(item2)  // Waits for lock
// Safe!
```

Most languages provide thread-safe versions:
- Java: `ConcurrentLinkedQueue`, `BlockingQueue`
- Python: `queue.Queue`
- Go: Channels
- C++: `std::concurrent_queue` (C++11+)

## Common Pitfalls

⚠️ **Using non-thread-safe queue with multiple threads**
- Solution: Use thread-safe queue implementation

⚠️ **Unbounded queue fills memory**
- Solution: Set maximum size, reject when full

⚠️ **Not handling empty queue**
- Solution: Check `is_empty()` or use blocking queue

⚠️ **Queue as bottleneck**
- Solution: Multiple queues, or faster queue implementation

## Best Practices

✅ **Use blocking queues for producer-consumer** - Simplifies code

✅ **Set queue size limits** - Prevent memory issues

✅ **Monitor queue depth** - Alert if queue grows too large

✅ **Choose right queue type**:
- FIFO queue: Fair, predictable order
- Priority queue: Important tasks first
- Ring buffer: Fixed memory, recent items only

## Related Topics

- [Jobs and Workers](jobs-and-workers.md) - Using queues for task distribution
- [Thread Pools](thread-pools.md) - Often use queues internally
- [Thread Safety](thread-safety.md) - Why thread-safe queues matter
