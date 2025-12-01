# Concurrency Fundamentals

> Simple, practical notes on concurrent programming concepts

## What's Covered

Essential concurrency concepts you need to know:
- **Processes & Threads** - What executes your code
- **Multithreading** - Running multiple threads
- **Background Processing** - Daemons, services, background jobs
- **Task Processing** - Jobs, workers, queues
- **Data Structures** - Queues, stacks, and how they're used
- **Common Problems** - Deadlocks, race conditions, and how to avoid them
- **Synchronization** - Locks, semaphores, and coordination

## Topics

### Core Concepts
- [Processes and Threads](topics/processes-and-threads.md)
- [Concurrency vs Parallelism](topics/concurrency-vs-parallelism.md)
- [Multithreading](topics/multithreading.md)

### Task Processing
- [Jobs and Workers](topics/jobs-and-workers.md)
- [Thread Pools](topics/thread-pools.md)
- [Queues and Stacks](topics/queues-and-stacks.md)
- [Task Scheduling](topics/task-scheduling.md)

### Background Processing
- [Background Processes](topics/background-processes.md)
- [Daemons](topics/daemons.md)
- [Services](topics/services.md)

### Synchronization
- [Locks and Mutexes](topics/locks-and-mutexes.md)
- [Semaphores](topics/semaphores.md)
- [Atomic Operations](topics/atomic-operations.md)

### Common Issues
- [Race Conditions](topics/race-conditions.md)
- [Deadlocks](topics/deadlocks.md)
- [Thread Safety](topics/thread-safety.md)

## Quick Reference

**Need to process tasks in the background?**
→ [Background Processes](topics/background-processes.md), [Thread Pools](topics/thread-pools.md)

**Multiple threads accessing shared data?**
→ [Locks and Mutexes](topics/locks-and-mutexes.md), [Thread Safety](topics/thread-safety.md)

**Managing a queue of work?**
→ [Jobs and Workers](topics/jobs-and-workers.md), [Queues](topics/queues-and-stacks.md)

**Long-running background service?**
→ [Daemons](topics/daemons.md), [Services](topics/services.md)

## Format

Each topic includes:
- Clear explanation of what it is
- When to use it
- Simple examples (language-agnostic)
- Common pitfalls to avoid

---

**Note**: These are language-agnostic concepts. The principles apply whether you're using Python, JavaScript, Go, Java, or any other language.
