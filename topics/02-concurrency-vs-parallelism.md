# Concurrency vs Parallelism

## The Difference

**Concurrency**: Dealing with multiple things at once (managing multiple tasks)
**Parallelism**: Doing multiple things at once (executing multiple tasks simultaneously)

## Simple Explanation

**Concurrency** is about **structure** - how you organize your code to handle multiple tasks.
**Parallelism** is about **execution** - actually running multiple tasks at the same time.

## Analogy

**Concurrency**: You're a chef managing multiple dishes
- You chop vegetables for dish A
- Put dish B in the oven
- Stir sauce for dish C
- Check on dish B
- You're handling multiple tasks, but only doing one thing at a time

**Parallelism**: You have 3 chefs each working on a different dish simultaneously
- All dishes are being prepared at the exact same time
- Requires multiple workers (CPU cores)

## Visual

### Concurrent (Single Core)
```
Time →
Task A: ███░░░░░███░░░░
Task B: ░░░███░░░░░░███
Task C: ░░░░░░███░░░░░░

One CPU switches between tasks
```

### Parallel (Multiple Cores)
```
Time →
CPU 1 - Task A: ████████████
CPU 2 - Task B: ████████████
CPU 3 - Task C: ████████████

All tasks run simultaneously
```

## Key Points

1. **Concurrency** doesn't require multiple CPUs - it's about better organizing work
2. **Parallelism** requires multiple CPUs/cores to truly run things simultaneously
3. You can have concurrency without parallelism (single core, switching between tasks)
4. You can have parallelism without concurrency (just running one thing per core)

## Examples

**Concurrent but not parallel:**
- Single-core CPU running multiple threads (switches between them quickly)
- Event loop in JavaScript (handles many async operations, but one at a time)

**Parallel:**
- Video encoding using all CPU cores
- Database query running on multiple nodes
- Multiple threads on a multi-core processor

**Both concurrent AND parallel:**
- Web server with thread pool on multi-core machine
- Distributed system processing multiple requests across machines

## When Does It Matter?

**Focus on concurrency when:**
- Your program waits a lot (I/O, network, user input)
- You want to keep UI responsive while doing background work
- Managing many connections/requests

**Focus on parallelism when:**
- You have CPU-intensive calculations
- Want to speed up computation using multiple cores
- Processing large amounts of data

## Common Pitfalls

⚠️ **Don't confuse them**: Using more threads doesn't always make things faster. On a single core, threads just add overhead.

⚠️ **I/O-bound vs CPU-bound**:
- I/O-bound tasks (network, files): Concurrency helps
- CPU-bound tasks (calculations): Need parallelism for speedup

## Related Topics

- [Multithreading](03-multithreading.md) - Can be concurrent, parallel, or both
- [Thread Pools](12-thread-pools.md) - Managing concurrent/parallel execution
