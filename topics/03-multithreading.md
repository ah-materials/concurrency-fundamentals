# Multithreading

## What Is It?

Running multiple threads within a single process to execute tasks concurrently or in parallel.

## Why Use It?

1. **Better resource utilization** - Keep CPU busy while waiting for I/O
2. **Responsiveness** - UI stays responsive while doing background work
3. **Performance** - Utilize multiple CPU cores for CPU-intensive tasks
4. **Simplify program structure** - Separate concerns into different threads

## Simple Example

```
Main Thread: Handle user interface
Worker Thread 1: Process uploaded file
Worker Thread 2: Fetch data from database
Worker Thread 3: Send email notifications
```

Each thread runs independently, but they all share the same process memory.

## Visual

```
Process
┌────────────────────────────────┐
│  Shared Memory                 │
│  ┌─────────────────────┐       │
│  │ Shared Data         │       │
│  │ Variables, Objects  │       │
│  └─────────────────────┘       │
│                                │
│  Thread 1    Thread 2   Thread 3
│     ↓            ↓          ↓
│  [Task A]    [Task B]   [Task C]
│                                │
└────────────────────────────────┘
```

## When to Use

**Good use cases:**
- Web server handling multiple requests
- GUI application with background processing
- Downloading multiple files simultaneously
- Parallel data processing

**Bad use cases:**
- CPU-bound task on single-core machine (no speedup, just overhead)
- When tasks need to run strictly in order
- Simple sequential operations

## Common Patterns

### 1. Worker Threads
Main thread delegates work to background threads:
```
Main Thread: Receive requests
Worker Threads: Process each request
```

### 2. Thread Pool
Reuse a fixed number of threads for many tasks:
```
Pool of 10 threads
Queue of 1000 tasks
Threads pick up tasks from queue as they finish
```

### 3. Producer-Consumer
Some threads produce data, others consume it:
```
Producer Thread: Read files → add to queue
Consumer Threads: Take from queue → process data
```

## Key Challenges

### 1. Race Conditions
Multiple threads accessing shared data without coordination:
```
Thread 1: Read counter = 5, add 1, write 6
Thread 2: Read counter = 5, add 1, write 6
Result: counter = 6 (should be 7!)
```
**Solution**: Use locks/synchronization

### 2. Deadlocks
Threads waiting for each other forever:
```
Thread 1: Holds Lock A, waits for Lock B
Thread 2: Holds Lock B, waits for Lock A
Both stuck!
```
**Solution**: Acquire locks in consistent order

### 3. Thread Safety
Ensuring shared data isn't corrupted by concurrent access.
**Solution**: Use thread-safe data structures, locks, or avoid sharing

## Thread Lifecycle

```
[Created] → [Ready] → [Running] → [Terminated]
                ↑         ↓
                └─[Waiting/Blocked]
```

- **Created**: Thread is created but not started
- **Ready**: Waiting for CPU time
- **Running**: Actively executing
- **Waiting/Blocked**: Waiting for resource/condition
- **Terminated**: Finished execution

## Best Practices

✅ **Keep shared data minimal** - Less sharing = fewer problems

✅ **Use thread-safe data structures** - When available in your language

✅ **Limit number of threads** - Too many threads = overhead and complexity

✅ **Use thread pools** - Reuse threads instead of creating new ones

✅ **Protect critical sections** - Use locks for shared data access

## Common Pitfalls

⚠️ **Too many threads**: Creating 1000 threads doesn't make things 1000x faster

⚠️ **Forgetting synchronization**: Shared data without locks = race conditions

⚠️ **Holding locks too long**: Other threads wait, performance suffers

⚠️ **Not handling exceptions**: Uncaught exception in thread can crash your program

## Related Topics

- [Processes and Threads](01-processes-and-threads.md) - Understanding threads
- [Thread Pools](12-thread-pools.md) - Managing multiple threads efficiently
- [Locks and Mutexes](07-locks-and-mutexes.md) - Protecting shared data
- [Race Conditions](04-race-conditions.md) - Common problem with multithreading
- [Deadlocks](05-deadlocks.md) - When threads get stuck
- [Thread Safety](06-thread-safety.md) - Writing safe concurrent code
