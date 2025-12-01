# Thread Pools

## What Is It?

A collection of reusable threads that execute tasks from a queue.

Instead of creating a new thread for each task (expensive), you create a pool of threads once and reuse them.

## Why Use Thread Pools?

1. **Performance**: Creating/destroying threads is expensive
2. **Resource control**: Limit number of concurrent threads
3. **Simplicity**: Don't manage individual threads yourself

## Visual

```
                Thread Pool
            ┌───────────────────┐
Task Queue  │   Thread 1        │ → Executes tasks
            │   Thread 2        │ → Executes tasks
  [Task A]→ │   Thread 3        │ → Executes tasks
  [Task B]→ │   Thread 4        │ → Executes tasks
  [Task C]→ │   Thread 5        │ → Executes tasks
  [Task D]  └───────────────────┘
  [Task E]     ↑
               └─ Threads grab tasks from queue
```

## How It Works

1. Create pool with N threads (e.g., 10 threads)
2. All threads wait for tasks
3. Submit task to pool → goes into queue
4. Next available thread picks up task
5. Thread executes task, then goes back to waiting
6. Thread is reused for next task

## Example Pseudocode

```
// Create pool with 5 threads
pool = ThreadPool(size=5)

// Submit tasks
pool.submit(task1)
pool.submit(task2)
pool.submit(task3)
pool.submit(task4)
pool.submit(task5)
pool.submit(task6)  // Waits in queue

// Shutdown when done
pool.shutdown()
pool.wait_for_completion()
```

## Pool Sizing

**How many threads should you have?**

### CPU-Bound Tasks (heavy computation)
```
pool_size = number_of_cpu_cores
```
More threads won't help - CPUs are already fully utilized.

### I/O-Bound Tasks (waiting for network, disk, database)
```
pool_size = number_of_cpu_cores * (1 + wait_time / compute_time)
```
Can have many more threads since they're mostly waiting.

**Example**:
- 4 CPU cores
- Task spends 90% time waiting (network), 10% computing
- Pool size = 4 * (1 + 9) = 40 threads

### Simple Rule of Thumb
- **CPU-intensive**: # of cores (e.g., 4-8)
- **I/O-intensive**: 2-4x # of cores (e.g., 16-32)
- **Mixed**: Start with 2x # of cores, adjust based on testing

## Types of Thread Pools

### 1. Fixed Thread Pool
```
pool = FixedThreadPool(size=10)
```
- Always 10 threads
- Simple and predictable
- Most common choice

### 2. Cached Thread Pool
```
pool = CachedThreadPool()
```
- Creates new threads as needed
- Reuses idle threads
- Can grow without limit (risky!)

### 3. Scheduled Thread Pool
```
pool = ScheduledThreadPool(size=5)
pool.schedule(task, delay=5_seconds)
pool.schedule_periodic(task, period=1_minute)
```
- For tasks that run after delay or repeatedly

### 4. Single Thread Executor
```
pool = SingleThreadPool()
```
- Only 1 thread
- Tasks run sequentially
- Useful for ordered processing

## Common Patterns

### Submit and Forget
```
pool.submit(send_email_task)
pool.submit(update_database_task)
// Continue without waiting for results
```

### Submit and Wait for Result
```
future1 = pool.submit(calculate_task1)
future2 = pool.submit(calculate_task2)

result1 = future1.get()  // Wait for completion
result2 = future2.get()
```

### Batch Processing
```
for item in large_dataset:
    pool.submit(process_item, item)

pool.shutdown()
pool.wait_for_all_tasks()
```

## Benefits

✅ **Reuse threads** - No create/destroy overhead

✅ **Bounded resources** - Limit concurrent threads

✅ **Queue management** - Handles task queuing for you

✅ **Simpler code** - Don't manage threads manually

✅ **Better performance** - Less overhead than creating threads per task

## Common Pitfalls

⚠️ **Too many threads**: More threads ≠ faster. Causes context switching overhead.
- Solution: Size pool appropriately

⚠️ **Too few threads**: Pool becomes bottleneck
- Solution: Monitor queue size, adjust pool size

⚠️ **Unbounded queue**: Memory fills up with pending tasks
- Solution: Set queue limit, handle rejection

⚠️ **Not shutting down**: Threads keep program alive
- Solution: Call `shutdown()` when done

⚠️ **Blocking tasks**: One slow task blocks a thread
- Solution: Use timeouts, or separate pools for different task types

⚠️ **Shared state without synchronization**: Pool threads access shared data
- Solution: Use thread-safe data structures or locks

## Best Practices

✅ **Start conservative**: Begin with small pool, increase if needed

✅ **Separate pools**: Different pools for different task types (CPU vs I/O)

✅ **Set queue limits**: Prevent memory overflow

✅ **Use timeouts**: Don't let tasks run forever

✅ **Handle rejections**: What happens when queue is full?

✅ **Monitor metrics**: Track queue size, active threads, completion time

✅ **Graceful shutdown**: Wait for tasks to complete

## Real-World Examples

**Web Server**:
```
request_pool = ThreadPool(size=200)

function handle_request(request):
    request_pool.submit(process_request, request)
```

**Batch Processing**:
```
worker_pool = ThreadPool(size=10)

for file in files:
    worker_pool.submit(process_file, file)
```

**Background Tasks**:
```
background_pool = ThreadPool(size=5)

background_pool.submit(send_emails)
background_pool.submit(cleanup_old_data)
background_pool.submit(generate_reports)
```

## Related Topics

- [Jobs and Workers](jobs-and-workers.md) - Thread pool implements this pattern
- [Multithreading](multithreading.md) - Understanding threads
- [Queues](queues-and-stacks.md) - Used internally by thread pools
- [Thread Safety](thread-safety.md) - Important when threads share data
