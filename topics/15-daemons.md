# Daemons

## What Is a Daemon?

A background process that runs continuously, usually without direct user interaction.

The term comes from "disk and execution monitor" - a program that monitors and manages system operations.

## Key Characteristics

1. **Runs in background** - No UI, no terminal attachment
2. **Long-lived** - Starts at boot, runs until shutdown
3. **Detached** - Continues running even if user logs out
4. **Automatic** - Starts/stops without user intervention

## Simple Analogy

Think of daemons like building maintenance staff:
- They work in the background
- You don't directly interact with them
- They keep things running smoothly
- They're always there when needed

## Common Examples

**System Daemons**:
- `sshd` - SSH server daemon (handles remote logins)
- `httpd` / `nginx` - Web server daemon
- `crond` - Task scheduler daemon
- `systemd` - System and service manager
- `dockerd` - Docker daemon

**Application Daemons**:
- Database servers (MySQL, PostgreSQL)
- Message queues (RabbitMQ, Kafka)
- Cache servers (Redis, Memcached)
- Monitoring agents

## Daemon vs Regular Process

| Aspect | Regular Process | Daemon |
|--------|----------------|--------|
| User interaction | Yes | No |
| Lifetime | Until task done | Continuous |
| Terminal | Attached | Detached |
| Parent process | Shell/user | System (PID 1) |
| Visibility | Foreground | Background |

## How Daemons Work

### Typical Daemon Structure
```
1. Start up
2. Detach from terminal
3. Close standard input/output
4. Change to root directory (/)
5. Set file permissions
6. Write PID to file
7. Enter main loop:
   while running:
       - Wait for events/requests
       - Process them
       - Log activities
8. Cleanup on shutdown
```

### Example Pseudocode
```
function become_daemon():
    // Fork to background
    if fork() != 0:
        parent_exit()  // Parent exits

    // Child becomes session leader
    create_new_session()

    // Change to root directory
    change_directory("/")

    // Close standard file descriptors
    close(stdin, stdout, stderr)

    // Redirect to /dev/null
    redirect_to_null()

    // Write PID file
    write_pid("/var/run/mydaemon.pid")

function main_loop():
    while not shutdown_signal:
        event = wait_for_event()
        process(event)
        log_activity()
```

## Starting/Stopping Daemons

### Linux (systemd)
```bash
# Start daemon
sudo systemctl start mydaemon

# Stop daemon
sudo systemctl stop mydaemon

# Enable at boot
sudo systemctl enable mydaemon

# Check status
sudo systemctl status mydaemon
```

### macOS (launchd)
```bash
# Load daemon
launchctl load /Library/LaunchDaemons/com.example.mydaemon.plist

# Unload daemon
launchctl unload /Library/LaunchDaemons/com.example.mydaemon.plist
```

### Manual
```bash
# Start in background
./mydaemon &

# Or with nohup (won't stop when you logout)
nohup ./mydaemon &

# Find and stop
ps aux | grep mydaemon
kill <pid>
```

## Daemon Thread vs Daemon Process

### Daemon Thread (Programming)
A thread that runs in the background:
```
main_thread = Thread(target=main_app)
daemon_thread = Thread(target=background_task, daemon=True)

daemon_thread.start()  // Dies when main thread exits
main_thread.start()
```

**Key difference**: Daemon thread dies when main program exits.

### Daemon Process (OS)
A process managed by the operating system that runs independently.

## Common Daemon Tasks

1. **Serving requests** (web server, database)
2. **Monitoring** (system health, log files)
3. **Scheduling** (running tasks at specific times)
4. **Processing queues** (background jobs)
5. **Maintaining state** (cache, sessions)
6. **Syncing data** (backups, replication)

## Best Practices

✅ **Proper logging**: Write to log files (can't use stdout/stderr)

✅ **PID file**: Write process ID to file for management

✅ **Signal handling**: Respond to SIGTERM (shutdown), SIGHUP (reload config)

✅ **Resource cleanup**: Close files, connections properly on shutdown

✅ **Error handling**: Don't crash - log errors and continue

✅ **Configuration reload**: Support reloading config without restart (SIGHUP)

✅ **Health checks**: Provide way to check if daemon is healthy

## Common Pitfalls

⚠️ **Not detaching properly**: Daemon dies when terminal closes
- Solution: Proper daemonization or use process manager

⚠️ **No logging**: Can't debug issues
- Solution: Write comprehensive logs to file

⚠️ **Resource leaks**: Files/connections not closed
- Solution: Proper cleanup in signal handlers

⚠️ **No graceful shutdown**: Killed abruptly, data lost
- Solution: Handle SIGTERM properly

⚠️ **Running as root unnecessarily**: Security risk
- Solution: Drop privileges after startup

## Creating a Simple Daemon

### Python Example
```python
import daemon
import logging

def do_work():
    logging.info("Daemon is working")
    # Your daemon logic here

with daemon.DaemonContext():
    logging.basicConfig(filename='/var/log/mydaemon.log')
    while True:
        do_work()
        time.sleep(60)  # Work every minute
```

### Node.js Example
```javascript
// Use process manager like pm2
// pm2 start daemon.js

setInterval(() => {
    console.log('Daemon is working');
    // Your daemon logic
}, 60000);  // Every minute
```

## Modern Approaches

Instead of writing daemonization code yourself, use:

1. **Process Managers**:
   - `systemd` (Linux)
   - `launchd` (macOS)
   - `pm2` (Node.js)
   - `supervisor` (Python)

2. **Containers**:
   - Docker containers run as daemons
   - Managed by container runtime

3. **Cloud Services**:
   - Serverless functions
   - Managed services

## Related Topics

- [Background Processes](14-background-processes.md) - Related concept
- [Services](16-services.md) - Often implemented as daemons
- [Task Scheduling](13-task-scheduling.md) - What daemons often do
