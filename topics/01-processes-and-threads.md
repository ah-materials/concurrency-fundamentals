# Processes and Threads

## What Are They?

**Process**: A running instance of a program. Has its own memory space.
- When you run a program, the OS creates a process for it
- Each process is isolated from others
- Has its own memory, resources, and execution state

**Thread**: A unit of execution within a process. Shares memory with other threads in the same process.
- Lightweight compared to processes
- Multiple threads can run within one process
- Share the same memory space (can access same variables)

## Simple Analogy

**Process** = A restaurant
- Has its own kitchen, supplies, staff
- Completely separate from other restaurants

**Thread** = A chef in that restaurant
- Multiple chefs (threads) work in the same kitchen (process)
- They share the same ingredients and tools (memory)
- But they can work on different dishes (tasks) simultaneously

## Visual

```
Process A                    Process B
┌─────────────────┐         ┌─────────────────┐
│  Memory Space   │         │  Memory Space   │
│                 │         │                 │
│  Thread 1       │         │  Thread 1       │
│  Thread 2       │         │  Thread 2       │
│  Thread 3       │         │                 │
└─────────────────┘         └─────────────────┘
```

## Key Differences

| Aspect | Process | Thread |
|--------|---------|--------|
| Memory | Separate | Shared |
| Creation Cost | Expensive | Cheap |
| Communication | Complex (IPC needed) | Easy (shared memory) |
| Crash Impact | Isolated | Can crash entire process |

## When to Use

**Use separate processes when:**
- You need isolation (security, stability)
- Running completely independent programs
- Want to avoid one failure crashing everything

**Use threads when:**
- Tasks need to share data frequently
- Want lightweight concurrency
- Doing parallel work within same application

## Common Pitfalls

⚠️ **With threads**: Shared memory means you need synchronization (locks) to avoid race conditions

⚠️ **With processes**: Communication overhead is higher - don't use for tasks that need frequent data sharing

## Related Topics

- [Multithreading](multithreading.md) - Using multiple threads
- [Thread Safety](thread-safety.md) - Protecting shared data
