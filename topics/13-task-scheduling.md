# Task Scheduling

## What Is Task Scheduling?

Determining when and in what order tasks should be executed.

## Why Schedule Tasks?

1. **Run at specific times** - Send reports at midnight
2. **Periodic execution** - Backups every hour
3. **Fair resource allocation** - Each task gets CPU time
4. **Priority handling** - Important tasks first
5. **Optimize throughput** - Maximize work completed

## Types of Scheduling

### 1. Time-Based Scheduling

**Run at specific time**:
```
Run backup at 2:00 AM daily
Send email at 9:00 AM weekdays
```

**Periodic/recurring**:
```
Every 5 minutes: Check for new messages
Every hour: Clean up temp files
Every day: Generate reports
```

**Tools**: cron (Linux), Task Scheduler (Windows), setTimeout (programming)

### 2. Priority-Based Scheduling

Tasks with higher priority run first:
```
Priority 10: User-facing requests (handle immediately)
Priority 5:  Background processing
Priority 1:  Cleanup tasks (when idle)
```

### 3. Fair Scheduling (Round-Robin)

Each task gets equal CPU time:
```
Task A: 100ms → Task B: 100ms → Task C: 100ms → repeat
```

### 4. FIFO (First-In-First-Out)

Tasks processed in order received:
```
Queue: [Task1, Task2, Task3]
Process Task1, then Task2, then Task3
```

## Common Scheduling Tools

### Cron (Linux/Mac)
```bash
# Crontab format: minute hour day month weekday command
# Run every day at 2 AM
0 2 * * * /path/to/backup.sh

# Run every 5 minutes
*/5 * * * * /path/to/check.sh

# Run Mon-Fri at 9 AM
0 9 * * 1-5 /path/to/report.sh
```

### Task Scheduler (Windows)
```powershell
# Create scheduled task
$action = New-ScheduledTaskAction -Execute "script.exe"
$trigger = New-ScheduledTaskTrigger -Daily -At 2am
Register-ScheduledTask -TaskName "Backup" -Action $action -Trigger $trigger
```

### Programmatic Scheduling
```python
# Python with schedule library
import schedule

schedule.every().day.at("02:00").do(backup)
schedule.every(5).minutes.do(check_status)
schedule.every().monday.at("09:00").do(send_report)

while True:
    schedule.run_pending()
    time.sleep(60)
```

## Thread/Task Scheduling

### Preemptive Scheduling
OS can interrupt running thread:
```
Thread A running → Timer interrupt → OS switches to Thread B
```

### Cooperative Scheduling
Thread yields control voluntarily:
```
Thread A: do_work(), then yield()
Thread B: gets chance to run
```

## Scheduling Algorithms

### FCFS (First-Come-First-Served)
Simple, fair, but no priorities

### Round-Robin
Each task gets time slice, then moves to back of queue

### Priority Scheduling
Higher priority tasks run first

### Shortest Job First
Short tasks complete before long tasks

## Best Practices

✅ **Use existing tools** - cron, Task Scheduler, libraries
✅ **Handle failures** - Retry failed scheduled tasks
✅ **Don't overlap** - Ensure previous run completes before next starts
✅ **Monitor execution** - Log when tasks run and if they succeed
✅ **Timezone aware** - Be explicit about timezones

## Common Pitfalls

⚠️ **Tasks overlap**: Previous instance still running when next starts
⚠️ **No error handling**: Failed task just disappears
⚠️ **Timezone confusion**: Especially with DST
⚠️ **Resource contention**: Too many tasks scheduled at same time

## Related Topics

- [Jobs and Workers](11-jobs-and-workers.md) - What gets scheduled
- [Thread Pools](12-thread-pools.md) - How tasks are executed
- [Daemons](15-daemons.md) - Long-running processes that schedule tasks
