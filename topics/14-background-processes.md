# Background Processes

## What Is a Background Process?

A process that runs without direct user interaction, typically while the user works on other tasks.

## Foreground vs Background

**Foreground Process**:
- Attached to terminal/UI
- Receives user input
- Blocks terminal until complete
- Example: Text editor you're actively using

**Background Process**:
- Runs independently
- No direct user interaction
- Terminal/UI remains available
- Example: File download, data sync

## Visual

```
Terminal
┌──────────────────────────────────┐
│ $ long_running_task &            │ ← & runs in background
│ [1] 12345                        │ ← Process ID
│ $                                │ ← Terminal available
│                                  │
│ (task running in background...)  │
└──────────────────────────────────┘
```

## How to Run in Background

### Unix/Linux/Mac
```bash
# Start in background with &
./script.sh &

# Or start normally, then suspend and background
./script.sh
# Press Ctrl+Z (suspend)
bg  # Continue in background

# Bring back to foreground
fg

# Persist after logout
nohup ./script.sh &
```

### Windows
```cmd
# Start process
start /B script.exe

# Or use Task Scheduler for persistent background tasks
```

## Use Cases

1. **Long-running computations**
   - Data processing
   - Video encoding
   - Scientific simulations

2. **File operations**
   - Large downloads
   - Backups
   - File synchronization

3. **Monitoring tasks**
   - Watch for file changes
   - Monitor system health
   - Log aggregation

4. **Periodic tasks**
   - Scheduled jobs
   - Cleanup operations
   - Data synchronization

## Programmatic Background Processing

### Threading Example
```
main_thread():
    background_thread = create_thread(background_task)
    background_thread.start()  // Runs in background
    continue_with_main_work()  // Main thread continues

background_task():
    while running:
        do_work()
        sleep(interval)
```

### Process Example
```
parent_process():
    child_pid = fork()
    if child_pid == 0:
        // Child process - do background work
        background_task()
        exit()
    else:
        // Parent continues
        continue_main_work()
```

### Async Example (JavaScript/Python)
```javascript
// Fire and forget
async function backgroundTask() {
    await longRunningOperation();
}

backgroundTask();  // Runs in background
continueMainWork();  // Doesn't wait
```

## Background Task Patterns

### Pattern 1: Fire and Forget
```
submit_task(send_email)
// Don't wait for result, continue immediately
```

### Pattern 2: Deferred Processing
```
queue.add(process_upload)
// Worker processes queue in background
```

### Pattern 3: Periodic Background Work
```
schedule_recurring(cleanup_task, every=1_hour)
```

### Pattern 4: Event-Driven Background
```
on_file_change(event):
    background_process(event.file)
```

## Managing Background Processes

### List Running Background Jobs
```bash
# Unix
jobs

# Show all processes
ps aux

# Find specific process
pgrep process_name
```

### Stop Background Process
```bash
# Kill by job number
kill %1

# Kill by process ID
kill 12345

# Force kill
kill -9 12345
```

### Monitor Background Process
```bash
# Watch output (if redirected to file)
tail -f output.log

# Check if still running
ps -p 12345

# Resource usage
top -p 12345
```

## Best Practices

✅ **Log output** - Redirect stdout/stderr to files

✅ **Handle errors** - Don't silently fail in background

✅ **Provide status** - Way to check progress

✅ **Graceful shutdown** - Handle stop signals properly

✅ **Resource limits** - Don't consume all CPU/memory

✅ **Timeout long tasks** - Don't run forever

## Common Pitfalls

⚠️ **Not redirecting output**: Breaks if terminal closes
```bash
# Wrong
./script.sh &

# Right
./script.sh > output.log 2>&1 &
```

⚠️ **Process dies when shell exits**
```bash
# Use nohup or disown
nohup ./script.sh &
# Or
./script.sh &
disown
```

⚠️ **No way to monitor progress**: Can't tell if stuck

⚠️ **Resource hogging**: Background task slows system

⚠️ **No error handling**: Task fails silently

## Background vs Daemon

**Background Process**:
- Started by user
- Typically temporary
- Dies when parent dies (unless nohup)

**Daemon**:
- System service
- Starts at boot
- Runs continuously
- Independent of user sessions

## Related Topics

- [Daemons](daemons.md) - Long-running background services
- [Services](services.md) - System-level background processes
- [Jobs and Workers](jobs-and-workers.md) - Task processing pattern
- [Thread Pools](thread-pools.md) - Background thread management
