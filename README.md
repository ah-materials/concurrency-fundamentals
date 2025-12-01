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

## Suggested Learning Path

Follow this sequential order to build your understanding from fundamentals to advanced concepts:

### 1. Foundation (Start Here)
1. [Processes and Threads](topics/01-processes-and-threads.md) - Understand the basic execution units
2. [Concurrency vs Parallelism](topics/02-concurrency-vs-parallelism.md) - Learn the key distinction
3. [Multithreading](topics/03-multithreading.md) - Running multiple threads together

### 2. Common Problems (Learn What Can Go Wrong)
4. [Race Conditions](topics/04-race-conditions.md) - When threads compete for resources
5. [Deadlocks](topics/05-deadlocks.md) - When threads block each other
6. [Thread Safety](topics/06-thread-safety.md) - Writing code that works with multiple threads

### 3. Synchronization Tools (How to Fix Problems)
7. [Locks and Mutexes](topics/07-locks-and-mutexes.md) - Basic synchronization primitives
8. [Semaphores](topics/08-semaphores.md) - Controlling access to limited resources
9. [Atomic Operations](topics/09-atomic-operations.md) - Lock-free synchronization

### 4. Data Structures & Patterns
10. [Queues and Stacks](topics/10-queues-and-stacks.md) - Essential concurrent data structures

### 5. Task Processing Patterns
11. [Jobs and Workers](topics/11-jobs-and-workers.md) - The worker pattern
12. [Thread Pools](topics/12-thread-pools.md) - Reusing threads efficiently
13. [Task Scheduling](topics/13-task-scheduling.md) - Timing and prioritizing work

### 6. Background Processing (Production Patterns)
14. [Background Processes](topics/14-background-processes.md) - Running work in the background
15. [Daemons](topics/15-daemons.md) - Long-running background processes
16. [Services](topics/16-services.md) - System-level background services

## Topics

### Core Concepts
- [Processes and Threads](topics/01-processes-and-threads.md)
- [Concurrency vs Parallelism](topics/02-concurrency-vs-parallelism.md)
- [Multithreading](topics/03-multithreading.md)

### Task Processing
- [Jobs and Workers](topics/11-jobs-and-workers.md)
- [Thread Pools](topics/12-thread-pools.md)
- [Queues and Stacks](topics/10-queues-and-stacks.md)
- [Task Scheduling](topics/13-task-scheduling.md)

### Background Processing
- [Background Processes](topics/14-background-processes.md)
- [Daemons](topics/15-daemons.md)
- [Services](topics/16-services.md)

### Synchronization
- [Locks and Mutexes](topics/07-locks-and-mutexes.md)
- [Semaphores](topics/08-semaphores.md)
- [Atomic Operations](topics/09-atomic-operations.md)

### Common Issues
- [Race Conditions](topics/04-race-conditions.md)
- [Deadlocks](topics/05-deadlocks.md)
- [Thread Safety](topics/06-thread-safety.md)

## Quick Reference

**Need to process tasks in the background?**
→ [Background Processes](topics/14-background-processes.md), [Thread Pools](topics/12-thread-pools.md)

**Multiple threads accessing shared data?**
→ [Locks and Mutexes](topics/07-locks-and-mutexes.md), [Thread Safety](topics/06-thread-safety.md)

**Managing a queue of work?**
→ [Jobs and Workers](topics/11-jobs-and-workers.md), [Queues](topics/10-queues-and-stacks.md)

**Long-running background service?**
→ [Daemons](topics/15-daemons.md), [Services](topics/16-services.md)

## Format

Each topic includes:
- Clear explanation of what it is
- When to use it
- Simple examples (language-agnostic)
- Common pitfalls to avoid

---

**Note**: These are language-agnostic concepts. The principles apply whether you're using Python, JavaScript, Go, Java, or any other language.
