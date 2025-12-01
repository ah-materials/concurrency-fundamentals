# Services

## What Is a Service?

A long-running background process that provides functionality to other programs or users, typically managed by the operating system.

## Services vs Other Concepts

| Concept | Description |
|---------|-------------|
| **Service** | OS-managed background process |
| **Daemon** | Unix/Linux term for service |
| **Background Process** | Any process running in background |

On Linux/Unix: Service H Daemon
On Windows: Service is specific type of background process

## Common Examples

**System Services**:
- Web servers (Apache, Nginx)
- Databases (MySQL, PostgreSQL)
- SSH server
- DNS server

**Application Services**:
- Docker daemon
- Message queues
- Cache servers
- Monitoring agents

## Key Characteristics

1. **Starts automatically** - Usually at system boot
2. **Runs continuously** - Doesn't exit until stopped
3. **OS-managed** - System manages lifecycle
4. **Recoverable** - Can restart on failure
5. **No UI** - Headless

## Managing Services

### Linux (systemd)
```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl status nginx
```

### Windows
```powershell
Start-Service -Name "ServiceName"
Stop-Service -Name "ServiceName"
Get-Service -Name "ServiceName"
```

## Best Practices

 **Logging**: Write to system logs
 **Error handling**: Handle errors gracefully
 **Graceful shutdown**: Clean up on stop
 **Run as non-root**: Use service account

## Related Topics

- [Daemons](daemons.md) - Unix/Linux services
- [Background Processes](background-processes.md) - General background execution
